# PRMC-ADAPTIVE V2.0 - PHẦN 4: LOGIC VÀO LỆNH, STOP LOSS VÀ TAKE PROFIT THÍCH ỨNG

## Bước 5.1: Giá vào lệnh (Entry Price) - Học từ lịch sử

Thay vì hồi quy 50% cố định, lấy mức hồi quy trung bình của các lệnh **đã thắng** trong 100 lần trước:
`RETRACEMENT_DYNAMIC = Percentile_40 (Retracement của các lệnh Win)`

**Tính Entry cho lệnh BUY:**
`Entry_Price = Open_{nến thứ 6} - (Biên_độ_nến_thứ_5 * RETRACEMENT_DYNAMIC)`

**Tính Entry cho lệnh SELL:**
`Entry_Price = Open_{nến thứ 6} + (Biên_độ_nến_thứ_5 * RETRACEMENT_DYNAMIC)`

*(Đảm bảo Entry nằm trong khoảng an toàn: Không thấp hơn 0.1*ATR so với đáy, không cao hơn 0.1*ATR so với đỉnh).*

---

## Bước 5.2: Stop Loss (SL) - Mở rộng theo biến động (Volatility)

**Hệ số điều chỉnh ATR (Động):**
`Volatility_Ratio = ATR_current / MA_ATR_50`
`ATR_Multiplier = Clip(0.15 + 0.25 * Volatility_Ratio, 0.10, 0.50)`

**Công thức:**
- **Lệnh BUY:** `SL_Price = Min(Low_1..Low_5) - (ATR_Multiplier * ATR_14)`
- **Lệnh SELL:** `SL_Price = Max(High_1..High_5) + (ATR_Multiplier * ATR_14)`

> *(Khi thị trường biến động mạnh, SL tự động nới rộng để tránh bị quét râu; khi biến động thấp, SL thu hẹp để tối ưu R:R).*

---

## Bước 5.3: Take Profit (TP) - Chốt lời tại vùng kháng cự/hỗ trợ

TP không cố định là 2.0R nữa. Hệ thống tìm vùng cản gần nhất:

1. Tìm vùng kháng cự gần nhất trong 50 nến trước (`Resistance`).
2. Tìm vùng hỗ trợ gần nhất trong 50 nến trước (`Support`).

**Công thức BUY:** `TP_Price = Min(Entry + (2.0 * Delta_Risk), Resistance - 0.1*ATR)`
**Công thức SELL:** `TP_Price = Max(Entry - (2.0 * Delta_Risk), Support + 0.1*ATR)`

**Luật bảo vệ (R:R Floor):**
Nếu khoảng cách đến vùng cản khiến R:R thực tế < 1.2, hệ thống sẽ **bỏ qua vùng cản** và đặt TP = 1.2 * Delta_Risk để đảm bảo bù đắp rủi ro tối thiểu.