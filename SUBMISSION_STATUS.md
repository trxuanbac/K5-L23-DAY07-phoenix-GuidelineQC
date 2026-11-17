# Gói nộp Phoenix — HOLD

Đã có gói hồ sơ để nộp theo trường hợp Hold trong `SUBMISSION.md`. Đây là bàn giao bằng chứng đã có, chưa phải bản nhãn Release hoặc thông báo đã nộp LMS.

- Nhãn gán ban đầu: 101 và 102, đủ 80/80 lượt, giữ nguyên.
- IAA trước calibration: 282 cặp ghép; IoU trung bình 0.7923, cùng lớp 276/282; 860 ca trên 36 ảnh. IAA dùng chẩn đoán, không tự quyết định Release.
- Nhãn đề xuất sau rà AI: 40 ảnh, 554 annotation; manifest và checksum ở `submission/qc/batch_manifest.json`.
- Guideline v1.1 và 860 dòng calibration là **đề xuất AI chờ người xác nhận**. Bản 101 cũng có AI hỗ trợ; chưa xác nhận đáp ứng yêu cầu người thật gán độc lập.
- QC người thật vòng 1: 0/40 ảnh, tỉ lệ lỗi N/A. Còn 506 box review và ba vấn đề cấp ảnh. Không coi việc kiểm cấu trúc qua là chất lượng nhãn đã đạt.

Xem [QC_REPORT.md](submission/qc/QC_REPORT.md), [nguồn và giới hạn](submission/qc/AI_CALIBRATION_PROVENANCE.md), [phiếu QC](submission/qc/reviews/round-01/qc_review.csv). 102 phụ trách review bản cuối theo `TEAM.md`; 101/102 cần xác nhận calibration, guideline và nguồn hỗ trợ với coach.

## Kiểm lại gói hiện tại

Từ thư mục gốc repo, dùng Python 3 và cài `requirements.txt` trong môi trường ảo:

```bash
python scripts/validate_labels.py --all
python scripts/check_submission.py --round 1
```

Lệnh kiểm hồ sơ có thể trả 0 với một gói Hold trung thực. Nó không xác nhận nhãn chính xác, không thay người review và không có nghĩa Release.

## Sau khi 102 kiểm thật

Ghi quyết định calibration, người chốt và kiểm lại theo từng ca; chốt ba vấn đề cấp ảnh, guideline v1.1 và ghi chú cá nhân bằng dữ kiện thật. Bản đề xuất/snapshot vòng 1 cần được giữ lịch sử trước khi cập nhật.

Nếu đổi nhãn hoặc đầu vào calibration: chạy `prepare_calibration.py`, xuất COCO đã sửa đúng tên phiên bản được in, chạy `build_final.py`, rồi mở `qc_sample.py --round 2` và điền phiếu bằng kết quả review thật. Chạy `qc_report.py --round 2` với số ca/vấn đề còn mở thực tế; cập nhật `QC_REPORT.md` theo kết quả và kiểm `check_submission.py --round 2`.

Gói nộp không kèm `.git/`, `work/`, môi trường ảo hoặc thông tin đăng nhập. Có đủ 40 ảnh và ghi công nguồn để mở thử độc lập.
