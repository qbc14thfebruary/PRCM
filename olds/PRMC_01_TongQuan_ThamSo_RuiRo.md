# PRMC-ADAPTIVE V2.0 - PHẦN 1: TỔNG QUAN & BẢNG THAM SỐ ĐẦU VÀO (CÓ KIỂM SOÁT RỦI RO)

## 1. Giới thiệu
Đây là module nền tảng của thuật toán PRMC-Adaptive dành cho XAUUSD. 
**Nguyên lý cốt lõi:** Không có tham số nào là bất biến. Hệ thống tự động điều chỉnh dựa trên biến động (Volatility) và chu kỳ thị trường (Regime) để tránh Overfitting.

## 2. Bảng tham số & Cơ chế phòng thủ (Risk Guard)
| Mã Tham Số | Giá trị Mặc định | **Nguy cơ rủi ro khi cố định** | **Cơ chế thích ứng (Sẽ code ở File 2-4)** |
| :--- | :--- | :--- | :--- |
| `TIMEFRAME` | `"H1"` | Bỏ lỡ xu hướng khung D1/H4. | **Checkpoint:** Kiểm tra EMA 50 khung H4 trước khi vào lệnh. |
| `PATTERN_LENGTH` | `5` | Quá ngắn để lọc nhiễu khi Sideway. | Cố định, nhưng bắt buộc kiểm tra ADX để tăng độ khắt khe. |
| `MATCH_THRESHOLD` | `90%` | **Rủi ro cao nhất:** Thiếu mẫu khi sideway, thừa nhiễu khi trending. | **Động theo Phân vị 70%** (Xem File 2). |
| `PROBABILITY_TRIGGER` | `65%` | Kích hoạt tín hiệu rác khi số mẫu (M) quá nhỏ. | **Động theo Cỡ mẫu M** (Xem File 3). |
| `RETRACEMENT_LEVEL` | `50%` | Điểm vào không tối ưu theo từng phase. | **Động theo trung vị lệnh thắng** (Xem File 4). |
| `REWARD_RISK_RATIO` | `2.0` | Lợi thế âm nếu Win Rate < 40%. | **Điều chỉnh bằng Kelly Criterion** (Xem File 3). |
| `ATR_PERIOD` | `14` | Phản ứng chậm với biến động tin tức. | Dùng thêm **ATR Ratio** để scale Stop Loss (Xem File 4). |

## 3. Cấu trúc Checkpoint tổng thể (Cầu chì)
Hệ thống sẽ vận hành tuần tự qua 5 lớp Checkpoint. Nếu 1 lớp bị vi phạm => Trả về `"action": "WAIT"` ngay lập tức.
1. **Lớp 1 (Dữ liệu):** Spread đột biến, tin tức sắp ra.
2. **Lớp 2 (Mẫu):** Số mẫu khớp M < 10.
3. **Lớp 3 (Xác suất):** Chênh lệch P(UP) - P(DN) < 20%.
4. **Lớp 4 (Kỳ vọng):** Kelly Ratio < 0.05 (Không có lợi thế).
5. **Lớp 5 (Vốn):** Drawdown ngày vượt -3% tài khoản.