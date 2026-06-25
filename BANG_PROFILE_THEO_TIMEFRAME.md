ĐÃ TẠO LẠI BẢNG PROFILE HOÀN CHỈNH & CÓ KẾT LUẬN CHI TIẾT
Dưới đây là phiên bản được tối ưu và giải thích rõ ràng dựa trên bảng bạn cung cấp:

📊 BẢNG TỔNG HỢP PROFILE THEO TIMEFRAME (ĐÃ TỐI ƯU)


# BẢNG TỔNG HỢP PROFILE THEO TIMEFRAME (ĐÃ TỐI ƯU)

**Dành cho Bot PRMC-Adaptive V2.1**  
**Cập nhật: 25/06/2026**

Dưới đây là bảng tham số được tối ưu theo từng khung thời gian. Bạn có thể copy-paste trực tiếp vào file `config.json`.

---

## 📊 BẢNG TỔNG HỢP PROFILE

| Tham số                    | **M5 (Scalping)** | **M15**          | **H1 (Cân bằng)** | **H4 (Xu hướng)** |
|---------------------------|-------------------|------------------|-------------------|-------------------|
| **PATTERN_LENGTH**        | **8**             | **6**            | **5**             | **5**             |
| **SIMILARITY_PERCENTILE** | **60**            | **65**           | **70**            | **75**            |
| **PROB_BASE_TRIGGER**     | **0.60**          | **0.62**         | **0.65**          | **0.70**          |
| **HALFLIFE_BARS**         | **24**            | **36**           | **48**            | **72**            |
| **ATR_SL_HARD_FLOOR**     | **0.45**          | **0.35**         | **0.30**          | **0.30**          |
| **REWARD_RISK_RATIO**     | **1.5**           | **1.8**          | **2.0**           | **2.5**           |
| **ENTROPY_MIN_DIFF**      | **0.15**          | **0.18**         | **0.20**          | **0.25**          |

---

## 🎯 KẾT LUẬN & KHUYẾN NGHỊ

**1. Profile khuyến nghị cho người mới:**  
**H1 (Cân bằng)** – Cân bằng giữa số lượng và chất lượng tín hiệu.

**2. Profile Scalping (M5):**  
Dành cho trader kinh nghiệm, muốn nhiều lệnh, chấp nhận rủi ro cao hơn.

**3. Profile Xu hướng (H4):**  
Dành cho trader bận rộn, ưu tiên chất lượng và xu hướng lớn.

---

## ⚠️ LƯU Ý KHI SỬ DỤNG

- Khi thay **TIMEFRAME**, **bắt buộc** phải thay đổi ít nhất 3 tham số: `PATTERN_LENGTH`, `ATR_SL_HARD_FLOOR`, `SIMILARITY_PERCENTILE`.
- Luôn test trên **tài khoản Demo** trước khi dùng thật.
- Sau khi thay đổi config, restart bot để áp dụng.

Tham sốM5 (Scalping)M15H1 (Cân bằng)H4 (Xu hướng)PATTERN_LENGTH8655SIMILARITY_PERCENTILE60657075PROB_BASE_TRIGGER0.600.620.650.70HALFLIFE_BARS24364872ATR_SL_HARD_FLOOR0.450.350.300.30REWARD_RISK_RATIO1.51.82.02.5ENTROPY_MIN_DIFF0.150.180.200.25

🔍 GIẢI THÍCH CHI TIẾT TẠI SAO PHẢI THÍCH ỨNG NHƯ VẬY
1. PATTERN_LENGTH

M5 cần dài hơn (8): Khung nhỏ có nhiều nhiễu, pattern ngắn dễ bị nhầm lẫn → phải dùng nhiều nến hơn để nhận diện rõ mẫu hình.
H1 và H4 dùng 5: Khung lớn hơn, mỗi nến đã chứa nhiều thông tin hơn nên pattern ngắn vẫn đủ mạnh.

2. SIMILARITY_PERCENTILE

M5: 60 → Nới lỏng để tìm được đủ mẫu (vì thị trường M5 biến động mạnh, khó khớp chính xác).
H4: 75 → Khắt khe hơn vì mỗi mẫu trên H4 có giá trị cao hơn, cần chất lượng cao để tránh tín hiệu giả.

3. PROB_BASE_TRIGGER

Tăng dần theo khung thời gian: Scalping (M5) chấp nhận xác suất thấp hơn để có nhiều tín hiệu, trong khi H4 chỉ lấy tín hiệu rất chắc chắn.

4. HALFLIFE_BARS (Thời gian bán rã trọng số)

M5: 24 (khoảng 2 giờ) → Trọng số giảm nhanh, ưu tiên mẫu gần đây vì thị trường thay đổi rất nhanh.
H4: 72 (khoảng 12 ngày) → Nhìn xa hơn, xu hướng dài hạn quan trọng hơn.

5. ATR_SL_HARD_FLOOR

M5: 0.45 → SL phải rộng hơn để tránh bị quét bởi spread và nhiễu trên khung nhỏ.
H1/H4: 0.30 → Có thể đặt SL chặt hơn vì biến động mỗi nến lớn hơn.

6. REWARD_RISK_RATIO

M5: 1.5 → Chấp nhận R:R thấp hơn vì scalping thường chốt lời nhanh.
H4: 2.5 → Nhắm đến lợi nhuận lớn hơn trên xu hướng dài.

7. ENTROPY_MIN_DIFF

Tăng theo khung lớn: H4 yêu cầu chênh lệch xác suất rõ ràng hơn (0.25) để tránh vào lệnh trong giai đoạn sideway mạnh.


🎯 KẾT LUẬN & KHUYẾN NGHỊ
1. Profile tốt nhất cho người mới bắt đầu:
→ H1 (Cân bằng)
Lý do: Cân bằng giữa số lượng tín hiệu và chất lượng, spread ổn định, dễ quản lý.
2. Profile Scalping (M5):
Phù hợp với trader có kinh nghiệm cao, thời gian ngồi máy nhiều, chấp nhận rủi ro cao hơn để đổi lấy tần suất lệnh nhiều.
3. Profile Xu hướng (H4):
Phù hợp với trader bận rộn, muốn ít lệnh nhưng chất lượng cao, tận dụng xu hướng lớn.
4. Nguyên tắc chung khi chỉnh config:

Thay đổi TIMEFRAME → Phải thay đổi ít nhất PATTERN_LENGTH + ATR_SL_HARD_FLOOR + SIMILARITY_PERCENTILE.
Muốn nhiều tín hiệu hơn → Giảm SIMILARITY_PERCENTILE, PROB_BASE_TRIGGER, ENTROPY_MIN_DIFF.
Muốn an toàn hơn → Tăng ATR_SL_HARD_FLOOR, ENTROPY_MIN_DIFF, PROB_BASE_TRIGGER.
Luôn test trên Demo ít nhất 1-2 tuần trước khi dùng tài khoản thật.