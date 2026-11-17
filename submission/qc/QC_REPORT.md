# Báo cáo QC và bàn giao — Phoenix

- Mục đích và giới hạn sử dụng: thực hành đặc tả và kiểm tra nhãn bbox 2D trên 40 ảnh tuyển chọn; dữ liệu hiện ở trạng thái Hold, chưa phát hành làm bộ huấn luyện đã xác thực độc lập.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; submission/qc/labels_calibrated.json; SHA256 4bdffea5e9bf69b8ea69bcdef541a23db0a6dcb2e7ee922828215a4b02422f1c.
- Guideline và taxonomy khi gán / khi bàn giao: guideline v1.0 khi gán; guideline v1.1 biên tập khi bàn giao; taxonomy v1.0. Không đổi lớp, thuộc tính hoặc ngưỡng.
- Lượt hoàn thành / được giao: 80/80 lượt, gồm hai bộ nhãn, mỗi bộ 40 ảnh.
- Vòng QC: 3
- Người review và ngày: mã 101 theo hồ sơ xác nhận ngày 2026-11-17; kết quả xác nhận chung được ghi ở phiếu vòng 2. Chưa có phiếu riêng của 102.
- Đã duyệt / cả lô: 40/40 ảnh theo phiếu xác nhận chung toàn lô; chưa có ghi chú riêng từng ảnh.
- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: nộp coach để đối chiếu hồ sơ và các ca còn mở. Còn 5 box occluded_level=3 giữ can_review theo R-03/R-08 và 3 vấn đề cấp ảnh chưa có kết luận phân loại cụ thể.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: calibration_candidates.csv/case_id → calibration_decisions.csv → calibration_trace.json/source_refs → calibration_object_map.csv/final_object_id → labels_calibrated.json và data/images/<image>; quy tắc tại guideline v1.1.

## Kết quả theo phiếu QC

| Chỉ số | Kết quả |
|---|---:|
| Ảnh có kết quả kiểm / cả lô | 40/40 |
| Ảnh lỗi / ảnh đã kiểm | 0/40 = 0% |
| Đối tượng lỗi / đối tượng đã kiểm | 0/536 = 0% |
| Vật bỏ sót được báo | 0 |
| Tổng annotation | 554 |
| Đối tượng / vùng ignore | 536 / 18 |

Kết quả dựa trên xác nhận chung toàn lô. Vòng 3 kế thừa phiếu vòng 2 khi cập nhật tài liệu và manifest; checksum nhãn không đổi, không phát sinh lượt kiểm mới. Biên bản chuyển dữ liệu: reviews/round-03/review_transfer.json. Nguồn và phạm vi xác nhận lưu trong user_confirmation_receipt.json.

## Các ca còn mở

- Mức che 3: img_005 ID63; img_006 ID90; img_008 ID114; img_018 ID207; img_021 ID315. Chi tiết tại remaining_review_flags.csv.
- Vấn đề cấp ảnh: img_009 xe khách van/minibus/bus; img_011 người hóa trang/tượng và xe thật/mô hình; img_024 xe vàng thật/mô hình. Chi tiết tại calibration_open_issues.csv.
- 860 dòng calibration được ghi theo xác nhận chung; chưa có biên bản riêng từng ca hoặc phiếu riêng của 102.

## IAA trước calibration

282 cặp ghép; IoU trung bình 0.7923116979; cùng lớp 276/282 (97.87%); cùng occluded_level 70/282 (24.82%); cùng truncated 269/282 (95.39%). Box chưa ghép: 243/86. Tổng 860 dòng trên 36 ảnh.

IAA dùng để chẩn đoán trước calibration. Nhãn nguồn, số liệu IAA và các ngưỡng được giữ nguyên. Nhật ký phương pháp, thay đổi và phiên bản tại CALIBRATION_LOG.md.

## Kiểm tra hồ sơ

Chạy validate_labels.py --all, qc_report.py --round 3 --guideline-gap 0 --data-problem 3 và check_submission.py --round 3. Trạng thái tự động: HOLD. Kiểm cấu trúc và checksum không thay thế việc kết luận các ca còn mở.
