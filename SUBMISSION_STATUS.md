# Gói nộp Phoenix — HOLD, đã ghi nhận kết quả kiểm của người dùng

Người dùng đã làm rõ “ko lỗi rôi”. Đã nhập phiếu QC vòng 2 theo xác nhận chung toàn lô: 40/40 ảnh không có lỗi được báo; ảnh lỗi 0/40, đối tượng lỗi 0/536. Mã 101 và phạm vi 40 ảnh được hiểu theo job20/bối cảnh công việc; nguồn và giới hạn ghi tại [user_confirmation_receipt.json](submission/qc/user_confirmation_receipt.json). Chưa có ghi chú riêng từng ảnh hoặc xác nhận riêng của 102.

- Hai bản gán 101/102 và IAA gốc được giữ nguyên; đủ 80/80 lượt theo cấu trúc file.
- Bản hiện tại: 40 ảnh, 554 annotation; giữ nguyên box/lớp, gỡ cờ review chung theo xác nhận của người dùng.
- Trạng thái tự động vẫn **Hold**: năm box mức che 3 phải giữ can_review theo guideline, và ba vùng chưa có kết luận phân loại cụ thể. Chi tiết ở [remaining_review_flags.csv](submission/qc/remaining_review_flags.csv) và [calibration_open_issues.csv](submission/qc/calibration_open_issues.csv).
- Bản 101 có AI hỗ trợ; chưa có coach xác nhận điều kiện độc lập thuần người. 102 vẫn là người review bản cuối theo TEAM.md; không gán lời người dùng cho 102.

Xem [QC_REPORT.md](submission/qc/QC_REPORT.md), [nguồn và giới hạn](submission/qc/AI_CALIBRATION_PROVENANCE.md), [phiếu vòng 2](submission/qc/reviews/round-02/qc_review.csv). Có thể nộp bằng chứng hiện có dạng Hold theo SUBMISSION.md. Gói chưa được gửi LMS.

## Kiểm lại

Từ thư mục gốc, cài requirements.txt trong môi trường ảo và chạy:

```bash
python scripts/validate_labels.py --all
python scripts/check_submission.py --round 2
```

Lệnh kiểm có thể trả 0 cho gói Hold nhất quán; kết quả đó không phải chứng nhận Release. Gói nén có 40 ảnh và ghi công nguồn; không kèm work/, .git/ hoặc môi trường ảo.

Vòng 1 và bản trước xác nhận được giữ nguyên trong lịch sử. Khi có thay đổi nhãn/calibration tiếp theo: chạy prepare_calibration.py, lưu COCO theo tên phiên bản được in, build_final.py, mở qc_sample.py --round 3, ghi kết quả thật và chạy báo cáo/kiểm với cùng số vòng.
