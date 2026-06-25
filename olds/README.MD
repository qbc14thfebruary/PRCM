# 🤖 PRMC-ADAPTIVE V2.1 – BOT TÍN HIỆU XAUUSD

**Phiên bản:** 2.1 (Sửa lỗi metric + SL)  
**Mục đích:** Cung cấp tín hiệu giao dịch BUY/SELL kèm Entry/SL/TP cho vàng (XAUUSD) trên khung H1 (có thể tùy chỉnh).  
**Tính năng:** Chỉ hiển thị tín hiệu – KHÔNG tự động đặt lệnh. Bạn là người quyết định có vào lệnh hay không.

---

## 📋 YÊU CẦU HỆ THỐNG

| Thành phần | Yêu cầu |
| :--- | :--- |
| **Hệ điều hành** | Windows (bắt buộc đối với MetaTrader 5 Python) |
| **Python** | 3.8 trở lên |
| **MetaTrader 5** | Đã cài đặt và đăng nhập tài khoản Exness |
| **Internet** | Ổn định để truy cập dữ liệu |
| **Tài khoản** | Exness (Live hoặc Demo) – đã nạp tiền hoặc demo |

> ⚠️ **Lưu ý quan trọng:** MetaTrader5 Python **chỉ chạy trên Windows**. Nếu bạn dùng Mac/Linux, bạn cần cài Windows qua Bootcamp, hoặc thuê VPS Windows (khoảng 100.000đ/tháng) để chạy bot 24/7.

---

## 🔧 BƯỚC 1: CÀI ĐẶT MÔI TRƯỜNG PYTHON

### 1.1. Kiểm tra Python
Mở Command Prompt (CMD) và gõ:
```bash
python --version
Nếu chưa có, tải Python tại python.org.
Khi cài: nhớ tick chọn "Add Python to PATH".

1.2. Tạo thư mục dự án
Tạo một thư mục mới, ví dụ: D:\PRMC_Bot\

bash
mkdir D:\PRMC_Bot
cd D:\PRMC_Bot
1.3. Tạo môi trường ảo (Virtual Environment)
bash
python -m venv venv
Sau đó kích hoạt:

bash
venv\Scripts\activate
(Dấu nhắc lệnh sẽ hiện (venv) phía trước, báo hiệu đang ở trong môi trường ảo).

1.4. Cài các thư viện cần thiết
Trong môi trường ảo, gõ lần lượt:

bash
pip install MetaTrader5
pip install pandas
pip install numpy
pip install python-dotenv
pip install schedule
Giải thích:

MetaTrader5: thư viện chính để kết nối với MT5, lấy dữ liệu.

pandas, numpy: xử lý dữ liệu dạng bảng, tính toán.

python-dotenv: đọc biến môi trường từ file .env (không bắt buộc với MT5).

schedule: lập lịch chạy bot đúng thời điểm nến đóng.

🔑 BƯỚC 2: KẾT NỐI VỚI EXNESS + MT5
2.1. Đăng nhập MT5
Mở phần mềm MetaTrader 5 trên máy tính.

Đăng nhập vào tài khoản Exness (Live hoặc Demo).

Đảm bảo bạn nhìn thấy giá vàng (XAUUSD) trong bảng Market Watch. Nếu chưa có, bấm chuột phải → Symbols → tìm XAUUSD → Show.

2.2. Kiểm tra tên Symbol chính xác
Trên Exness, vàng thường có các tên:

Tài khoản	Ký hiệu
Standard / Raw Spread	XAUUSD
Pro (MT5)	XAUUSDm
Zero	XAUUSD
Mở MT5, xem tên chính xác trong Market Watch và ghi nhớ. Bạn sẽ cần dùng tên này trong file cấu hình.

⚙️ BƯỚC 3: CẤU HÌNH BOT
3.1. Tạo file cấu hình .env (không bắt buộc với MT5, nhưng khuyến nghị)
Tạo file .env trong thư mục dự án:

text
TIMEFRAME=H1
SYMBOL=XAUUSD
MATCH_THRESHOLD_DYNAMIC=TRUE
ATR_PERIOD=14
Lưu ý: Với MT5, bạn không cần API key. Chỉ cần MT5 đã đăng nhập.

3.2. Tạo file config.py (hoặc khai báo trực tiếp trong signal_bot.py)
Nếu bạn không dùng .env, hãy khai báo trực tiếp trong code:

python
TIMEFRAME = "H1"           # H1, M15, H4
SYMBOL = "XAUUSD"          # hoặc XAUUSDm
📄 BƯỚC 4: TẠO FILE BOT CHÍNH signal_bot.py
Sao chép toàn bộ code mẫu từ Phần 5 (Code Template) trong file PRMC_05_Risk_Checkpoints_Code_Template.md vào file signal_bot.py.
(Đảm bảo không thừa dấu cách, không lỗi indent).

Cấu trúc thư mục dự án sau khi tạo:

text
D:\PRMC_Bot\
├── venv\                   (thư mục môi trường ảo)
├── signal_bot.py           (file chính)
├── .env                    (file cấu hình, nếu có)
├── signal_log.json         (sẽ tự sinh khi chạy – lưu tín hiệu)
└── README.md               (file này)
🚀 BƯỚC 5: CHẠY BOT
5.1. Mở Command Prompt với quyền Administrator (khuyến nghị)
Bấm chuột phải vào CMD → Run as administrator.

Di chuyển đến thư mục dự án:

bash
cd D:\PRMC_Bot
5.2. Kích hoạt môi trường ảo
bash
venv\Scripts\activate
5.3. Chạy bot
bash
python signal_bot.py
5.4. Kết quả mong đợi
Màn hình sẽ hiển thị:

text
🟢 Bot tín hiệu PRMC đã khởi động!
⏳ Khung thời gian: H1. Bot sẽ chạy khi nến đóng.
👉 Hãy để cửa sổ Terminal này mở. Khi có tín hiệu, nó sẽ in ra cho bạn.
Khi có tín hiệu, bạn sẽ thấy:

text
--------------------------------------------------
📊 TÍN HIỆU PRMC - XAUUSD - H1
📅 Thời điểm tính: 2026-06-25 15:00:05
📈 Xu hướng 5 nến gần nhất: DOWN
🎯 Hành động đề xuất: BUY
✅ Entry (Giá vào): 2348.50
🛑 Stop Loss (Cắt lỗ): 2342.20
🎯 Take Profit (Chốt lời): 2361.10
📏 ATR (14) hiện tại: 12.5
--------------------------------------------------
Bạn tự quyết định có đặt lệnh theo các mức giá này hay không.

🛑 BƯỚC 6: DỪNG BOT
Nhấn tổ hợp phím Ctrl + C trong cửa sổ CMD để dừng bot.
Bot sẽ tự động lưu log vào signal_log.json.

📊 KIỂM TRA LỊCH SỬ TÍN HIỆU
File signal_log.json được tạo tự động. Mỗi dòng là một tín hiệu đã phát sinh:

json
{"timestamp": "2026-06-25T15:00:05", "timeframe": "H1", "signal": {"action": "BUY", "entry": 2348.50, "sl": 2342.20, "tp": 2361.10}}