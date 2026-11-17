# Pha 2 — So sánh guideline nhóm Phoenix

- Thành viên: 101, 102.
- Ngày: 2026-11-16.
- Đầu vào: `individual/guideline_101.md`, `individual/guideline_102.md`.
- Trạng thái: phương án đối chiếu được tổng hợp trong `guideline_v1.0_agreed.md`; bảng dưới ghi lựa chọn và lý do.

## 1. Các điểm giống nhau

- Vẽ box ôm phần nhìn thấy, không đoán phần bị che.
- Người và xe máy có box riêng; mỗi hành khách cũng có box riêng.
- Xe chở xe có box xe vận chuyển và box cho từng ô tô được chở.
- Không đưa hàng hóa rời nhô ra ngoài vào box phương tiện.
- Không gán người/xe thật cho tượng, ma-nơ-canh, đồ chơi, hình in hoặc phản chiếu.
- Vật dưới 20 px không gán lớp vật thể riêng; dùng vùng ignore phù hợp.
- Ca chưa chắc cần dấu vết để reviewer kiểm tra; ảnh trống vẫn có trong COCO.

## 2. Điểm khác nhau và quyết định

| Vấn đề | Bản 101 | Bản 102 | Quyết định v1.0 | Lý do |
|---|---|---|---|---|
| Pickup không có nắp thùng | `truck`. | `car`; pickup có nắp cao lại thuộc `truck`. | Dùng `truck` cho pickup, kể cả có nắp thùng. | Hợp định nghĩa v0 "xe có thùng hàng riêng"; không đổi lớp chỉ vì lắp nắp. Đối chiếu img_037. |
| `occluded_level=3` | Không xác định được mức che; bật review. | Bị che trên 70%. | Giữ `3` là không rõ; `2` là che trên 30% nhưng vẫn nhận diện được. | Khớp ý nghĩa mức `3` của v0; mức che nhiều và không biết mức che là hai tình huống khác nhau. |
| Cắt mép và che khuất | Tách riêng: mép ảnh ghi `truncated`. | Tính cả mép ảnh vào `occluded_level`. | Cắt mép chỉ tính vào `truncated`; vật khác che mới tính vào `occluded_level`. | Tránh tính cùng một hiện tượng hai lần. Đối chiếu img_005. |
| Thêm `tuk_tuk`/`bus` | Giữ bộ lớp ban đầu, không ép xe ngoài phạm vi vào lớp khác. | Đề nghị thêm `tuk_tuk`, có nêu các ca bus. | Giữ sáu nhãn của starter cho lượt đầu; tuk-tuk/bus ngoài phạm vi, ca van/minibus chưa rõ chuyển reviewer theo R-08. | Giới hạn phạm vi nhất quán cho lượt đầu; không ép phương tiện thiếu lớp sang một lớp khác. |
| Ô tô bị che trên xe chở xe | Nhận diện được và cao từ 20 px thì xét gán. | Chỉ gán nếu thấy ít nhất 30% thân xe. | Không thêm ngưỡng 30% nhìn thấy; dùng khả năng nhận diện và ngưỡng 20 px, chưa rõ thì review. | Tránh bỏ qua xe nhận diện được chỉ vì bị che nhiều. Đối chiếu img_002. |
| Người đi xe máy | Một xe chở hai người là ba box. | Tiêu đề "bắt buộc 2 box" nhưng nội dung lại nói mỗi người một box. | Viết "một box cho xe, một box cho mỗi người"; img_026 có ba box cho nhóm hai người và một xe. | Bỏ cách hiểu luôn chỉ có hai box dù có hành khách. |
| Thuộc tính vùng ignore | Ignore không cần ba thuộc tính. | Viết mọi box bắt buộc có ba thuộc tính. | Ba thuộc tính chỉ áp dụng cho các lớp vật thể; ignore không cần. | Khớp hướng dẫn cấu hình CVAT của lab. |
| QC: IoU | Tách ngưỡng ghép box, ngưỡng theo kích thước và IAA trung bình. | Mô tả IoU trung bình như ngưỡng thay đổi theo kích thước. | Ghép box từ 0.5; so sánh lớn/nhỏ 0.85/0.7; IAA trung bình mục tiêu 0.85, đồng thuận lớp 95%. | Đây là ba mục khác nhau; giữ nguyên mục `rules` trong taxonomy. |

## 3. Cách xử lý các ca dễ nhầm

- **img_011:** bản 102 kết luận nhân vật Predator là tượng. Chỉ từ hình dạng trong ảnh chưa đủ chốt tượng hay người hóa trang; nguồn ghi công cũng dùng từ "costumed character". Nếu xác định người thật thì gán `person`; nếu xác định vật trưng bày thì loại; chưa đủ căn cứ thì reviewer quyết. Không suy ra mọi nhân vật giống tượng đều phải loại. Các xe trang trí ở đây cũng cần xem là xe thật hay mô hình.
- **img_032:** ảnh đã đối chiếu có người chờ xe, ô tô và hình người in trên quảng cáo. Không tự thêm bus theo tên ảnh. Người thật được xét riêng; hình người trên quảng cáo bị loại theo quy tắc.
- **img_002:** bản 102 nêu tám xe và ngưỡng nhìn thấy 30%. Khi gán cần kiểm từng xe thực tế, tránh lấy một con số có sẵn làm nhãn đúng hoặc bỏ xe theo ngưỡng chưa thống nhất.
- **Ảnh trống:** quyết định không gán tượng hoặc phản chiếu chỉ áp dụng cho vật đó. Muốn kết luận cả ảnh có 0 annotation phải rà toàn ảnh, kể cả vật nhỏ hoặc đối tượng khác.

Không sửa hai bản cá nhân; giữ nguyên để đối chiếu quyết định ban đầu.

## 4. Bước tiếp theo

1. Đã lưu bản khóa `guideline_v1.0_agreed.md` và taxonomy v1.0; các bản cá nhân giữ nguyên.
2. Sinh cấu hình nhãn bằng `python scripts/make_cvat_labels.py`.
3. Cả hai đọc cùng bản khóa và kiểm cách hiểu trên img_026 trước khi vẽ.
4. Sang Pha 3, chạy `python scripts/split_assign.py --members 101,102 --copy-images`.
5. Tạo Project CVAT từ `cvat/labels_generated.json`; mỗi thành viên một Task riêng với ảnh của mình. Nhóm hai người: mỗi người gán cả 40 ảnh độc lập.
