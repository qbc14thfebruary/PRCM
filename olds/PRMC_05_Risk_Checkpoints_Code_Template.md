# PRMC-ADAPTIVE V2.0 - PHẦN 5: MA TRẬN CẦU CHÌ (FUSE BREAKERS) & CODE TEMPLATE HOÀN CHỈNH

## 1. Các Checkpoint cứng (Bắt buộc phải có trong code)

| Tình huống Rủi ro | Logic xử lý (Code) | Hành động |
| :--- | :--- | :--- |
| **Spread đột biến** | `if Current_Spread > 1.5 * Avg_Spread_20: return WAIT` | Hủy lệnh, đợi nến tiếp theo. |
| **Tin tác động mạnh** | `if time_until_news(FOMC/NFP) < 15 min: return WAIT` | Tạm ngưng, không nhận lệnh mới. |
| **Thua lỗ liên tiếp** | `if Consecutive_Losses >= 4: Risk_per_Trade *= 0.5` | Giảm khối lượng 50% cho đến lệnh thắng. |
| **Drawdown ngày** | `if Daily_PnL < -3% of Equity: return WAIT` | Dừng giao dịch hoàn toàn trong ngày. |

## 2. Lịch trình cập nhật tham số (Update Cycle)
- **Real-time (mỗi nến):** Cập nhật Entry, SL, TP, ATR_Multiplier.
- **Cuối ngày (EOD):** Cập nhật MATCH_THRESHOLD, RETRACEMENT_DYNAMIC.
- **Cuối tuần (EOW):** Tính lại Win Rate trượt, Kelly, λ (Lambda).
- **Cuối tháng (EOM):** Chạy Walk-forward test, reset dữ liệu cũ hơn 2 năm.

## 3. Code Template tổng thể (Flow hoàn chỉnh)

```python
def PRMC_Adaptive_Executor(real_time_data):
    """Hàm xử lý chính cho AI Agent"""
    
    # --- LỚP 1: Bảo vệ dữ liệu ---
    if is_high_impact_news(real_time_data.time):
        return {"status": "BLOCKED", "action": "WAIT", "reason": "News Lock"}
    if real_time_data.spread > 1.5 * get_avg_spread(20):
        return {"status": "BLOCKED", "action": "WAIT", "reason": "Abnormal Spread"}
    
    # --- XỬ LÝ MÔ HÌNH LÕI ---
    # Bước 1+2 (Lấy M)
    M, matched_pool = get_pattern_matches(real_time_data) 
    
    # LỚP 2: Kiểm tra cỡ mẫu
    if M < 10:
        return {"status": "BLOCKED", "action": "WAIT", "reason": f"M={M}<10"}
    
    # Bước 3 (Lọc xu hướng)
    if is_against_ema50_htf(real_time_data):
        return {"status": "BLOCKED", "action": "WAIT", "reason": "Trend Conflict"}
    
    # Bước 4 (Xác suất & Kelly)
    probs = calculate_weighted_probability(matched_pool)
    if abs(probs['up'] - probs['down']) < 0.20:
        return {"status": "BLOCKED", "action": "WAIT", "reason": "Ambiguous Signal"}
    
    kelly = calculate_kelly()
    if kelly < 0.05:
        return {"status": "BLOCKED", "action": "WAIT", "reason": f"Kelly={kelly}<0.05"}
    
    # LỚP 5: Bảo vệ vốn
    if get_daily_drawdown() > 0.03:
        return {"status": "BLOCKED", "action": "WAIT", "reason": "Daily DD > 3%"}
    
    # --- Bước 5: Tính toán mức giá ---
    levels = calculate_adaptive_levels(probs, kelly)
    
    # --- Đầu ra chuẩn với Risk Metrics ---
    return {
        "status": "SUCCESS",
        "action": "BUY" if probs['up'] > probs['down'] else "SELL",
        "entry": levels.entry,
        "sl": levels.sl,
        "tp": levels.tp,
        "risk_metrics": {
            "sample_used": M,
            "win_prob": probs['up'],
            "kelly": kelly,
            "risk_cap": min(0.02, max(0.005, kelly * 1.5)),
            "regime": "TRENDING" if adx > 25 else "SIDEWAYS"
        }
    }