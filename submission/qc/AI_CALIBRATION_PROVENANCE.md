# Nguồn và giới hạn calibration — cập nhật sau xác nhận người dùng

- Ngày: 2026-11-17. Bản đầu do AI-Codex chuẩn bị theo yêu cầu 101.
- Người dùng nói “tôi đã xác nhân rồi”, chọn “Đã kiểm nhưng còn lỗi cần sửa”, sau đó làm rõ “ko lỗi rôi”. Lời sau cùng thay kết quả báo còn lỗi trước đó. Xem user_confirmation_receipt.json.
- Đã nhập phiếu vòng 2 gồm 40 dòng không lỗi theo xác nhận chung toàn lô. Mã 101 được suy ra từ job20/DAY07-101 và ngữ cảnh nhóm; không gán lời này cho 102. Chưa có ghi chú riêng từng ảnh hoặc biên bản riêng từng case.
- 860 dòng calibration ghi nhận giữ cách xử lý hiện tại theo xác nhận chung của người dùng, còn ghi rõ chưa có review độc lập của 102. Guideline v1.1 là giải thích editorial, không đổi nghĩa taxonomy hay ngưỡng v1.0.

## Phần AI đã thực hiện

Đã đối chiếu 40 ảnh nguồn và các tổng quan gốc/overlay, phóng các vùng khó gồm img_008, img_010, img_021, img_024, img_039; giữ 101 làm nền, chỉ bổ sung/sửa theo trace cụ thể. Bản đầu có 13 annotation bổ sung và một số sửa biên/lớp/vùng nhỏ, không hợp mù hai bộ nhãn hay lấy trung bình tọa độ.

Hai nhãn gán ban đầu và IAA được bảo toàn từng byte. Bản 101 có AI hỗ trợ thao tác/gán trước đó; không khai là người thật gán độc lập thuần túy. Coach chưa xác nhận việc đáp ứng yêu cầu Day 07.

## Thay đổi sau xác nhận chung

Giữ nguyên box/lớp và số lượng: 40 ảnh, 554 annotation = 536 đối tượng + 18 ignore. Gỡ 501 cờ chờ review chung ở đối tượng và hai cờ ở ignore theo xác nhận của người dùng. Giữ năm cờ occluded_level=3 vì guideline yêu cầu; không tự gán mức che thay thế từ lời “không lỗi”. Ba vấn đề cấp ảnh cũng chưa có kết luận phân loại cụ thể.

Vòng 2 phản ánh lời người dùng báo: 0/40 ảnh lỗi, 0/536 đối tượng lỗi. Các số này không phải kết quả Codex tự thực hiện một vòng người thật kiểm. Báo cáo tự động vẫn HOLD do năm cờ và ba vấn đề còn mở; xem QC_REPORT.md và remaining_review_flags.csv.

## Tra cứu và lịch sử

calibration_ai_trace.json và calibration_object_map.csv nối case/nguồn với ID nhãn hiện tại. calibration_open_issues.csv giữ ba ca chưa rõ. Phiếu và snapshot vòng 2 nằm trong reviews/round-02; vòng 1 giữ nguyên. history/phase6-before-user-confirmation giữ artifact của commit d066610; COCO cũ vẫn có tên phiên bản riêng.

Ảnh minh họa reviews/round-01/ai_preview vẫn đúng hình học và ID vì lần cập nhật này chỉ đổi cờ review. Thuộc tính hiện tại phải đọc từ labels_calibrated.json và snapshot vòng 2. Nhãn, manifest và vòng QC dùng đúng phiên bản được kiểm checksum bởi công cụ gốc.
