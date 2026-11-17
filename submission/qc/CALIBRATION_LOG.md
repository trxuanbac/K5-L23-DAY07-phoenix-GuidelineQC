# Nhật ký hiệu chỉnh và kiểm tra

Ngày cập nhật: 2026-11-17. Phạm vi: 40 ảnh, bbox 2D, taxonomy v1.0.

## Đối chiếu

Hai bộ nhãn được so sánh theo IoU và lớp. Có 282 cặp ghép, 860 dòng cần đối chiếu trên 36 ảnh. Các nhóm vấn đề gồm box chưa ghép, biên box, lớp, thuộc tính, ignore và cờ review.

Quá trình hiệu chỉnh dùng bản 101 làm nền; bổ sung 13 annotation theo các vùng đã đối chiếu và sửa một số biên, lớp, vùng nhỏ. Mỗi thay đổi có liên kết tới ảnh, nhãn nguồn và quy tắc trong [hồ sơ truy vết](calibration_trace.json).

## Kết quả hiện tại

- 40 ảnh, 554 annotation: 536 đối tượng và 18 vùng ignore.
- Phiếu QC ghi 40/40 ảnh không lỗi theo xác nhận chung toàn lô; chưa có ghi chú riêng từng ảnh.
- Sau xác nhận, đã gỡ 501 cờ review chung ở đối tượng và 2 cờ ở vùng ignore.
- Còn 5 box occluded_level=3 theo R-03/R-08 và 3 vấn đề cấp ảnh. Báo cáo tự động: Hold.

Chi tiết: [cờ review](remaining_review_flags.csv), [vấn đề cấp ảnh](calibration_open_issues.csv), [báo cáo QC](QC_REPORT.md).

## Phiên bản và bằng chứng

Nhãn nguồn, IAA và ngưỡng được giữ nguyên. Vòng 3 chỉ biên tập tài liệu, cập nhật đường dẫn truy vết và manifest; nhãn cuối giữ nguyên checksum so với vòng 2. Phiếu vòng 3 kế thừa vòng 2, có biên bản chuyển dữ liệu để phân biệt với lượt kiểm mới. Các bản và snapshot trước vẫn được lưu.

Nguồn dữ liệu, phạm vi xác nhận và checksum được lưu riêng trong manifest, hồ sơ xác nhận và các file truy vết.
