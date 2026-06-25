# MA TRẬN PHỤ THUỘC THAM SỐ PRMC V2.1

File cấu hình `config.json` chứa tất cả tham số. Tuy nhiên, các tham số này KHÔNG ĐỘC LẬP với nhau. Dưới đây là bảng phụ thuộc bắt buộc.

---

## 1. Trường hợp thay đổi `TIMEFRAME` (Khung thời gian)
Khi bạn đổi `TIMEFRAME`, về mặt toán học thuật toán vẫn chạy, nhưng **thông số kỹ thuật (Hyper-parameters) phải được điều chỉnh** để tránh sai số Spread và nhiễu.

| Tham số bị ảnh hưởng | Tác động | Cách tính / Giá trị khuyến nghị |
| :--- | :--- | :--- |
| **`PATTERN_LENGTH`** | Khung nhỏ (M5) cần nhiều nến hơn để đủ dữ liệu. | `= 5` cho H1/H4; <br>`= 8` hoặc `= 10` cho M5/M10/M15. |
| **`HALFLIFE_BARS`** | Ảnh hưởng đến trọng số thời gian. | **Vẫn giữ 48** (không thay đổi giá trị số, vì đã tính bằng số nến). |
| **`ATR_SL_HARD_FLOOR`** | **Rất quan trọng.** Với M5, Spread lớn hơn ATR. | Nâng `HARD_FLOOR` lên **0.40** cho M5/M10, giữ **0.30** cho M15 trở lên. |
| **`SPREAD_SL_MULTIPLIER`** | Đảm bảo SL > Spread. | Giữ nguyên `3.0`. (Code sẽ tự động check). |
| **Lịch trình (Schedule)** | Bot sẽ chạy nhiều hơn. | Code logic sẽ tự động điều chỉnh dựa trên `TIMEFRAME`. |

**👉 Ví dụ cụ thể:**
*Nếu bạn đang dùng `TIMEFRAME = "H1"` và muốn chuyển sang `"M5"`:*
- Sửa `TIMEFRAME` thành `"M5"`.
- Sửa `PATTERN_LENGTH` từ `5` lên `8`.
- Sửa `ATR_SL_HARD_FLOOR` từ `0.30` lên `0.40`.
- **Giữ nguyên** các tham số còn lại.

---

## 2. Trường hợp thay đổi `PROB_BASE_TRIGGER` (Ngưỡng xác suất)
Tham số này ảnh hưởng trực tiếp đến số lượng tín hiệu (tần suất) và chất lượng.

| Tham số bị ảnh hưởng | Tác động | Khuyến nghị |
| :--- | :--- | :--- |
| **`ENTROPY_MIN_DIFF`** | Nếu tăng `TRIGGER`, nên tăng nhẹ `ENTROPY` để lọc nhiễu. | Nếu `TRIGGER` > 0.70 → nâng `ENTROPY` lên `0.25`. |
| **`MIN_KELLY_RATIO`** | Trigger cao -> ít lệnh hơn -> có thể hạ Kelly để nhận thêm lệnh. | Giữ nguyên `0.05`. |

**👉 Ví dụ cụ thể:**
*Bạn muốn tăng độ an toàn (ít tín hiệu hơn nhưng chất lượng hơn):*
- Tăng `PROB_BASE_TRIGGER` từ `0.65` lên `0.75`.
- Tăng `ENTROPY_MIN_DIFF` từ `0.20` lên `0.25`.
- Giữ nguyên các tham số khác.

---

## 3. Trường hợp thay đổi `ATR_SL_HARD_FLOOR` (Chặn dưới Stop Loss)
Tham số này quyết định SL rộng hay hẹp. Nếu sửa sai, lệnh sẽ bị Stop Out ngay lập tức.

| Tham số bị ảnh hưởng | Tác động | Khuyến nghị |
| :--- | :--- | :--- |
| **`REWARD_RISK_RATIO`** | SL rộng hơn -> Risk lớn hơn -> TP phải xa hơn để giữ R:R. | Nếu `FLOOR` tăng > 0.4, nên tăng `R:R` lên `2.5` hoặc `3.0`. |
| **`R_R_MIN_FLOOR`** | SL rộng làm giảm R:R thực tế. | Nếu `FLOOR` lớn, giảm `R_R_MIN_FLOOR` xuống `1.0` để có thể chốt lời sớm. |

**👉 Ví dụ cụ thể:**
*Bạn thấy SL vẫn bị quét do spread trên M5, muốn mở rộng SL hơn nữa:*
- Tăng `ATR_SL_HARD_FLOOR` từ `0.40` lên `0.50`.
- Tăng `REWARD_RISK_RATIO` từ `2.0` lên `2.5` (để bù đắp risk lớn hơn).
- Giảm `R_R_MIN_FLOOR` từ `1.2` xuống `1.0` (cho phép TP gần hơn nếu cần).

---

## 4. Trường hợp thay đổi `PATTERN_LENGTH` (Số nến trong mẫu)
Ảnh hưởng trực tiếp đến số lượng mẫu khớp (M).

| Tham số bị ảnh hưởng | Tác động | Khuyến nghị |
| :--- | :--- | :--- |
| **`SIMILARITY_PERCENTILE`** | Mẫu dài hơn -> khó khớp hơn -> cần giảm Percentile để có đủ mẫu. | Nếu tăng `PATTERN_LENGTH`, hãy giảm `SIMILARITY_PERCENTILE` từ `70` xuống `60`. |

**👉 Ví dụ cụ thể:**
*Bạn chuyển từ H1 (PATTERN_LENGTH=5) sang M5 (PATTERN_LENGTH=10):*
- Tăng `PATTERN_LENGTH` lên `10`.
- Giảm `SIMILARITY_PERCENTILE` từ `70` xuống `60` (để đảm bảo tìm đủ mẫu `M > 10`).

---

## 5. Bảng tổng hợp các Profile khuyến nghị (Copy-Paste vào file config)

| Profile | TIMEFRAME | PATTERN_LENGTH | ATR_SL_HARD_FLOOR | PROB_BASE_TRIGGER | SIMILARITY_PERCENTILE | REWARD_RISK_RATIO |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Chậm và Chắc (H4)** | H4 | 5 | 0.30 | 0.70 | 75 | 2.0 |
| **Cân bằng (H1)** | H1 | 5 | 0.30 | 0.65 | 70 | 2.0 |
| **Nhanh (M15)** | M15 | 6 | 0.35 | 0.65 | 65 | 2.0 |
| **Scalping (M5)** | M5 | 8 | **0.45** | 0.60 | 60 | 2.5 |

> **Lưu ý:** Các profile trên chỉ là gợi ý khởi điểm. Bạn phải điều chỉnh dựa trên spread thực tế của Exness tại thời điểm giao dịch.