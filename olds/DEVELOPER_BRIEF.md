# HƯỚNG DẪN CHO CHUYÊN GIA PHÁT TRIỂN – PRMC-ADAPTIVE V2.1

## 1. Mục tiêu
Chuyển đổi các đặc tả trong các file `PRMC_01_*.md` đến `PRMC_05_*.md` thành một chương trình Python duy nhất (`signal_bot.py`) có thể chạy trên máy tính Windows với MetaTrader 5 đã đăng nhập Exness. Chương trình sẽ liên tục quét dữ liệu và in ra màn hình các tín hiệu giao dịch (BUY/SELL/WAIT) kèm Entry, SL, TP khi có điều kiện.

## 2. Phạm vi công việc
- Code toàn bộ logic từ Bước 1 đến Bước 5 trong các file PRMC.
- Đọc tất cả tham số từ file `config.json` (theo mẫu `config.example.json`).
- Kết nối với MT5, lấy dữ liệu giá XAUUSD (symbol có thể cấu hình).
- Thực hiện match pattern, tính xác suất, kiểm tra các checkpoint và tính Entry/SL/TP.
- In kết quả ra terminal và ghi log vào file `signal_log.json`.
- Không tự động đặt lệnh giao dịch (chỉ báo tín hiệu).

## 3. Các yêu cầu kỹ thuật cụ thể

### 3.1. Thư viện cần dùng
- `MetaTrader5`: kết nối và lấy dữ liệu OHLC.
- `pandas`, `numpy`: xử lý dữ liệu, tính toán ATR, ma trận, khoảng cách Euclid.
- `schedule` hoặc logic thời gian tự viết để chạy đúng thời điểm đóng nến.
- `json`, `os`, `datetime`, `time` (có sẵn).

### 3.2. Đọc cấu hình
- Tất cả các tham số (ví dụ TIMEFRAME, PATTERN_LENGTH, ATR_PERIOD, …) phải được đọc từ `config.json`. Không được hardcode bất kỳ số nào trong code (trừ các hằng số toán học như ln2).
- File `config.json` sẽ được cung cấp bởi người dùng (hoặc copy từ `config.example.json`).

### 3.3. Logic cốt lõi cần đảm bảo
- **Số hóa nến (Bước 1):** Mỗi nến được mã hóa thành vector (D, S, P) như mô tả.
- **Matching (Bước 2):** Sử dụng **khoảng cách Euclid chuẩn hóa** (không dùng cosine). Ngưỡng khớp được tính động dựa trên percentile (có clip min/max).
- **Bộ lọc (Bước 3):** Áp dụng Session, SMC, ADX, EMA50 H4 như mô tả.
- **Xác suất (Bước 4):** Dùng trọng số theo thời gian với Lambda được tính dựa trên `TIMEFRAME` và `HALFLIFE_BARS`. Tính P_UP và P_DN, kiểm tra điều kiện Entropy và Trigger động.
- **Kelly (Bước 4):** Tính Kelly từ win rate lịch sử (nếu có) hoặc dùng giá trị mặc định. Chặn nếu Kelly < MIN_KELLY_RATIO.
- **Entry/SL/TP (Bước 5):**
  - Entry: dùng RETRACEMENT_PERCENTILE động (lấy từ lịch sử các lệnh thắng).
  - SL: dùng ATR multiplier với **hard floor = 0.30** (có thể điều chỉnh qua config), và luôn đảm bảo khoảng cách SL > `SPREAD_SL_MULTIPLIER * spread` (spread lấy từ MT5).
  - TP: kết hợp vùng cản (Resistance/Support) và R:R floor = 1.2 (có thể cấu hình).

### 3.4. Các checkpoint (cầu chì) bắt buộc
- Spread > 1.5×trung bình → WAIT.
- Còn < 15 phút trước tin tức → WAIT.
- Số mẫu M < 10 → WAIT.
- |P(UP)-P(DN)| < 0.20 → WAIT.
- Kelly < 0.05 → WAIT.
- Drawdown ngày > 3% → WAIT.
- Lệnh thua liên tiếp ≥ 4 → giảm risk 50%.

### 3.5. Lịch trình chạy
- Xác định thời điểm đóng nến của khung `TIMEFRAME` hiện tại (hỗ trợ M5, M10, M15, H1, H4).
- Chạy hàm tính tín hiệu ngay sau khi nến đóng (chờ 2-3 giây để dữ liệu cập nhật).
- Sau khi chạy, ngủ cho đến nến tiếp theo đóng.

### 3.6. Đầu ra
- In ra màn hình thông tin tín hiệu theo định dạng như mẫu trong README.
- Ghi log JSON vào `signal_log.json`, mỗi dòng là một đối tượng JSON chứa timestamp, timeframe, signal, các chỉ số risk metrics (M, probs, kelly, …) để tiện theo dõi.

## 4. Các file có sẵn để tham khảo
| File | Mô tả |
| :--- | :--- |
| `PRMC_01_TongQuan_ThamSo_RuiRo.md` | Tổng quan, bảng tham số, checkpoint. |
| `PRMC_02_Step1_Step2_Step3_Matching_Filters.md` | Số hóa, matching Euclid, bộ lọc. |
| `PRMC_03_Step4_Probability_Kelly_Weighting.md` | Xác suất, entropy, trigger, Kelly. |
| `PRMC_04_Step5_Entry_SL_TP_Adaptive.md` | Entry, SL, TP, hard floor ATR, vùng cản. |
| `PRMC_05_Risk_Checkpoints_Code_Template.md` | Mã giả template, lịch trình, checkpoint. |
| `config.example.json` | Mẫu file cấu hình JSON. |
| `PARAMETER_DEPENDENCIES.md` | Hướng dẫn phụ thuộc tham số. |
| `CONFIG_GUIDE.md` | Giải thích từng tham số trong JSON. |
| `README.md` | Hướng dẫn cài đặt, chạy cho người dùng cuối. |

## 5. Lưu ý quan trọng khi code
- **Tuyệt đối không dùng cosine similarity.** Đã sửa thành Euclidean distance.
- **ATR multiplier luôn ≥ 0.30** (có thể điều chỉnh qua config nhưng không xuống dưới 0.20 nếu hardcode).
- **Lambda phải phụ thuộc vào TIMEFRAME** (công thức: `ln(2)/(HALFLIFE_BARS * số_phút_của_TF)`).
- **Spread phải được lấy từ MT5** và dùng để điều chỉnh SL nếu cần.
- **Mọi giá trị trả về** (entry, sl, tp) làm tròn đến 2 chữ số thập phân.
- **Không tự đặt lệnh:** chức năng chỉ là in và log tín hiệu.

## 6. Đầu ra mong đợi
Sau khi hoàn thành, `signal_bot.py` sẽ:
- Chạy vô thời hạn (cho đến khi người dùng nhấn Ctrl+C).
- Tự động xử lý các checkpoint và đưa ra tín hiệu đúng logic đã mô tả.
- Có thể chạy trên nhiều khung thời gian khác nhau chỉ bằng cách thay đổi `TIMEFRAME` trong config.

## 7. Hỗ trợ
- Trong quá trình code, nếu có điểm nào chưa rõ, có thể tham khảo file PRMC_05 (template) và các file hướng dẫn.
- Các công thức toán học đều đã được định nghĩa rõ trong các file .md.

---
**Chân thành cảm ơn chuyên gia đã dành thời gian phát triển dự án này.**