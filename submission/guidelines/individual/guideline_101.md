# Guideline gán nhãn — v0.1-draft — 101

- Phiên bản: v0.1-draft
- Ngày soạn: 2026-11-16
- Người viết / duyệt: 101 / chưa duyệt
- Trạng thái: bản nháp hỗ trợ bằng AI, chưa xác nhận đã xem đủ 40 ảnh; chưa dùng để gán nhãn chính thức.

> Thành viên 101 cần tự xem ảnh, kiểm tra và quyết định nội dung trước khi nộp bản cá nhân. Các quy tắc đề xuất dưới đây chưa phải quyết định của thành viên hoặc thống nhất của nhóm. Chưa có ca ảnh thực tế được ghi nhận trong bản này.

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện người và phương tiện trong ảnh đường phố bằng bounding box 2D. Cần phân biệt đúng đối tượng thật, đúng lớp và phần nhìn thấy. Bỏ sót người hoặc phương tiện có thể làm mô hình không phát hiện được đối tượng liên quan đến an toàn giao thông; box thừa trên tượng hoặc phản chiếu có thể gây phát hiện nhầm.

## 2. Lớp và thuộc tính

### Lớp

Bảng sau diễn giải taxonomy v0 để thành viên kiểm tra. Ví dụ là mô tả khái niệm, chưa phải bằng chứng từ 40 ảnh.

| Lớp | Định nghĩa | Ví dụ mô tả | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con và SUV dùng chủ yếu để chở người. | Sedan, SUV. | Van, xe tải có thùng hàng riêng. |
| `truck` | Xe tải hoặc xe có thùng hàng riêng. | Xe tải thùng, pickup có thùng hàng riêng. | Van có khoang hàng liền thân; tượng hình xe tải. |
| `van` | Xe thùng kín cỡ nhỏ, khoang sau liền thân xe. | Xe van giao hàng. | SUV hoặc xe tải có cabin và thùng riêng. |
| `motorcycle` | Xe máy hoặc mô tô. | Xe máy hai bánh. | Xe đạp; người ngồi trên xe là đối tượng riêng. |
| `person` | Người thật, gồm người đi bộ và người ngồi trên xe. | Người đứng, người lái xe máy. | Ma-nơ-canh, tượng, người trong hình in. |
| `ignore_region` | Vùng bỏ qua việc gán từng vật theo quy tắc đã chốt. | Vùng vật quá nhỏ theo ngưỡng lab. | Không dùng để che một ca khó chưa được quyết định. |

**Cần 101 xác nhận:** cách phân biệt pickup, van và SUV trên ảnh thật. Vật ngoài taxonomy không tự tạo lớp mới; ghi vấn đề để nhóm thống nhất cách xử lý. Không mặc nhiên dùng `ignore_region` cho mọi vật ngoài taxonomy.

### Thuộc tính

- `occluded_level`: các giá trị cố định `0`, `1`, `2`, `3`. Theo v0: không bị che, bị che ít, bị che nhiều, không rõ. **Chưa chốt ngưỡng phần trăm**; 101 cần đề xuất sau khi xem ảnh để nhóm thống nhất. Không tự dùng ngưỡng chưa xác nhận để gán.
- `truncated`: `yes` nếu đối tượng bị mép ảnh cắt mất một phần; `no` nếu không. Che bởi vật khác được ghi ở `occluded_level`.
- `can_review`: bật khi chưa chắc về lớp, box hoặc thuộc tính; ghi lý do để reviewer xử lý.
- `ignore_region` không cần các thuộc tính trên theo hướng dẫn CVAT của bài.

## 3. Quy tắc vẽ box

Các mục sau là **đề xuất để 101 thử trên ảnh và xác nhận**:

| Rule ID | Nội dung đề xuất | Việc cần kiểm tra |
|---|---|---|
| R-01 | Mỗi đối tượng thuộc phạm vi có một box riêng, ôm sát phần nhìn thấy; không tự suy diễn phần bị che. | Thử trên đối tượng bị che và đối tượng có nhiều phần nhìn thấy rời nhau. |
| R-02 | Người đi xe máy: mỗi người một box `person`, xe một box `motorcycle`; không gộp người và xe. | Ghi số người, số xe, số box dự kiến trên ít nhất hai ảnh. |
| R-03 | Đối tượng bị cắt mép: box nằm trong ảnh và đặt `truncated=yes`. | Phân biệt cắt mép với che khuất. |
| R-04 | Không gán lớp đối tượng thật cho tượng, đồ chơi, ma-nơ-canh, hình in hoặc ảnh phản chiếu. | Ghi ID ảnh và bằng chứng nhận biết; ca chưa chắc cần reviewer. |
| R-05 | Với xe chở xe, xem xét từng xe thật có thể phân biệt riêng thay vì gộp toàn bộ thành một box. | Kiểm tra phạm vi nhãn của xe được chở với nhóm. |
| R-06 | Áp dụng ngưỡng vật nhỏ của lab: chiều cao box dưới 20 px thuộc cách xử lý `ignore_region`. | Chốt cách tạo vùng cho một vật hoặc nhiều vật; không bỏ qua tùy ý. |
| R-07 | Chỉ dùng `ignore_region` cho vùng có căn cứ theo guideline; đám đông còn phân biệt được từng người cần quy tắc rõ. | Chốt tiêu chí khi nào không thể gán từng vật. |
| R-08 | Ca không xác định được lớp hoặc chưa vẽ được box phải có dấu vết để review. | Có box: `can_review`; chưa có box: ghi ảnh, vùng, lý do để thu vào `image_issues.csv` sau khi khóa nhãn. |

**Còn cần quyết định:** xe chủ (ego), phương tiện ngoài taxonomy như xe đạp/tuk-tuk, ngưỡng che khuất và ranh giới vùng đông người. Không coi các mục còn mở là quy tắc đã khóa.

## 4. Ca khó

Chưa điền ca thực tế vì chưa có kết quả quan sát và quyết định của 101. Không dùng ID ảnh minh họa thay cho ảnh đã xem.

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|

Khi xem đủ 40 ảnh, bổ sung các ca người đi xe máy, xe chở xe, người bị che, vật nhỏ, tượng và phản chiếu nếu có. Với mỗi ca, ghi rõ số box dự kiến, lớp, phần được bao và thuộc tính liên quan. Phân biệt ca loại theo quy tắc với ca cần reviewer.

## 5. QC và phiên bản

### Ngưỡng giữ nguyên theo lab

- Vật nhỏ: chiều cao box dưới 20 px.
- So sánh box: chiều cao từ 50 px dùng ngưỡng IoU 0.85; dưới 50 px dùng 0.7.
- Ghép box 1–1 để đo IAA: IoU tối thiểu 0.5.
- IAA mục tiêu: IoU trung bình ít nhất 0.85; đồng thuận lớp ít nhất 95%, chỉ tính trên các cặp ghép được. Không có cặp thì ghi N/A.
- Phải đủ lượt gán theo phân công trước khi đo IAA. Báo riêng box chưa ghép được.
- Release yêu cầu review toàn lô, tỉ lệ ảnh lỗi không quá 2% và reviewer xác nhận; lô 40 ảnh có một ảnh lỗi là 2.5%, vượt ngưỡng.
- Báo cả tỉ lệ lỗi theo ảnh và theo đối tượng, kèm tử số/mẫu số. Giữ số liệu IAA trước calibration.

### Người chốt và sử dụng AI

- Quy tắc cá nhân do 101 tự xác nhận sau khi xem ảnh; quy tắc nhóm do các thành viên thống nhất ở Pha 2.
- Ca calibration phải ghi người chốt, quy tắc, vị trí sửa và kết quả kiểm lại. Chưa có quyết định hoặc bằng chứng thì để mở, không tạo kết quả QC.
- Bản nháp này được AI hỗ trợ diễn giải cấu trúc và nội dung; 101 cần ghi cách kiểm lại thực tế trong ghi chú cá nhân. Chưa ghi nhận việc kiểm lại đã hoàn tất.

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v0.1-draft | Tạo khung cá nhân, diễn giải taxonomy v0 và liệt kê quy tắc đề xuất. | Chuẩn bị xem ảnh; chưa phải bản cá nhân hoàn tất. |
