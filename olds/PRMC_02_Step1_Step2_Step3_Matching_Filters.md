# PRMC-ADAPTIVE V2.0 - PHẦN 2: LOGIC SỐ HÓA, KHỚP MẪU & BỘ LỌC NGỮ CẢNH

## Bước 1: Số hóa chuỗi nến (Vector Hóa)
Giữ nguyên logic gốc: Mỗi nến `n` (1->5) có vector `V_n = [D_n, S_n, P_n]`.
Tạo ma trận hiện tại: `M_current = [V_1, V_2, V_3, V_4, V_5]`.

## Bước 2: Quét lịch sử & **Ngưỡng khớp TỰ ĐỘNG (Dynamic Threshold)**

**Code triển khai (Python Logic):**
```python
# Giả sử có hàm cosine_similarity tính độ tương đồng
all_scores = [cosine_similarity(M_current, M_past) for M_past in HISTORY_5Y]

# 1. Ngưỡng động = Phân vị 70 (Chỉ lấy 30% mẫu đẹp nhất)
dynamic_threshold = np.percentile(all_scores, 70)

# 2. Giới hạn cứng (Hard Clip) để tránh quá khắt khe hoặc quá lỏng lẻo
MATCH_THRESHOLD = np.clip(dynamic_threshold, 0.65, 0.95)

# 3. Lọc ra danh sách mẫu khớp
matched_pool = [m for m in HISTORY if cosine_similarity(M_current, m) >= MATCH_THRESHOLD]
M = len(matched_pool)

# ⚠️ CHECKPOINT (Lớp 2): Nếu M < 10 => DỪNG NGAY, không tính toán tiếp.
if M < 10:
    return {"action": "WAIT", "reason": "M < 10 - Insufficient data"}