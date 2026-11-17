# Hồ sơ nộp Phoenix

- Ngày cập nhật: 2026-11-17.
- Phạm vi: 40 ảnh, hai bộ nhãn, 80/80 lượt gán.
- Nhãn sau hiệu chỉnh: 554 annotation, gồm 536 đối tượng và 18 vùng ignore.
- Kết quả theo phiếu QC: 40/40 ảnh không có lỗi được báo cáo; tỉ lệ lỗi ảnh 0/40, lỗi đối tượng 0/536.
- Trạng thái: **Hold**. Còn 5 box mức che khuất 3 và 3 vấn đề cấp ảnh cần kết luận cụ thể.

[Báo cáo QC](submission/qc/QC_REPORT.md) · [Nhật ký hiệu chỉnh](submission/qc/CALIBRATION_LOG.md) · [Danh sách cờ review](submission/qc/remaining_review_flags.csv) · [Vấn đề cấp ảnh](submission/qc/calibration_open_issues.csv).

## Kiểm tra gói nộp

```bash
python scripts/validate_labels.py --all
python scripts/check_submission.py --round 3
```

Vòng 3 cập nhật tài liệu và manifest; kế thừa kết quả phiếu vòng 2 vì nội dung nhãn không đổi. Các vòng trước vẫn được lưu để đối chiếu. Nguồn kết quả và phạm vi xác nhận được lưu trong hồ sơ xác nhận kèm theo.
