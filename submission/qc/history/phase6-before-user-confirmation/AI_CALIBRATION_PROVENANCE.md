# Nguồn và giới hạn của bản đề xuất calibration

- Ngày chuẩn bị: 2026-11-17.
- Người thực hiện phần hỗ trợ: AI-Codex, theo yêu cầu của 101. Không ghi nhận AI là 101 hoặc 102.
- Trạng thái: **HOLD — AI_PROPOSAL_PENDING_101_102**. Chưa có xác nhận calibration của người thật; chưa có phiếu QC người thật đã điền.
- Hai file gán ban đầu `submission/labels/101.json` và `102.json` được bảo toàn từng byte. Không sửa các đầu vào để nâng IAA.
- Bản 101 trước đó có AI hỗ trợ thao tác/gán nhãn. Không khẳng định bộ đầu vào này đáp ứng yêu cầu người thật gán độc lập của Day 07. Cần trình bày nguồn này với coach; nếu cần lượt gán độc lập thuần người, phải thực hiện lượt mới và giữ lịch sử.

## Công việc thực sự đã thực hiện

1. Đối chiếu guideline v1.0, 40 ảnh nguồn, hai bộ COCO, danh sách 860 ca IAA; xem tổng quan 40 ảnh và phóng các vùng khó, gồm img_008, img_010, img_021, img_024, img_039. Xem lại tổng quan ảnh gốc cạnh overlay đề xuất cho đủ 40 ảnh. Đây là rà bằng AI, không phải QC độc lập của 102 và không chứng minh độ chính xác từng pixel.
2. Giữ nhãn 101 làm nền tạm. Chỉ bổ sung 13 box/vùng có đề xuất cụ thể từ nguồn 102; sửa một số biên, đề xuất một van thành car ở img_013, chuyển vùng người dưới 20 px ở img_009 sang ignore. Không lấy hợp toàn bộ hai bộ nhãn và không lấy trung bình tọa độ.
3. Các xe đạp, tuk-tuk, bus ngoài bộ lớp; đồ chơi, tượng, ảnh in và phản chiếu được đề xuất loại theo quy tắc hiện hành. Người thật trên phương tiện ngoài phạm vi vẫn được xét riêng. Với các phần của cùng một xe ở img_021, đề xuất một box thay vì thêm xe trùng.
4. Bất đồng chưa được người quyết định được giữ cờ review hoặc ghi vấn đề cấp ảnh. 860 dòng trong `calibration_decisions.csv` đều có `review_status=AI_PROPOSAL_PENDING_102`; nội dung là đề xuất/chuyển kiểm, **không phải 860 ca đã được nhóm chốt**. Các trường `nguoi_chot` và `kiem_lai` ghi rõ chưa có phê duyệt của người.
5. Tạo guideline v1.1 dạng đề xuất biên tập, nhãn theo tên phiên bản, nhãn tổng hợp, manifest và vòng QC 1 bằng script gốc. Không đổi taxonomy, ngưỡng, thuật toán tính IAA hoặc QC. Tên file `labels_calibrated.json` theo quy ước công cụ; trạng thái sử dụng của nó vẫn là bản đề xuất HOLD.

## Số liệu của bản đề xuất

- 40 ảnh; 554 annotation = 536 đối tượng + 18 vùng ignore.
- Đối tượng: person 309, car 72, truck 13, motorcycle 136, van 6.
- 506/536 đối tượng còn `can_review=true`, gồm các bất đồng chuyển người review. Không tắt hàng loạt để đạt Release.
- Ba vấn đề cấp ảnh chưa chốt: img_009 (van/minibus/bus), img_011 (người hóa trang/tượng, xe thật/mô hình), img_024 (xe vàng thật/mô hình).
- Người review hoàn thành: 0/40 ảnh. Tỉ lệ lỗi ảnh và đối tượng đều N/A, không phải 0%.

## Tra cứu bằng chứng

- [calibration_ai_trace.json](calibration_ai_trace.json) lưu nguồn, box, thuộc tính đề xuất, ghi chú, ID nhãn và liên kết từng case_id.
- [calibration_object_map.csv](calibration_object_map.csv) nối ID nguồn với ID trong nhãn đề xuất cuối.
- [calibration_open_issues.csv](calibration_open_issues.csv) lưu các ca chưa có box/lớp đủ căn cứ sau đối chiếu. File này được tạo sau khi khóa IAA, không thêm vào đầu vào đo IAA cũ.
- [Vòng QC 1](reviews/round-01/qc_report_auto.md) và [phiếu còn trống](reviews/round-01/qc_review.csv) là bằng chứng trạng thái chờ review.
- Ảnh minh họa trong `reviews/round-01/ai_preview/` hỗ trợ kiểm lại; chúng không phải phiếu QC đã được ký.

## Phần cần người thật hoàn thành

102 kiểm 40 ảnh, xác nhận/sửa từng ca và ba vấn đề cấp ảnh; cập nhật bảng calibration bằng quyết định thực tế và ghi người chốt/người kiểm. 101/102 xác nhận guideline và nguồn AI hỗ trợ với coach. Ghi chú cá nhân chỉ ghi điều người đó thực sự làm.

Nếu sửa nhãn hoặc bảng quyết định, chạy lại `prepare_calibration.py` để lấy tên phiên bản mới, lưu COCO đúng phạm vi, rồi `build_final.py`. Mở vòng QC mới để giữ bằng chứng vòng 1. Chỉ đổi sang Release khi QC thật và đầy đủ đáp ứng quy tắc.
