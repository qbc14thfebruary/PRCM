# PRMC-ADAPTIVE V2.1 – PHẦN 2: SỐ HÓA, KHỚP MẪU & BỘ LỌC (SỬA METRIC)

## Bước 1: Số hóa chuỗi nến
Với mỗi nến thứ `n` (1→PATTERN_LENGTH), ta tạo vector:
V_n = [D_n, S_n, P_n]
Trong đó:
- `D_n` : +1 (tăng), -1 (giảm), 0 (doji)
- `S_n` : 0 (Small), 1 (Medium), 2 (Large) – dựa trên độ dài thân so với trung bình 20 nến
- `P_n` : 0 (Inside Bar), 1 (HH/HL), 2 (LH/LL) – so sánh với nến trước

Ma trận hiện tại: `M_current = [V_1, V_2, ..., V_k]`.

## Bước 2: Quét lịch sử và **tính độ tương đồng bằng EUCLIDEAN DISTANCE** (Sửa lỗi cosine)

**Lý do sửa:** Cosine similarity chỉ đo góc, bỏ qua biên độ (ví dụ nến 10 pips và 100 pips cùng hướng sẽ có cos=1). Điều này không chấp nhận được trong Price Action. Do đó ta dùng **khoảng cách Euclid** (có xét đến độ lớn).

**Công thức khoảng cách Euclid chuẩn hóa:**

Cho hai vector (ma trận) `M_current` và `M_past` (có cùng số nến), ta tính:
dist = sqrt( sum_{n=1..k} (D_n^cur - D_n^past)^2

(S_n^cur - S_n^past)^2

(P_n^cur - P_n^past)^2 )
Vì các thành phần đều là số nguyên (hoặc đã mã hóa), khoảng cách tối thiểu là 0 (giống hệt), tối đa là sqrt(3*k). Với k=5, max ≈ 3.87.

**Chuyển khoảng cách thành độ tương đồng (Similarity):**
Similarity = 1 - (dist / max_dist)
Giá trị này nằm trong [0,1].

**Ngưỡng khớp động (Thay vì 90% cứng):**
all_sims = [Similarity(M_current, M_past) for each M_past in history]
dynamic_threshold = np.percentile(all_sims, 70) # lấy 30% tốt nhất
MATCH_THRESHOLD = np.clip(dynamic_threshold, 0.60, 0.95) # chặn dưới 0.60
*Với ngưỡng 0.60, ta vẫn đảm bảo các mẫu khá giống nhau.*

**Lọc danh sách khớp:**
matched_pool = [m for m in history if Similarity(M_current, m) >= MATCH_THRESHOLD]
M = len(matched_pool)
if M < 10: return WAIT

## Bước 3: Bộ lọc ngữ cảnh (Session + SMC + Regime)

- **Session:** Chỉ giữ các mẫu cùng phiên giao dịch (Á – Âu – Mỹ) với thời điểm hiện tại.
- **SMC:** Kiểm tra vùng cung/cầu hoặc sweep thanh khoản (có thể dùng logic xác định vùng giá).
- **Regime (Xu hướng):**
  - Tính ADX(14) trên H1.
  - Nếu ADX < 20 (sideway): tăng thêm `0.02` vào `MATCH_THRESHOLD` để lọc nhiễu.
  - Nếu ADX > 40 (trend mạnh): cho phép bỏ qua điều kiện SMC (vì thường breakout).
- **EMA50 H4:** Kiểm tra xu hướng khung lớn. Nếu giá hiện tại < EMA50 H4 thì lệnh BUY bị vô hiệu; ngược lại SELL bị vô hiệu.

**Kết luận File 2:** Đã thay cosine bằng Euclidean distance, đảm bảo biên độ được tính đến.