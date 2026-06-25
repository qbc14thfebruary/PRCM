# HƯỚNG DẪN CẤU HÌNH `config.json`

## 1. Nhóm THAM_SO_BAT_BUOC (Bắt buộc, không có giá trị mặc định)

| Tên tham số | Kiểu | Mô tả | Ví dụ |
| :--- | :--- | :--- | :--- |
| `TIMEFRAME` | String | Khung thời gian giao dịch. Bot sẽ chạy đúng lúc nến này đóng. | `"M5"`, `"M15"`, `"H1"`, `"H4"` |
| `SYMBOL` | String | Tên cặp tiền tệ trên MT5. Exness thường dùng `XAUUSD` hoặc `XAUUSDm`. | `"XAUUSD"` |
| `PATTERN_LENGTH` | Integer | Số lượng nến tạo thành 1 mẫu để quét. | `5` (H1), `8` (M5) |
| `HISTORY_RANGE_BARS` | Integer | Số cây nến lịch sử tối đa để quét tìm mẫu. | `500` (khoảng 20 ngày H1). |

---

## 2. Nhóm THAM_SO_MATCHING (Độ tương đồng)

| Tên tham số | Kiểu | Mô tả | Gợi ý |
| :--- | :--- | :--- | :--- |
| `SIMILARITY_PERCENTILE` | Integer (0-100) | Lấy phần trăm mẫu có độ tương đồng cao nhất. 70 nghĩa là chỉ lấy 30% mẫu tốt nhất. | `60` (M5), `70` (H1), `75` (H4) |
| `SIMILARITY_MIN_CLIP` | Float (0-1) | Chặn dưới của ngưỡng tương đồng. Không cho phép xuống dưới 0.6 (kém chất lượng). | `0.60` |
| `SIMILARITY_MAX_CLIP` | Float (0-1) | Chặn trên của ngưỡng tương đồng. Không cho phép quá 0.95 (tránh khớp hoàn hảo -> overfit). | `0.95` |

---

## 3. Nhóm THAM_SO_XAC_SUAT (Xác suất & Entropy)

| Tên tham số | Kiểu | Mô tả | Gợi ý |
| :--- | :--- | :--- | :--- |
| `PROB_BASE_TRIGGER` | Float (0-1) | Ngưỡng xác suất cơ sở. Hệ thống sẽ tự động tăng/giảm dựa trên số mẫu M. | `0.65` |
| `ENTROPY_MIN_DIFF` | Float (0-1) | Chênh lệch tối thiểu giữa P(UP) và P(DOWN) để tránh tín hiệu mơ hồ. | `0.20` |
| `HALFLIFE_BARS` | Integer | Số nến để trọng số giảm đi một nửa. 48 nghĩa là mẫu 48 nến trước chỉ còn 50% trọng số. | `48` |

---

## 4. Nhóm THAM_SO_VON_KY_VONG (Quản lý vốn Kelly)

| Tên tham số | Kiểu | Mô tả | Gợi ý |
| :--- | :--- | :--- | :--- |
| `DEFAULT_WIN_RATE` | Float (0-1) | Win rate mặc định nếu chưa có lịch sử giao dịch. | `0.45` (cẩn thận) |
| `MIN_KELLY_RATIO` | Float | Nếu Kelly nhỏ hơn giá trị này, bot sẽ không ra tín hiệu. | `0.05` |
| `RISK_PER_TRADE_MIN` | Float | Tối thiểu % tài khoản rủi ro cho 1 lệnh. | `0.005` (0.5%) |
| `RISK_PER_TRADE_MAX` | Float | Tối đa % tài khoản rủi ro cho 1 lệnh. | `0.02` (2%) |

---

## 5. Nhóm THAM_SO_QUAN_LY_LENH (Entry, SL, TP)

| Tên tham số | Kiểu | Mô tả | Gợi ý |
| :--- | :--- | :--- | :--- |
| `REWARD_RISK_RATIO` | Float | Tỷ lệ lợi nhuận rủi ro mục tiêu. | `2.0` |
| `R_R_MIN_FLOOR` | Float | R:R tối thiểu cho phép. Nếu vùng cản gần hơn mức này, bot sẽ tự đặt TP theo floor. | `1.2` |
| `RETRACEMENT_PERCENTILE` | Integer | Phần trăm hồi quy lấy từ lịch sử lệnh thắng. | `40` |
| `ATR_PERIOD` | Integer | Số nến để tính ATR. | `14` |
| `ATR_SL_BASE_MULTIPLIER` | Float | Hệ số ATR cơ sở cho SL. | `0.15` |
| `ATR_SL_RANGE_MULTIPLIER` | Float | Hệ số điều chỉnh theo biến động. | `0.25` |
| `ATR_SL_HARD_FLOOR` | Float | **Chặn dưới cứng** của hệ số ATR. Không cho phép nhỏ hơn giá trị này. | `0.30` (H1), `0.45` (M5) |
| `ATR_SL_HARD_CEIL` | Float | Chặn trên cứng của hệ số ATR. | `0.80` |
| `SPREAD_SL_MULTIPLIER` | Float | SL phải lớn hơn Spread nhân với hệ số này. | `3.0` |

---

## 6. Nhóm THAM_SO_RUI_RO_HE_THONG (Cầu chì)

| Tên tham số | Kiểu | Mô tả | Gợi ý |
| :--- | :--- | :--- | :--- |
| `MAX_SPREAD_RATIO` | Float | Nếu Spread > trung bình 20 nến * tỷ lệ này, bot tạm dừng. | `1.5` |
| `MAX_DAILY_DRAWDOWN` | Float | Nếu lỗ ngày vượt quá % này, bot tạm dừng đến hết ngày. | `0.03` (3%) |
| `MAX_CONSECUTIVE_LOSSES` | Integer | Nếu thua liên tiếp đạt ngưỡng, giảm risk 50%. | `4` |
| `STOP_BEFORE_NEWS_MINUTES` | Integer | Số phút trước tin tức (FOMC/NFP) để tạm ngưng giao dịch. | `15` |