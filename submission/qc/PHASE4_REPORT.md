# Kết quả Pha 4 — nhóm Phoenix

Đã cập nhật sau khi pull bản sửa nhãn 102 tại commit `8b1235e`. Hai bản đủ 40 ảnh; đã chạy lại validation, IAA và ảnh chồng nhãn. IAA chưa đạt ngưỡng IoU; cần calibration.

## Đầu vào

- `101.json`: COCO 1.0 xuất từ CVAT task 20 / job 20 (`DAY07-101`), 40 ảnh, 541 annotation (525 vật thể, 16 ignore). Giữ nguyên SHA-256 so với Pha 4 trước.
- `102.json`: bản sửa từ commit `8b1235e`, 40 ảnh, 368 annotation. Giữ nguyên nội dung đã push bởi 102.
- Báo cáo/bảng calibration cũ được giữ tại [history/phase4-before-8b1235e](history/phase4-before-8b1235e/README.md); không dùng số đo cũ cho bản 102 hiện tại.

## Số đo hiện tại

| Chỉ số | Kết quả | Ngưỡng / ý nghĩa |
|---|---:|---|
| Độ phủ | 80/80 | 40 ảnh × 2 thành viên |
| Cặp box ghép | 282 | Ghép 1–1, IoU ≥ 0,5 |
| IoU trung bình | 0.792 | Cần ≥ 0,85: chưa đạt |
| Đồng thuận lớp | 97.9% | Cần ≥ 95%: đạt phần lớp |
| Đồng thuận occluded_level | 24.8% | Trên các cặp ghép |
| Đồng thuận truncated | 95.4% | Trên các cặp ghép |
| Box chưa ghép 101 / 102 | 243 / 86 | Chưa kết luận bên nào bỏ sót/vẽ thừa |
| Ảnh cần calibration | 36/40 | Phải đối chiếu ảnh gốc và guideline |

## Dòng cần xem

860 dòng thuộc nhiều loại; một vật có thể đóng góp nhiều dòng. Không diễn giải thành 860 vật sai.

| Loại | Số dòng |
|---|---:|
| `bo_sot_thua` | 329 |
| `iou_thap` | 156 |
| `sai_thuoc_tinh` | 214 |
| `co_can_review` | 139 |
| `sai_lop` | 6 |
| `bat_dong_ignore` | 16 |

## Thay đổi và ưu tiên kiểm lại

1. Bản 102 đã bổ sung `occluded_level`: {"0": 71, "2": 87, "3": 115, "1": 95}; `truncated`: {"no": 307, "yes": 61}. Nhận định cũ rằng toàn bộ là 0/no không còn áp dụng.
2. Có 115 box 102 mang `occluded_level=3` nhưng `can_review=false`. Theo định nghĩa mức che ở guideline v1.0, mức 3 là không ước lượng được phần bị che và cần cờ review; xem [rule_review_warnings.csv](rule_review_warnings.csv).
3. Có 3 box dưới 20 px trong hai bộ nhãn:
   - `img_009.jpg`, 102 #101, `person`, cao 16.00 px; đối chiếu R-06.
   - `img_009.jpg`, 102 #102, `person`, cao 15.67 px; đối chiếu R-06.
   - `img_034.jpg`, 102 #301, `person`, cao 19.35 px; đối chiếu R-06.
4. `img_027.jpg`: 1 dòng trong danh sách cần xem; đối chiếu R-04 về tượng/xe đồ chơi với ảnh gốc, không suy ra xe thật chỉ từ hình dạng.
4. `img_038.jpg`: 22 dòng trong danh sách cần xem; đối chiếu R-04 về tượng/xe đồ chơi với ảnh gốc, không suy ra xe thật chỉ từ hình dạng.

## Ảnh ưu tiên

| Ảnh | Dòng cần xem | Điểm |
|---|---:|---:|
| `img_021.jpg` | 152 | 191 |
| `img_019.jpg` | 48 | 90 |
| `img_030.jpg` | 62 | 88 |
| `img_032.jpg` | 47 | 64 |
| `img_003.jpg` | 44 | 61 |
| `img_008.jpg` | 36 | 51 |
| `img_006.jpg` | 35 | 49 |
| `img_038.jpg` | 22 | 44 |

## Mở kết quả và bước tiếp

- [Báo cáo IAA từ script](iaa_summary.md)
- [Các ca cần calibration](calibration_candidates.csv)
- [Bảng quyết định hiện tại](calibration_decisions.csv)
- [Cảnh báo quy tắc cần review](rule_review_warnings.csv)
- [Xếp hạng ảnh](image_scores.csv)
- [Ảnh chồng nhãn tổng quan](../../work/viz/_overview.png)

Màu overlay: 101 đỏ, 102 xanh. work/ dùng cục bộ. Các bản trước được lưu trong history/; số liệu IAA trong báo cáo này giữ nguyên. Kết quả hiệu chỉnh và QC hiện tại được tổng hợp tại [báo cáo QC](QC_REPORT.md) và [nhật ký hiệu chỉnh](CALIBRATION_LOG.md).
