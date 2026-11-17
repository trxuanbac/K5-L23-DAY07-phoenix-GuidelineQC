# Báo cáo bàn giao Phoenix — HOLD, vòng 2

Người dùng đã làm rõ kết quả kiểm là “ko lỗi rôi”, thay cho thông báo còn lỗi trước đó. Phiếu vòng 2 được Codex nhập theo xác nhận chung này, với mã 101 theo ngữ cảnh job20/DAY07-101 và phạm vi toàn lô 40 ảnh. Không khai đây là ghi chú riêng từng ảnh hoặc review độc lập của 102. Nguồn nguyên văn và cơ sở xác định phạm vi/mã ở user_confirmation_receipt.json.

- Mục đích và giới hạn sử dụng: thực hành đặc tả/QC bbox 2D Day 07 trên 40 ảnh tuyển chọn; người dùng xác nhận chung không còn lỗi, nhưng các ca chưa có kết luận cụ thể vẫn được giữ để kiểm. Chưa phát hành làm bộ huấn luyện đã xác thực độc lập.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; submission/qc/labels_calibrated.json; SHA256 4bdffea5e9bf69b8ea69bcdef541a23db0a6dcb2e7ee922828215a4b02422f1c. Vòng 2 dùng đúng bản nhãn này.
- Guideline và taxonomy khi gán / khi bàn giao: gán theo guideline_v1.0_agreed.md và taxonomy v1.0; bàn giao guideline_v1.1_calibrated.md editorial, người dùng 101 xác nhận chung, chưa có xác nhận riêng của 102. Taxonomy, thuộc tính và ngưỡng giữ nguyên.
- Lượt hoàn thành / được giao: 80/80 lượt theo cấu trúc file, 40 ảnh mỗi mã; bản 101 có AI hỗ trợ, chưa có coach xác nhận đáp ứng yêu cầu độc lập thuần người của Day 07.
- Vòng QC: 2
- Người review và ngày: người dùng báo kết quả kiểm qua hội thoại ngày 2026-11-17; ghi mã 101 theo ngữ cảnh job20, Codex nhập phiếu theo xác nhận chung. Chưa có xác nhận riêng của 102 được cung cấp.
- Đã duyệt / cả lô: nhập kết quả 40/40 ảnh theo xác nhận chung toàn lô của người dùng; chưa có ghi chú riêng từng ảnh. Xem user_confirmation_receipt.json để biết phạm vi được hiểu theo ngữ cảnh.
- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: có thể nộp coach dạng Hold theo SUBMISSION.md. Còn 5 box occluded_level=3 phải giữ can_review theo R-03/R-08 và ba vấn đề cấp ảnh chưa có kết luận chi tiết. Không suy ra phân loại hoặc mức che thay thế từ lời xác nhận chung. 102 vẫn là người review bản cuối theo phân công, chưa có xác nhận riêng của 102 trong hồ sơ.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: tra case_id trong calibration_candidates.csv → calibration_decisions.csv → calibration_ai_trace.json/source_refs → calibration_object_map.csv/final_object_id → labels_calibrated.json và data/images/<image>; rule trong guideline v1.1. Phiếu vòng 2 và user_confirmation_receipt.json ghi nguồn kết quả người dùng báo.

## Kết quả vòng 2

- Phiếu có 40/40 dòng không lỗi theo lời người dùng: ảnh có lỗi 0/40 = 0%; đối tượng hiện có bị lỗi 0/536 = 0%; số bỏ sót được báo 0. Đây là kết quả người dùng báo, không phải Codex đo độc lập chất lượng từng ảnh.
- Bản nhãn giữ nguyên hình học/lớp: 40 ảnh, 554 annotation = 536 đối tượng + 18 ignore. Đã gỡ 501 cờ review chung ở đối tượng theo xác nhận này; năm cờ mức 3 còn giữ theo quy tắc. Hai cờ ở vùng ignore cũng được bỏ vì không có yêu cầu review thuộc tính cho ignore.
- Năm box còn cờ: img_005 ID63, img_006 ID90, img_008 ID114, img_018 ID207, img_021 ID315. Chi tiết ở remaining_review_flags.csv. Không tự đổi mức 3 thành 0/1/2 để đạt Release.
- Ba ca cấp ảnh giữ nguyên: img_009 xe khách van/minibus/bus; img_011 người hóa trang/tượng và xe thật/mô hình; img_024 xe vàng thật/mô hình. Chưa có câu trả lời phân loại cụ thể cho các vùng này; xem calibration_open_issues.csv.
- 860 dòng calibration đã ghi nhận chấp nhận chung của người dùng 101 cho cách xử lý hiện tại. Chưa có biên bản riêng từng ca hoặc xác nhận riêng của 102. Guideline vẫn giữ nghĩa v1.0 và các ngưỡng gốc.
- Vòng 1 cùng bản trước xác nhận được giữ nguyên trong reviews/round-01 và history/phase6-before-user-confirmation. Không thay lịch sử 0/40 của vòng 1 thành kết quả mới.

## IAA trước calibration và nguồn nhãn

282 cặp ghép, IoU trung bình 0.7923116979; cùng lớp 276/282 (97.87%), cùng occluded 70/282 (24.82%), cùng truncated 269/282 (95.39%). 243/86 box chưa ghép của 101/102; 860 dòng trên 36 ảnh. Hai bộ nhãn gốc, IAA và ngưỡng được bảo toàn từng byte. IAA là phép đo chẩn đoán trước calibration.

Bản 101 có AI hỗ trợ. Sự xác nhận không còn lỗi của người dùng không phải xác nhận của coach về yêu cầu độc lập thuần người. Ghi chú cá nhân chỉ ghi nội dung/thống kê có nguồn, không tạo thay lời tự thuật.

## Kiểm tra và bàn giao

Đã tạo phiên bản COCO mới, build_final.py, mở qc_sample.py --round 2, nhập phiếu theo lời người dùng và chạy qc_report.py --round 2 --guideline-gap 0 --data-problem 3. Báo cáo tự động là HOLD do năm cờ mức 3 và ba vấn đề còn mở.

check_submission.py --round 2 kiểm tính nhất quán của gói Hold; nó không thay người kiểm chất lượng hoặc quyết định của coach. Khi có kết luận/sửa cụ thể, giữ lịch sử, build phiên bản mới và mở vòng QC mới. Gói chưa được gửi lên LMS hoặc kênh coach.
