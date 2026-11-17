# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 2
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 282 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 282 | 0.792 | 97.9% | 35.8% | 78.4% | 243/91 | 0 | CHƯA ĐẠT |
| 101|102 | 40 | 282 | 0.792 | 97.9% | 35.8% | 78.4% | 243/91 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 851 ({'bo_sot_thua': 334, 'iou_thap': 157, 'sai_thuoc_tinh': 199, 'co_can_review': 139, 'sai_lop': 6, 'bat_dong_ignore': 16}), trên 38 ảnh cần calibration.
