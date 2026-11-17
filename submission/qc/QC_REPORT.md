# Báo cáo bàn giao Phoenix — HOLD

- Mục đích và giới hạn sử dụng: thực hành đặc tả/QC bbox 2D Day 07 trên 40 ảnh tuyển chọn; nhãn đề xuất AI chờ người xác nhận, chưa dùng làm bộ huấn luyện đã phát hành.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; submission/qc/labels_calibrated.json; SHA256 567123437647c34af802779ef6a85e6351f3661051be604b4c5225ba19c3dd17. Tên file theo công cụ; trạng thái bản đề xuất vẫn Hold.
- Guideline và taxonomy khi gán / khi bàn giao: gán theo guideline_v1.0_agreed.md và taxonomy v1.0; bàn giao guideline_v1.1_calibrated.md dạng đề xuất editorial chưa được nhóm xác nhận, taxonomy và ngưỡng v1.0 giữ nguyên.
- Lượt hoàn thành / được giao: 80/80 lượt theo cấu trúc file, 40 ảnh mỗi mã; bản 101 có AI hỗ trợ, chưa xác nhận đủ yêu cầu độc lập thuần người của Day 07.
- Vòng QC: 1
- Người review và ngày: 102 được phân công; chưa có người review điền phiếu. AI-Codex chuẩn bị đề xuất và kiểm hồ sơ ngày 2026-11-17, không đứng tên review 101/102.
- Đã duyệt / cả lô: người review 0/40 ảnh; AI đã xem tổng quan 40 ảnh và các vùng phóng lớn ghi ở AI_CALIBRATION_PROVENANCE.md, không tính thành số người duyệt.
- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: nộp coach để xem bằng chứng còn Hold theo SUBMISSION.md; 102 kiểm lại 40 ảnh, 101/102 chốt 860 ca đề xuất và guideline, xác nhận nguồn AI với coach. Còn 506 box can_review và ba vấn đề cấp ảnh; chỉ dùng rà soát và học tập.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: tra case_id trong calibration_candidates.csv → calibration_decisions.csv → calibration_ai_trace.json/source_refs → calibration_object_map.csv/final_object_id → labels_calibrated.json và data/images/<image>; rule trong guideline v1.1. Ví dụ C001/img_001/R-01/R-02/R-08 dẫn tới 101:4 và ID tương ứng trong bản đề xuất.

## Số liệu và trạng thái thực tế

- IAA trước calibration: 282 cặp ghép; IoU trung bình 0.7923116979; cùng lớp 276/282 (97.87%), cùng occluded_level 70/282 (24.82%), cùng truncated 269/282 (95.39%). 243/86 box chưa ghép của 101/102; 860 dòng trên 36 ảnh. Không đổi nhãn gốc hoặc ngưỡng để nâng số liệu. IAA này dùng chẩn đoán, không tự quyết định Release sau calibration.
- Bản đề xuất: 40 ảnh, 554 annotation = 536 đối tượng + 18 ignore. Cả 860 dòng calibration còn AI_PROPOSAL_PENDING_102; không có bằng chứng họp/chốt nhóm được cung cấp.
- Vòng 1 đầy đủ phạm vi 40 ảnh nhưng phiếu người review còn trống. Ảnh lỗi trên ảnh đã duyệt: 0/0 (N/A); đối tượng lỗi trên đối tượng đã duyệt: 0/0 (N/A); số bỏ sót ghi trong phiếu: 0. Các số 0 này phản ánh chưa có phiếu, không chứng minh sạch lỗi.
- Ba vấn đề chưa có kết luận ở calibration_open_issues.csv: img_009 xe khách, img_011 nhân vật/xe trưng bày, img_024 xe vàng trên mái.
- Ghi chú 101 do AI tổng hợp dữ kiện, ghi chú 102 giữ nội dung cá nhân sẵn có và bổ sung thống kê có nguồn; phần tự thuật chưa được người đó xác nhận vẫn được ghi rõ. Không tạo thay cảm nhận hoặc xác nhận cá nhân.

## Kiểm tra và tái lập

Đã chạy build_final.py, validate_labels.py --all, qc_sample.py --round 1 và qc_report.py --round 1 --guideline-gap 860 --data-problem 3. Phiếu, snapshot, checksum và quyết định tự động lưu ở reviews/round-01/. Báo cáo tự động là HOLD. Ba cảnh báo vật nhỏ ở bản 102 gốc được giữ nguyên làm bằng chứng; bản đề xuất hiện không còn đối tượng thường cao dưới 20 px, nhưng độ chính xác biên/nhận dạng vẫn cần người kiểm.

check_submission.py --round 1 kiểm tính nhất quán của một gói Hold, không xác nhận chất lượng nhãn hay công nhận việc dùng AI phù hợp quy định lớp. Gói nén đã có dữ liệu nguồn, ghi công và hướng dẫn, chưa gửi lên LMS hay kênh coach.

Sau khi người thật chốt/sửa: giữ lịch sử, tạo COCO theo phiên bản mới, build_final lại, mở vòng QC mới, ghi phiếu thật và chạy báo cáo/kiểm nộp theo vòng mới. Chỉ quyết định Release khi đầy đủ bằng chứng thực tế đáp ứng guideline.
