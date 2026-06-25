ada# PRMC-ADAPTIVE V2.1 – PHẦN 4: ENTRY, SL, TP (SỬA HARD FLOOR ATR)

## Bước 5.1: Entry (Điểm vào)
**Thay vì hồi quy 50% cố định, ta dùng trung vị hồi quy của các lệnh thắng trước đó:**
- Ghi nhận tỷ lệ hồi quy của mỗi lệnh thắng: `ret = (entry_price - low_bar) / (high_bar - low_bar)` đối với BUY; hoặc `(high_bar - entry_price) / (high_bar - low_bar)` đối với SELL.
- Sau 100 lệnh, tính `RETRACEMENT_DYNAMIC = np.percentile(list_of_ret, 40)`.

**Entry cụ thể:**
- Lệnh BUY: `Entry = Open_{nến thứ 6} - (Range_{nến thứ 5} * RETRACEMENT_DYNAMIC)`
- Lệnh SELL: `Entry = Open_{nến thứ 6} + (Range_{nến thứ 5} * RETRACEMENT_DYNAMIC)`

*Giới hạn:* Entry phải nằm trong đoạn [Low+0.1*ATR, High-0.1*ATR] để tránh vào quá sát biên.

---

## Bước 5.2: Stop Loss (SL) – **SỬA HARD FLOOR ATR MULTIPLIER**

**Vấn đề cũ:** Hệ số ATR từ 0.1 đến 0.5, với mức 0.1 quá nhỏ, dễ bị quét SL do spread và nhiễu.

**Giải pháp:** Đặt **Hard Floor (chặn dưới) = 0.3** thay vì 0.1.

**Công thức mới:**
volatility_ratio = ATR_current / MA_ATR_50
ATR_multiplier = np.clip(0.15 + 0.25 * volatility_ratio, 0.30, 0.60)
- Giá trị tối thiểu giờ là 0.30, đảm bảo SL luôn đủ rộng để tránh nhiễu spread.
- Giá trị tối đa 0.60 (nới rộng khi thị trường biến động mạnh).

**SL Price:**
- BUY: `SL = min(Low_1..Low_k) - (ATR_multiplier * ATR_14)`
- SELL: `SL = max(High_1..High_k) + (ATR_multiplier * ATR_14)`

---

## Bước 5.3: Take Profit (TP) – Kết hợp vùng cản và R:R tối thiểu

- Tìm vùng kháng cự gần nhất (Resistance) và hỗ trợ gần nhất (Support) trong 50 nến.
- TP BUY: `TP = min(Entry + 2.0*Risk, Resistance - 0.1*ATR)`
- TP SELL: `TP = max(Entry - 2.0*Risk, Support + 0.1*ATR)`
- **Đảm bảo R:R tối thiểu = 1.2**:
  - Nếu `(TP - Entry) / Risk < 1.2` với BUY (hoặc `(Entry - TP)/Risk < 1.2` với SELL), thì đặt TP tương ứng với R:R = 1.2.

**Lưu ý:** Tất cả các mức giá đều được làm tròn đến 2 chữ số thập phân.
