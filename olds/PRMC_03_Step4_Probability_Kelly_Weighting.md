# PRMC-ADAPTIVE V2.0 - PHẦN 3: Bộ lọc ngữ cảnh & Lọc xu hướng (Regime Filter)

*(Chứa logic xác suất có trọng số, trigger động và quản lý vốn Kelly)*

```markdown
# PRMC-ADAPTIVE V2.0 - PHẦN 3: XÁC SUẤT TRỌNG SỐ, KÍCH HOẠT & QUẢN LÝ VỐN KELLY

## Bước 4: Thống kê xác suất CÓ TRỌNG SỐ (Weighted Probability)

**4.1. Trọng số thời gian (Time Decay):**
Mẫu càng gần hiện tại càng quan trọng. 
- Tính `λ (Lambda)` dựa trên chu kỳ bán rã: Giả định xu hướng H1 sống trung bình 48 nến (~2 ngày).
`λ = ln(2) / 48`

**4.2. Hàm trọng số tổng hợp cho mẫu thứ `i`:**
`Weight_i = exp(-λ * (Current_Time - Past_Time_i)) * (Similarity_Score_i)^2`

**4.3. Xác suất điều chỉnh:**
`P(UP | M_current) = Sum(Weight_i của các mẫu có nến thứ 6 tăng) / Sum(Weight_i của tất cả mẫu)`

---

## Bước 4.4: Ngưỡng kích hoạt THÔNG MINH (Trigger & Entropy Filter)

**Yêu cầu kích hoạt (Phải thỏa cả 2 điều kiện):**

**Điều kiện 1 (Chênh lệch rõ ràng):** `|P(UP) - P(DOWN)| >= 0.20` (Tối thiểu 20%).
*(Nếu chênh lệch < 20%, tín hiệu đang ở trạng thái "Mơ hồ" - Ambiguous).*

**Điều kiện 2 (Ngưỡng động theo cỡ mẫu M):** 
`P_max >= 0.50 + (0.20 / (1 + e^(-0.05 * (M - 20))))`
- Nếu M = 10 => Ngưỡng ~ 55%.
- Nếu M = 50 => Ngưỡng ~ 65%.
- Nếu M = 100 => Ngưỡng ~ 68%.

> **🚨 CHECKPOINT (Lớp 3):** Nếu không thỏa cả 2, trả về WAIT.

---

## Bước 5.4: Quản lý vốn theo **Kelly Criterion (Kiểm soát rủi ro tối thượng)**

Trước khi tính khối lượng lệnh, cần kiểm tra lợi thế thống kê:

**Ước tính Win Rate (WR) trượt:** Dựa trên 100 lệnh gần nhất (mặc định 45% nếu chưa đủ dữ liệu).
**Tính Kelly Fraction:** `Kelly = WR - ((1 - WR) / R:R_Ratio)`

**Quy tắc áp dụng:**
- Nếu `Kelly < 0.05`: **Không đặt lệnh** (Không có lợi thế).
- Nếu `Kelly >= 0.05`: `Risk_per_Trade = Kelly * 1.5` (Giới hạn từ 0.5% đến 2% tài khoản).

> **🚨 CHECKPOINT (Lớp 4):** Nếu Kelly < 0.05 -> Trả về WAIT.