# Guideline gán nhãn cá nhân — v1.0 — 101

- Phiên bản: v1.0 (cá nhân)
- Ngày: 2026-11-16
- Thành viên: 101 — nhóm Phoenix
- Người viết: 101
- Phạm vi: bộ 40 ảnh `data/images/img_001.jpg` đến `img_040.jpg`; bounding box 2D; CVAT, xuất COCO 1.0.
- Đã xem: 40/40 ảnh.

## 1. Mục tiêu

Gán nhãn người và phương tiện trong ảnh đường phố để huấn luyện mô hình phát hiện đối tượng. Mỗi vật được vẽ một box, chọn lớp và ghi mức bị che, bị cắt ở mép ảnh.

Cần tránh bỏ sót người, gộp người với xe hoặc nhầm tượng thành vật thật. Bỏ sót người và xe có thể làm mô hình không phát hiện được nguy cơ trên đường; gán tượng hoặc phản chiếu lại dễ gây báo nhầm.

Gán cả người và xe đang di chuyển, đứng yên, đỗ hoặc được chở trên xe khác. Vật bị che hoàn toàn thì không tự đoán để thêm box.

## 2. Lớp và thuộc tính

### 2.1. Lớp

Mỗi vật chỉ chọn một lớp. Riêng `ignore_region` dùng để đánh dấu vùng bỏ qua, không tính là một người hay một xe.

| Lớp | Định nghĩa | Ví dụ | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con, SUV hoặc station wagon có thân xe chở người, không có thùng hàng riêng kiểu pickup. | Ô tô con trên xe chở xe ở img_002. | Pickup thuộc `truck`; van thuộc `van`; mô hình xe bị loại. |
| `truck` | Xe tải có cabin và phần chở hàng riêng, gồm pickup và xe đầu kéo gắn rơ-moóc chở hàng. | Xe chở xe ở img_002; pickup có nắp thùng ở img_037. | Van có khoang sau liền thân; tượng pickup ở img_027. |
| `van` | Xe có khoang kín liền thân phía sau cabin, dạng van chở hàng hoặc chở người, không có thùng pickup riêng. | Xe van màu bạc phía sau nhóm người ở img_005. | Pickup gắn nắp thùng; bus/minibus không rõ cấu trúc cần reviewer. |
| `motorcycle` | Xe máy hoặc mô tô, gồm scooter và mô tô có sidecar gắn liền. | Xe máy chở hai người ở img_026. | Xe đạp, xích lô, tuk-tuk không tự chuyển thành `motorcycle`. |
| `person` | Người thật có phần cơ thể nhìn thấy và nhận diện được, kể cả người đi bộ, người lái hoặc hành khách. | Hai người trên xe ở img_026. | Tượng, ma-nơ-canh, hình người in và phản chiếu. |
| `ignore_region` | Vùng giới hạn nơi không đánh giá việc gán từng vật theo quy tắc vật nhỏ hoặc đám đông không thể tách. | Người áo đỏ ở xa trong img_025, cao khoảng 17 px. | Không dùng để che mọi ca chưa chắc hoặc toàn bộ phương tiện ngoài taxonomy. |

**Thứ tự phân biệt xe:** có thùng hàng riêng hoặc khung pickup nhận diện được → `truck`; thân ô tô con/SUV → `car`; thân van kín liền khoang sau → `van`. Không chọn lớp chỉ theo màu sơn, logo hoặc việc xe đang chở hàng.

**Ngoài taxonomy:** xe đạp, xích lô, tuk-tuk và bus không được ép sang lớp gần giống. Khi xác định rõ ngoài phạm vi thì không gán box phương tiện, nhưng vẫn gán người thật thuộc phạm vi. Trường hợp ranh giới van/minibus chưa rõ phải chuyển reviewer; không tự thêm lớp.

### 2.2. Thuộc tính

Áp dụng cho năm lớp vật thể; `ignore_region` không cần thuộc tính. Mức che khuất được chia như sau. Giữ nguyên các ngưỡng QC trong `taxonomy.json`.

#### `occluded_level`

Ước lượng tỉ lệ phần đối tượng bị **vật khác che** so với hình dạng đầy đủ dự kiến trong tư thế hiện tại. Không tính phần ngoài khung ảnh, phần khuất tự nhiên do góc nhìn hoặc mặt sau đối tượng. Đây là đánh giá bằng mắt, không phải phép đo chính xác diện tích.

| Giá trị | Quy tắc |
|---|---|
| `0` | Không bị vật khác che (0%). |
| `1` | Bị che trên 0% và không quá 30%. |
| `2` | Bị che trên 30%, nhưng còn đủ dấu hiệu nhận diện đối tượng. |
| `3` | Biết là người hoặc xe nhưng không ước lượng được phần bị che, chẳng hạn nhiều vật chồng lên nhau. Bật `can_review`. |

Không dùng mức `3` thay cho "bị che nhiều". Đối tượng hoàn toàn không thấy không được tạo box giả. Trường hợp ước lượng sát mốc 30% và không chốt được thì bật `can_review`.

#### `truncated`

- `yes`: hình thể đối tượng bị biên ảnh cắt mất một phần.
- `no`: không có bằng chứng bị cắt; box chỉ chạm mép nhưng vật vẫn đầy đủ không tự động là `yes`.
- Một đối tượng có thể vừa bị che vừa bị cắt: ghi cả hai thuộc tính độc lập.

#### `can_review`

- Bật khi chưa chắc lớp, hình thể, mức che hoặc cách áp dụng quy tắc.
- Không bật cho ca đã xác định rõ bị loại theo quy tắc.
- Cờ này không thay thế việc ghi lý do. Nếu chưa có box hợp lệ thì ghi vấn đề cấp ảnh thay vì tạo box tùy ý.

## 3. Quy tắc vẽ box

### R-01 — Box theo phần nhìn thấy

Vẽ box ngang/dọc ôm sát các phần nhìn thấy của vật. Không chừa lề và không kéo box ra phần bị che. Bóng đổ, khói, ánh đèn và phản chiếu không nằm trong hình thể đối tượng.

Nếu một vật lộ nhiều mảng rời nhau do bị che, vẫn dùng một box bao tất cả các mảng thuộc cùng vật. Box có thể chứa khoảng trống hoặc vật che ở giữa; không tách một vật thành nhiều box. Không dùng chung box cho nhiều đối tượng.

### R-02 — Người và xe là các đối tượng riêng

Mỗi người thật một box `person`, mỗi xe một box đúng lớp. Người lái và hành khách không được gộp vào box xe. Với một xe máy chở hai người nhận diện rõ và đủ kích thước: gán ba box gồm hai `person` và một `motorcycle`.

Box người gồm cơ thể, tóc, quần áo, mũ bảo hiểm và đồ đeo sát người; không kéo sang tay lái hoặc hàng hóa trên xe. Box xe gồm thân xe, bánh, gương và bộ phận gắn cố định nhìn thấy; không kéo sang người hoặc kiện hàng rời. Người và xe có thể có box chồng nhau.

### R-03 — Che khuất và cắt mép

Giữ nguyên nguyên tắc phần nhìn thấy của R-01; gán `occluded_level` theo phần bị vật khác che. Cắt box tại biên ảnh khi vật bị cắt, đặt `truncated=yes`, không đặt tọa độ ngoài ảnh. Chỉ gán nếu phần nhìn thấy đủ nhận diện là một đối tượng; nếu không biết phần nhìn thấy thuộc vật nào thì ghi lại để reviewer kiểm tra.

### R-04 — Vật không thật và phản chiếu

Không gán lớp vật thể cho tượng, ma-nơ-canh, đồ chơi, mô hình, người/xe trong hình in, màn hình hoặc gương. Không tạo `ignore_region` chỉ vì đó là vật không thật.

Vật thật nhìn xuyên qua kính vẫn có thể được gán nếu đủ nhận diện và kích thước; cần phân biệt với ảnh phản chiếu trên kính. Người mặc trang phục hóa trang vẫn là `person` khi có căn cứ là người thật. Nếu chưa phân biệt được người với tượng hoặc xe thật với mô hình, chuyển reviewer thay vì đoán.

### R-05 — Xe chở xe và các phần gắn liền

Gán riêng mỗi xe thật được chở nếu nhận diện được và đạt ngưỡng kích thước. Xe vận chuyển có một box `truck` bao phần nhìn thấy của đầu kéo và rơ-moóc gắn liền; không mở rộng box chỉ để chứa các xe được chở nhô ra ngoài kết cấu của nó.

Xe đứng yên, xe trưng bày thật hoặc xe có vật trang trí vẫn được gán theo cấu trúc phương tiện; không tự loại vì không chạy. Không tạo box riêng cho nắp thùng pickup, sidecar hoặc bộ phận gắn liền. Ca rơ-moóc tách rời hoặc không xác định được quan hệ gắn liền chuyển reviewer.

### R-06 — Vật nhỏ

Đo trên độ phân giải ảnh gốc, không dùng kích thước sau khi zoom màn hình. Chiều cao box phần nhìn thấy dưới 20 px: không gán lớp vật thể riêng, tạo `ignore_region` ôm vùng đó. Chiều cao đúng 20 px trở lên: gán bình thường nếu nhận diện được.

Ưu tiên một vùng nhỏ cho từng vật hoặc cụm vật nhỏ liên tục; không gộp các cụm xa nhau bằng một vùng lớn. Không để vùng ignore phủ lên người hoặc xe lớn vẫn gán riêng được. Chưa xác định được lớp vì quá nhỏ thì vẫn cần căn cứ đó là vùng đối tượng trong phạm vi; không đánh dấu mọi chi tiết ảnh nhỏ là ignore.

### R-07 — Đám đông

Vẫn gán từng người nếu phân biệt được hình thể và đạt ngưỡng. Chỉ dùng `ignore_region` cho cụm người vẫn không tách được từng người sau khi phóng to. Giới hạn vùng theo cụm không tách được, giữ các đối tượng rõ ở ngoài vùng. Ghi lý do để reviewer kiểm tra lại. Không vẽ một box `person` chung cho cả đám đông.

### R-08 — Ca chưa chắc và vấn đề cấp ảnh

Có thể tạo box hợp lý nhưng còn nghi ngờ: bật `can_review`, ghi ảnh, vùng và điểm chưa chắc. Không tạo được box/lớp có căn cứ: ghi vấn đề cấp ảnh với `image,annotator,region,note` để nhóm tập hợp vào `submission/qc/image_issues.csv` sau khi khóa nhãn.

Ca loại theo quy tắc phải ghi được lý do và mã R tương ứng. Ca chưa chắc thì để reviewer kiểm tra, không tự cho vào ignore. Khi gán độc lập, không xem nhãn của người khác để hỏi cách làm.

### R-09 — Xe chủ và vật ngoài phạm vi

Loại phần xe gắn camera (ego) khi có bằng chứng rõ là xe chủ, gồm mép nắp ca-pô hoặc gương của chính xe chụp. Không loại mọi xe ở tiền cảnh chỉ vì gần máy ảnh. Không đủ căn cứ xác định ego thì chuyển reviewer.

Xe ngoài bộ lớp thì không vẽ box cho xe, cũng không thêm ignore chỉ vì thiếu lớp. Người thật trên hoặc cạnh xe đó vẫn xét theo R-02, R-03 và R-06.

### R-10 — Phân biệt pickup, van và xe chở khách

Pickup vẫn là `truck` khi gắn nắp thùng, nếu còn thấy cấu trúc cabin và thùng hàng riêng. Van có khoang sau liền thân. Xe có hình dáng bus, hoặc trường hợp van/minibus không đủ dấu hiệu phân biệt, chuyển reviewer để chốt ranh giới phạm vi; không dựa vào tên gọi trong nguồn ảnh để thay quan sát.

## 4. Ca khó và ví dụ áp dụng

Số box trong bảng áp dụng cho đối tượng/vùng được mô tả, không phải tổng số box của cả ảnh. Khi gán nhãn, kiểm chiều cao box trên ảnh gốc theo ngưỡng 20 px.

| Ảnh | Tình huống | Rule ID | Cách xử lý | Lý do |
|---|---|---|---|---|
| [img_002](../../../data/images/img_002.jpg) | Xe tải đang chở nhiều ô tô thật trên hai tầng. | R-01, R-05 | Một `truck` cho xe vận chuyển; mỗi ô tô nhận diện riêng được xét một `car`, sau khi kiểm ngưỡng 20 px. | Xe được chở vẫn là đối tượng thật độc lập; không gộp xe tải và tất cả hàng thành một box. |
| [img_005](../../../data/images/img_005.jpg) | Nhiều người đi xe máy chồng lấp; người tiền cảnh bị cắt ở mép dưới. | R-02, R-03, R-08 | Tách từng người và xe nhận diện được; box theo phần nhìn thấy; người bị mép ảnh cắt đặt `truncated=yes`; ca khó tách bật review. | Tránh gộp người và xe; không suy diễn chân hoặc phần xe bị khuất. |
| [img_009](../../../data/images/img_009.jpg) | Xe chở khách màu sáng thân dài, nhiều cửa sổ giữa ảnh. | R-08, R-10 | Cần reviewer xác định van hay ngoài taxonomy; chưa tự gán `van` hoặc tạo vùng ignore. | Ranh giới van/minibus/bus chưa đủ rõ để kết luận chỉ bằng tên lớp. |
| [img_025](../../../data/images/img_025.jpg) | Người mặc áo đỏ ở xa, gần giữa lối đi, phía sau người mặc áo đen tiền cảnh. Trên ảnh gốc 960 × 639 px, vùng quan sát xấp xỉ `(573, 354)` đến `(582, 371)`, cao khoảng 17 px. | R-06 | Dùng `ignore_region` ôm sát người này, không gán `person` riêng. Kiểm lại biên khi vẽ trên ảnh gốc. | Người này nhỏ hơn 20 px nên xử lý theo R-06, không phải vì chưa biết chọn lớp nào. |
| [img_026](../../../data/images/img_026.jpg) | Một xe máy chở hai người ở tiền cảnh. | R-01, R-02, R-03 | Gán ba box cho nhóm này: hai `person`, một `motorcycle`; phần xe ra khỏi mép dưới được xét truncated. | Một phương tiện và hai người là ba đối tượng; box người không chứa toàn bộ xe. |
| [img_027](../../../data/images/img_027.jpg) | Vật hình pickup trên bệ trong nghĩa trang, có bề mặt và cấu trúc như tượng; nguồn ghi là monument. | R-04 | Loại tượng khỏi nhãn `truck`, không tạo ignore chỉ cho tượng. | Đây là tượng hình xe, không phải phương tiện thật; đối chiếu hình ảnh và nguồn ghi công. |
| [img_033](../../../data/images/img_033.jpg) | Ma-nơ-canh trong cửa kính, kèm các lớp hình ảnh trên kính. | R-04, R-08 | Không gán `person` cho ma-nơ-canh hoặc hình in/phản chiếu; ca không phân biệt được người thật chuyển reviewer. | Có hình người không đồng nghĩa với người thật hiện diện trực tiếp. |
| [img_037](../../../data/images/img_037.jpg) | Pickup trắng gắn nắp thùng cao phía sau cabin. | R-05, R-10 | Gán một `truck` cho pickup, bao cả nắp thùng gắn liền; không đổi thành van chỉ vì có khoang kín. | Cấu trúc pickup còn nhận diện được dù thùng đã được che kín. |

Quy tắc tách người và xe áp dụng cho cả img_005 và img_026. Với img_005 phải tách các phần bị che theo R-03; img_026 có hai người trên một xe nên vẽ ba box cho nhóm đó. Xe tải ở img_002 và pickup gắn nắp thùng ở img_037 đều thuộc `truck`, dù hình dáng khác nhau.

## 5. QC và phiên bản

### 5.1. Ngưỡng giữ nguyên của lab

| Hạng mục | Ngưỡng / cách tính |
|---|---|
| Vật nhỏ | Chiều cao box dưới 20 px. |
| So sánh box lớn | Chiều cao từ 50 px: IoU 0.85. |
| So sánh box nhỏ | Chiều cao dưới 50 px: IoU 0.7. |
| Ghép box IAA | Ghép 1–1, IoU tối thiểu 0.5, không phân biệt lớp khi ghép. |
| IAA mục tiêu | IoU trung bình ít nhất 0.85 và đồng thuận lớp ít nhất 95%, trên các cặp ghép được. |
| Độ phủ | Đủ 100% lượt gán hợp lệ theo phân công trước khi đo IAA. |
| Release | Review toàn lô, tỉ lệ ảnh lỗi không quá 2%, không có lỗi lặp theo quy tắc lab và reviewer xác nhận đủ bằng chứng. |

IoU bằng diện tích giao chia diện tích hợp của hai box. Không có cặp ghép thì báo N/A; báo riêng box không ghép được và bất đồng ignore. Đồng thuận cao không chứng minh nhãn đúng: phải đối chiếu quy tắc, ảnh và ca tham chiếu của coach.

Tỉ lệ lỗi ảnh = số ảnh có ít nhất một lỗi / số ảnh đã duyệt. Tỉ lệ lỗi đối tượng = số annotation đối tượng lỗi / số annotation đã duyệt, theo quy định đếm của lab. Báo tử số/mẫu số và ghi rõ lỗi bỏ sót theo phiếu QC. Với 40 ảnh, một ảnh lỗi là 2.5%, vượt ngưỡng 2%. Thiếu bằng chứng hoặc chỉ review mẫu: Hold; vượt ngưỡng hoặc lỗi lặp: Rework; Release cần reviewer xác nhận.

### 5.2. Cách kiểm và xử lý ca chưa rõ

1. Bản cá nhân được gửi riêng theo hướng dẫn của coach trước khi xem bản của các thành viên khác.
2. Nhóm thảo luận và khóa `guideline_v1.0_agreed.md` cùng taxonomy v1.0 trước khi gán độc lập. Không chỉnh `rules` trong taxonomy.
3. Mỗi người gán và export COCO riêng; kiểm bằng `validate_labels.py`. Ảnh không có đối tượng vẫn phải có trong COCO.
4. Sau khi khóa nhãn, chạy IAA và xem ảnh chồng box. Calibration ghi từng ca, mã rule, quyết định, người chốt, vị trí sửa và kết quả kiểm lại.
5. Lưu guideline v1.1 và `guideline_change.json`. Đổi ý nghĩa quy tắc là `semantic`, cần phạm vi cả lô và rà lại lô; sửa câu chữ không đổi nhãn là `editorial`.
6. Reviewer nên khác người sửa; nhóm hai người đổi vai và ghi rõ. Giữ các vòng QC cũ và IAA trước calibration; không ghi kết quả kiểm chưa làm.

Theo phân công hiện tại, 101 điều phối và 102 review bản cuối. Các ca chưa rõ được ghi lại để trao đổi với nhóm hoặc hỏi coach.

### 5.3. Lịch sử phiên bản

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 cá nhân | Bổ sung định nghĩa lớp, mức che khuất và các quy tắc R-01 đến R-10 từ bản v0. | Làm rõ các ca như người đi xe máy (img_026), xe chở xe (img_002) và vật nhỏ (img_025). |
