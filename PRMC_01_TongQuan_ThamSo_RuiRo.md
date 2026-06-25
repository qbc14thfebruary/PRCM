# PRMC-ADAPTIVE V2.1 – PHẦN 1: TỔNG QUAN & BẢNG THAM SỐ (CÓ KIỂM SOÁT RỦI RO)
**Phiên bản:** 2.1 – Sửa lỗi metric và SL

## 1. Giới thiệu
Thuật toán PRMC-Adaptive dành cho XAUUSD, hoạt động trên khung H1 (có thể tùy chỉnh). Mục tiêu: đưa ra tín hiệu giao dịch với xác suất thắng cao dựa trên nhận dạng mẫu hình nến, kết hợp bộ lọc thanh khoản và quản lý rủi ro động.

## 2. Bảng tham số & cơ chế phòng thủ
| Tham số | Mặc định | Rủi ro nếu cố định | Cơ chế thích ứng |
| :--- | :--- | :--- | :--- |
| `TIMEFRAME` | `"H1"` | Bỏ lỡ xu hướng lớn | Kiểm tra EMA50 H4 trước khi vào lệnh |
| `PATTERN_LENGTH` | `5` | Ngắn khi sideway | Giữ cố định, tăng độ khắt khe khi ADX thấp |
| `MATCH_THRESHOLD` | Không có (sẽ tính động) | **Lỗi cũ:** dùng cosine similarity sai | **Sửa:** dùng **Euclidean distance** (xem File 2) |
| `PROBABILITY_TRIGGER` | `65%` (cơ sở) | Kích hoạt khi M quá nhỏ | Động theo M và Entropy (File 3) |
| `RETRACEMENT_LEVEL` | `50%` | Điểm vào không tối ưu | Lấy trung vị các lần hồi quy thành công (File 4) |
| `REWARD_RISK_RATIO` | `2.0` | Kỳ vọng âm nếu win rate thấp | Điều chỉnh bằng Kelly (File 3) |
| `ATR_PERIOD` | `14` | Phản ứng chậm với biến động | Dùng ATR ratio để scale SL (File 4) |

## 3. 5 lớp Checkpoint tổng thể (Cầu chì bắt buộc)
1. **Lớp 1 – Dữ liệu:** Spread > 1.5×trung bình 20 nến → WAIT.
2. **Lớp 2 – Mẫu:** Số lượng mẫu khớp M < 10 → WAIT.
3. **Lớp 3 – Xác suất:** `|P(UP)-P(DOWN)| < 20%` → WAIT.
4. **Lớp 4 – Kỳ vọng:** Kelly < 0.05 → WAIT.
5. **Lớp 5 – Vốn:** Drawdown ngày > 3% → WAIT.

**Tất cả các checkpoint này đều được code cứng trong vòng lặp chính.**