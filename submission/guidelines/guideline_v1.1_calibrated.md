# Guideline nhóm Phoenix — v1.1 (101 xác nhận chung)

- Thành viên: 101, 102.
- Ngày: 2026-11-16.
- Phạm vi: 40 ảnh đường phố; bounding box 2D; CVAT, xuất COCO 1.0.
- Trạng thái: bản giải thích biên tập do AI hỗ trợ ngày 2026-11-17; người dùng (101 theo ngữ cảnh job20) xác nhận chung bản hiện tại qua hội thoại. Chưa có xác nhận riêng của 102. v1.0 vẫn là bản đã dùng khi gán độc lập.
- Đối chiếu hai bản cá nhân và lý do chọn quy tắc: [phase2_comparison.md](phase2_comparison.md).

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
| `van` | Xe dạng van có khoang kín liền thân phía sau cabin, dùng chở hàng hoặc chở người; không có thùng pickup riêng, không phải thân bus chuyên chở khách. | Xe van màu bạc phía sau nhóm người ở img_005. | Pickup gắn nắp thùng; bus/minibus không rõ cấu trúc cần reviewer. |
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

Box người gồm cơ thể, tóc, quần áo, mũ bảo hiểm và đồ đeo sát người; không kéo sang tay lái hoặc hàng hóa trên xe. Box xe gồm thân xe, bánh, gương và bộ phận gắn cố định nhìn thấy; không kéo sang người hoặc kiện hàng rời. Với hàng chất cao hoặc nhô ra ngoài, chỉ bao kết cấu xe, không bao phần hàng vượt ra ngoài. Người và xe có thể có box chồng nhau.

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

Pickup vẫn là `truck` khi gắn nắp thùng, nếu còn thấy cấu trúc cabin và thùng hàng riêng. Van có khoang sau liền thân. Bus có thân xe chuyên chở khách, khoang khách cao/dài và cửa lên xuống riêng được loại khỏi phạm vi bộ lớp này. Nếu không phân biệt được van với minibus bằng cấu trúc nhìn thấy, chuyển reviewer theo R-08; không dựa vào tên gọi trong nguồn ảnh để thay quan sát.

## 4. Ca khó và ví dụ áp dụng

Số box trong bảng áp dụng cho đối tượng/vùng được mô tả, không phải tổng số box của cả ảnh. Khi gán nhãn, kiểm chiều cao box trên ảnh gốc theo ngưỡng 20 px.

| Ảnh | Tình huống | Rule ID | Cách xử lý | Lý do |
|---|---|---|---|---|
| [img_002](../../data/images/img_002.jpg) | Xe tải đang chở nhiều ô tô thật trên hai tầng. | R-01, R-05 | Một `truck` cho xe vận chuyển; mỗi ô tô nhận diện riêng được xét một `car`, sau khi kiểm ngưỡng 20 px. | Xe được chở vẫn là đối tượng thật độc lập; không gộp xe tải và tất cả hàng thành một box. |
| [img_005](../../data/images/img_005.jpg) | Nhiều người đi xe máy chồng lấp; người tiền cảnh bị cắt ở mép dưới. | R-02, R-03, R-08 | Tách từng người và xe nhận diện được; box theo phần nhìn thấy; người bị mép ảnh cắt đặt `truncated=yes`; ca khó tách bật review. | Tránh gộp người và xe; không suy diễn chân hoặc phần xe bị khuất. |
| [img_009](../../data/images/img_009.jpg) | Xe chở khách màu sáng thân dài, nhiều cửa sổ giữa ảnh. | R-08, R-10 | Chuyển reviewer theo R-08 để xác định van hay minibus ngoài phạm vi; không ép sang `van` hoặc tạo ignore chỉ vì chưa rõ lớp. | Ranh giới van/minibus/bus chưa đủ rõ để kết luận chỉ bằng tên lớp. |
| [img_025](../../data/images/img_025.jpg) | Người mặc áo đỏ ở xa, gần giữa lối đi, phía sau người mặc áo đen tiền cảnh. Trên ảnh gốc 960 × 639 px, vùng quan sát xấp xỉ `(573, 354)` đến `(582, 371)`, cao khoảng 17 px. | R-06 | Dùng `ignore_region` ôm sát người này, không gán `person` riêng. Kiểm lại biên khi vẽ trên ảnh gốc. | Người này nhỏ hơn 20 px nên xử lý theo R-06, không phải vì chưa biết chọn lớp nào. |
| [img_026](../../data/images/img_026.jpg) | Một xe máy chở hai người ở tiền cảnh. | R-01, R-02, R-03 | Gán ba box cho nhóm này: hai `person`, một `motorcycle`; phần xe ra khỏi mép dưới được xét truncated. | Một phương tiện và hai người là ba đối tượng; box người không chứa toàn bộ xe. |
| [img_027](../../data/images/img_027.jpg) | Vật hình pickup trên bệ trong nghĩa trang, có bề mặt và cấu trúc như tượng; nguồn ghi là monument. | R-04 | Loại tượng khỏi nhãn `truck`, không tạo ignore chỉ cho tượng. | Đây là tượng hình xe, không phải phương tiện thật; đối chiếu hình ảnh và nguồn ghi công. |
| [img_033](../../data/images/img_033.jpg) | Ma-nơ-canh trong cửa kính, kèm các lớp hình ảnh trên kính. | R-04, R-08 | Không gán `person` cho ma-nơ-canh hoặc hình in/phản chiếu; ca không phân biệt được người thật chuyển reviewer. | Có hình người không đồng nghĩa với người thật hiện diện trực tiếp. |
| [img_037](../../data/images/img_037.jpg) | Pickup trắng gắn nắp thùng cao phía sau cabin. | R-05, R-10 | Gán một `truck` cho pickup, bao cả nắp thùng gắn liền; không đổi thành van chỉ vì có khoang kín. | Cấu trúc pickup còn nhận diện được dù thùng đã được che kín. |

| [img_011](../../data/images/img_011.jpg) | Nhân vật Predator ở tiền cảnh và các xe trang trí. | R-04, R-08 | Không kết luận là tượng chỉ từ ngoại hình. Nếu chưa phân biệt được người hóa trang với vật trưng bày, ghi vấn đề cấp ảnh để reviewer kiểm tra; xét xe thật/mô hình theo cùng nguyên tắc. | Người hóa trang vẫn thuộc `person`; tượng và mô hình thì bị loại. |
| [img_020](../../data/images/img_020.jpg) | Xe và cảnh đường phản chiếu trong gương chiếu hậu; gương thuộc xe chụp ảnh. | R-04, R-09 | Không gán vật trong mặt gương; không gán phần gương/xe chủ đã xác định. | Không tạo bản sao đối tượng từ ảnh phản chiếu; xe chủ ngoài phạm vi. |
| [img_032](../../data/images/img_032.jpg) | Người chờ xe, ô tô và hình người in trên quảng cáo ở bến chờ. | R-01, R-03, R-04, R-07 | Gán từng người thật và xe thuộc bộ lớp nếu đủ kích thước; loại hình in. Không thêm bus theo tên ảnh và không ignore cả hàng người còn tách được. | Quyết định dựa trên vật nhìn thấy trong ảnh, không dựa vào tên bến xe. |

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

### 5.2. Quyết định và áp dụng

Bản v1.0 được chốt từ hai bản cá nhân theo các lựa chọn và lý do trong `phase2_comparison.md`. Các mã R trong bản nhóm là mã dùng chung; không suy ra ý nghĩa từ mã R ở bản cá nhân 102 vì cách đánh số khác nhau.

- Giữ sáu nhãn ban đầu, không thêm `tuk_tuk` hoặc `bus` cho lượt gán này.
- Giữ nguyên bộ giá trị thuộc tính và toàn bộ ngưỡng trong `taxonomy.json`.
- img_026 là ca kiểm cách hiểu: hai `person` và một `motorcycle` cho nhóm ở tiền cảnh; vẽ theo phần nhìn thấy, không gộp người với xe.
- Ca chưa chắc có box thì bật `can_review`. Chưa có box có căn cứ thì ghi ảnh, vùng và lý do; sau khi khóa nhãn mới tập hợp `image_issues.csv` để calibration. Không xem nhãn của nhau trong lượt độc lập.
- 101 điều phối, 102 review bản cuối theo phân công hiện tại. Khi một người trực tiếp sửa, người còn lại kiểm lại và ghi kết quả. Ca chưa rõ được đưa ra ở calibration hoặc hỏi coach.
- Không sửa âm thầm quy tắc trong lúc gán. Ghi vấn đề và giữ bản độc lập; thay đổi sau calibration được lưu ở guideline v1.1 cùng phạm vi rà lại.

### 5.3. Phiên bản

| Phiên bản | Nội dung | Lý do |
|---|---|---|
| v1.0 | Chốt định nghĩa lớp, thuộc tính, R-01 đến R-10 và phạm vi gán từ hai bản cá nhân. | Làm rõ pickup, mức che, cắt mép, xe chở xe và vật ngoài taxonomy trước lượt gán độc lập. |


## 6. Làm rõ sau so sánh — đề xuất biên tập v1.1

Không đổi lớp, thuộc tính, ngưỡng, hoặc nghĩa quy tắc R-01 đến R-10. Phần này nhắc lại cách áp dụng v1.0; người dùng đã xác nhận chung bản hiện tại; chưa có bằng chứng họp hoặc phê duyệt riêng của 102.

- Mỗi người và mỗi xe là một vật riêng (R-01/R-02). Một vật lộ nhiều mảng vẫn dùng một box. Không cộng hai box của hai người gán thành hai vật mới chỉ vì chúng không ghép được ở IoU 0.5.
- Xe đạp, xích lô, tuk-tuk và bus ngoài taxonomy (R-09/R-10). Người thật trên xe vẫn xét riêng. Xe tải chở xe, pickup có nắp thùng và xe có sidecar theo R-05/R-10.
- Đồ chơi, tượng, ma-nơ-canh, biển quảng cáo và phản chiếu không phải đối tượng thật hiện diện trực tiếp (R-04). Ca xe thật/mô hình chưa rõ ở img_011 và img_024 vẫn chờ xác nhận, không tự coi đã xử lý.
- Kiểm chiều cao phần nhìn thấy trên ảnh gốc: dưới 20 px dùng ignore nhỏ (R-06). Không kéo box đầu người xuống phần bị xe che; cũng không bỏ qua các mảng thân người nhìn thấy thuộc cùng người.
- Mức 3 là không ước lượng được phần bị che, cần can_review=true (R-03/R-08). Bất đồng occluded không được giải bằng sao chép toàn bộ thuộc tính của một phía mà không xem ảnh.
- truncated=yes cần bằng chứng vật bị biên ảnh cắt (R-03). Box nằm hẳn bên trong ảnh không cung cấp bằng chứng đó; ca sửa biên vẫn phải kiểm lại.
- Người dùng báo đã kiểm và không còn lỗi; 860 dòng được ghi nhận theo xác nhận chung của 101, không khai là đã có biên bản riêng cho từng ca hay xác nhận của 102. Nhãn gốc 101/102 được bảo toàn. Các mức che 3 và vấn đề cấp ảnh chưa có kết luận chi tiết vẫn giữ trạng thái review theo R-03/R-08.

Danh sách ca còn mở: [calibration_open_issues.csv](../qc/calibration_open_issues.csv). Bằng chứng và giới hạn: [AI_CALIBRATION_PROVENANCE.md](../qc/AI_CALIBRATION_PROVENANCE.md).
