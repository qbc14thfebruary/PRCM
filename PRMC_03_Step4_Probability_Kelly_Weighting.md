# PRMC-ADAPTIVE V2.1 – PHẦN 3: XÁC SUẤT CÓ TRỌNG SỐ, KÍCH HOẠT & KELLY

## Bước 4.1: Trọng số thời gian (Time Decay)
- Giả định chu kỳ bán rã của xu hướng H1 ≈ 48 nến (2 ngày).
- `lambda = ln(2) / 48 ≈ 0.0144`

Trọng số cho mẫu `i`:
w_i = exp(-lambda * (now - timestamp_i)) * (Similarity_i)^2
Trong đó `Similarity_i` là độ tương đồng Euclid tính ở File 2.

## Bước 4.2: Xác suất điều chỉnh
Gọi `matched_pool` là tập các mẫu khớp (sau lọc), mỗi mẫu có nến thứ `k+1` (nến thứ 6) tăng hoặc giảm.
P_UP = sum(w_i cho các mẫu có nến thứ 6 tăng) / sum(w_i)
P_DN = 1 - P_UP

## Bước 4.3: Điều kiện kích hoạt (Trigger + Entropy)

**1. Entropy (chênh lệch xác suất):**
if abs(P_UP - P_DN) < 0.20: return WAIT (tín hiệu mơ hồ)

**2. Ngưỡng xác suất tối thiểu (động theo M):**
threshold = 0.50 + 0.20 / (1 + exp(-0.05 * (M - 20)))
- M=10 → threshold≈0.55
- M=50 → threshold≈0.65
- M=100→ threshold≈0.68

**Kích hoạt khi:** `max(P_UP, P_DN) >= threshold`

## Bước 4.4: Quản lý vốn Kelly

- Win rate (WR) ước lượng từ 100 giao dịch gần nhất (nếu chưa đủ, dùng 0.45).
- R:R mục tiêu = 2.0 (có thể điều chỉnh từ tham số).
- Kelly = WR - (1-WR)/2.0
- Nếu Kelly < 0.05 → **không vào lệnh** (không có lợi thế).
- Nếu Kelly ≥ 0.05 → rủi ro mỗi lệnh = Kelly * 1.5 (giới hạn 0.5% – 2% tài khoản).

**Lưu ý:** Kelly được cập nhật cuối mỗi tuần để đảm bảo ổn định.
