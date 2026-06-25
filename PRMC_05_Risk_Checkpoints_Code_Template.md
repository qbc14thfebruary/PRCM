# PRMC-ADAPTIVE V2.1 – PHẦN 5: CẦU CHÌ, CHECKPOINT & CODE TEMPLATE

## 1. Bảng checkpoint cứng (bắt buộc phải có trong code)

| Điều kiện | Hành động |
| :--- | :--- |
| Spread hiện tại > 1.5 × Spread trung bình 20 nến | WAIT |
| Còn 15 phút trước tin FOMC/NFP (hoặc các tin tác động cao) | WAIT (không nhận lệnh mới) |
| Số mẫu M < 10 | WAIT |
| |P(UP) – P(DN)| < 0.20 | WAIT |
| Kelly < 0.05 | WAIT |
| Drawdown ngày > 3% vốn | WAIT (dừng đến hết ngày) |
| Thua lỗ liên tiếp ≥ 4 | Giảm risk 50% cho đến lệnh thắng |

## 2. Chu kỳ cập nhật tham số

| Tần suất | Tham số cập nhật |
| :--- | :--- |
| Mỗi nến (real-time) | Entry, SL, TP (dựa trên ATR hiện tại) |
| Cuối ngày (EOD) | MATCH_THRESHOLD (percentile mới), RETRACEMENT_DYNAMIC |
| Cuối tuần (EOW) | Win rate trượt, Kelly, lambda |
| Cuối tháng (EOM) | Reset dữ liệu cũ hơn 2 năm (Walk-forward) |

## 3. Code template (Python) – Chỉ in tín hiệu

```python
import MetaTrader5 as mt5
import numpy as np
import pandas as pd
from datetime import datetime, timedelta
import time
import json

# --- Cấu hình ---
TIMEFRAME = "H1"           # hoặc M15, H4
PATTERN_LENGTH = 5
HISTORY_BARS = 500
ATR_PERIOD = 14

# --- Kết nối MT5 (cần đăng nhập Exness) ---
def connect_mt5():
    if not mt5.initialize():
        print("MT5 initialize failed. Please open MT5 app.")
        return False
    if mt5.account_info() is None:
        print("Please login to MT5 first.")
        mt5.shutdown()
        return False
    print("MT5 connected.")
    return True

# --- Lấy dữ liệu ---
def get_ohlc(symbol, timeframe, bars=HISTORY_BARS):
    rates = mt5.copy_rates_from_pos(symbol, timeframe, 0, bars)
    if rates is None:
        return None
    df = pd.DataFrame(rates)
    df['time'] = pd.to_datetime(df['time'], unit='s')
    return df

# --- Hàm tính ma trận và tương đồng Euclid ---
def encode_candle(open_, high, low, close, prev_open, prev_high, prev_low):
    # D_n
    if close > open_: D = 1
    elif close < open_: D = -1
    else: D = 0
    # S_n (cần lịch sử để tính trung bình – giả lập)
    # giả sử có sẵn body length so với trung bình 20
    # Ở đây ta dùng mã giả
    return D, 0, 0   # cần cài thêm

def euclidean_similarity(M1, M2):
    # M1, M2 là list các vector (D,S,P)
    k = len(M1)
    max_dist = np.sqrt(3*k)
    dist = 0
    for i in range(k):
        dist += (M1[i][0]-M2[i][0])**2 + (M1[i][1]-M2[i][1])**2 + (M1[i][2]-M2[i][2])**2
    dist = np.sqrt(dist)
    return 1 - dist/max_dist

# --- Hàm tín hiệu PRMC (skeleton) ---
def prmc_signal(df):
    # Bước 1: tạo M_current (5 nến cuối)
    # Bước 2: quét lịch sử, tính similarity, lọc M >= 10
    # Bước 3: lọc ADX, EMA50, SMC
    # Bước 4: tính P_UP, P_DN, kiểm tra threshold và Kelly
    # Bước 5: tính Entry/SL/TP
    # Trả về dict
    return {"action": "WAIT"}

# --- Hàm chính ---
def run_signal():
    symbol = "XAUUSD"
    timeframe = mt5.TIMEFRAME_H1
    df = get_ohlc(symbol, timeframe)
    if df is None:
        return
    signal = prmc_signal(df)
    # In ra
    if signal['action'] != 'WAIT':
        print(f"\n📈 SIGNAL {signal['action']} at {signal['entry']}, SL {signal['sl']}, TP {signal['tp']}")
        # Lưu log
    else:
        print(f"[{datetime.now()}] No signal.")

# --- Vòng lặp chạy theo thời gian đóng nến ---
def get_next_close(now, tf):
    # Tự viết logic
    pass

if __name__ == "__main__":
    if not connect_mt5():
        exit()
    print("Bot tín hiệu PRMC đang chạy... Nhấn Ctrl+C để dừng.")
    while True:
        now = datetime.now()
        next_close = get_next_close(now, TIMEFRAME)
        if now >= next_close:
            time.sleep(3)  # chờ dữ liệu
            run_signal()
            time.sleep(10)
        time.sleep(1)