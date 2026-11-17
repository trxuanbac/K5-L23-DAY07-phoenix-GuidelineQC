# Kết quả Pha 4 — nhóm Phoenix

Trạng thái: đã xuất COCO 101, kiểm cấu trúc cả hai bản, chạy so sánh và tạo ảnh chồng nhãn. IAA chưa đạt; cần calibration, chưa phải nhãn cuối đã QC.

## Đầu vào

- `101.json`: bản COCO 1.0 xuất nguyên trạng từ CVAT task 20 / job 20 (`DAY07-101`), 40 ảnh, 541 annotation (525 vật thể và 16 ignore).
- `102.json`: bản đã push trong repo, 40 ảnh, 373 annotation. Giữ nguyên file.
- Bản 101 từng có AI hỗ trợ gán nhãn trong phiên làm việc trước; người dùng báo đã hoàn tất. Kết quả dưới đây là so sánh hai artifact, không tự xác nhận tính độc lập của hai người gán hoặc phê duyệt QC.
- Hai bản nhãn được giữ nguyên trong Pha 4; không sửa nhãn để tăng số đo đồng thuận.

## Số đo

| Chỉ số | Kết quả | Ngưỡng / cách đọc |
|---|---:|---|
| Độ phủ | 80/80 lượt | 40 ảnh × 2 thành viên |
| Cặp box ghép được | 282 | Ghép 1–1, IoU ≥ 0,5 |
| IoU trung bình | 0.792 | Cần ≥ 0,85: chưa đạt |
| Đồng thuận lớp | 97.9% | Cần ≥ 95%: đạt phần lớp |
| Đồng thuận occluded_level | 35.8% | Trên 282 cặp ghép |
| Đồng thuận truncated | 78.4% | Trên 282 cặp ghép |
| Box chưa ghép 101 / 102 | 243 / 91 | Chưa kết luận bên nào bỏ sót/vẽ thừa |
| Ảnh bị cờ | 38/40 | Cần xem và calibration |

## Dòng cần xem

851 dòng ứng với nhiều loại vấn đề; một vật có thể góp nhiều dòng. Không diễn giải thành 851 vật thể sai.

| Loại | Số dòng |
|---|---:|
| `bo_sot_thua` | 334 |
| `iou_thap` | 157 |
| `sai_thuoc_tinh` | 199 |
| `co_can_review` | 139 |
| `sai_lop` | 6 |
| `bat_dong_ignore` | 16 |

## Ưu tiên kiểm lại

1. Thuộc tính của bản 102: tất cả 373/373 vật thể là `occluded_level=0`, `truncated=no`, `can_review=false`. Cần đối chiếu ảnh và xác nhận các thuộc tính đã được gán đúng, thay vì suy ra chúng đúng chỉ vì hợp cấu trúc.
2. `img_038.jpg`: 22 box car chỉ có trong bản 102. Ảnh chồng nhãn cho thấy đây là xe đồ chơi; đối chiếu R-04 khi chốt calibration.
3. `img_027.jpg`: C583 là truck chỉ có ở bản 102 trên ảnh tượng pickup; đối chiếu R-04.
4. 3 box dưới 20 px trong bản 102:
   - `img_009.jpg`, annotation #102, `person`, cao 16.00 px: đối chiếu R-06.
   - `img_009.jpg`, annotation #103, `person`, cao 15.67 px: đối chiếu R-06.
   - `img_034.jpg`, annotation #302, `person`, cao 19.35 px: đối chiếu R-06.

5. Các ảnh có điểm bất đồng cao nhất:
   - `img_021.jpg`: 153 dòng cần xem, điểm 193.
   - `img_019.jpg`: 47 dòng cần xem, điểm 89.
   - `img_030.jpg`: 62 dòng cần xem, điểm 88.
   - `img_003.jpg`: 47 dòng cần xem, điểm 64.
   - `img_032.jpg`: 39 dòng cần xem, điểm 56.
   - `img_008.jpg`: 39 dòng cần xem, điểm 54.
   - `img_006.jpg`: 33 dòng cần xem, điểm 47.
   - `img_038.jpg`: 22 dòng cần xem, điểm 44.

## Mở kết quả

- [Báo cáo IAA nguyên gốc](iaa_summary.md)
- [Danh sách ca cần calibration](calibration_candidates.csv)
- [Bảng quyết định cần hai thành viên điền](calibration_decisions.csv)
- [Xếp hạng ảnh cần xem](image_scores.csv)
- [Ảnh chồng nhãn tổng quan](../../work/viz/_overview.png)
- [Ví dụ xe đồ chơi img_038](../../work/viz/img_038.png)

Màu overlay: 101 đỏ, 102 xanh. `work/` chỉ dùng cục bộ, không nộp theo SUBMISSION.md.

## Bước kế tiếp

Hai thành viên mở từng ca, đối chiếu ảnh gốc và guideline, điền quyết định, rule, phiên bản, người chốt, dấu vết sửa và kiểm lại trong `calibration_decisions.csv`. 851 dòng đã được tạo với các trường quyết định còn trống. Chưa tạo guideline v1.1 hoặc khai rằng đã có quyết định nhóm. Sau khi chốt thì cập nhật guideline/change record và chạy prepare_calibration.py để sửa đúng phạm vi; chưa dùng consensus_draft.json làm nhãn đã được duyệt.
