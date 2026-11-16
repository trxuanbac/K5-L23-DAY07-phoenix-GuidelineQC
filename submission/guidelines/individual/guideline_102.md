# Guideline gán nhãn — v0.1 (Bản cá nhân) — Mã thành viên 102

> Bản guideline cá nhân của thành viên **102**, xây dựng từ việc khảo sát toàn bộ 40 ảnh tại `data/images/` nhằm khắc phục các điểm mơ hồ của bản khởi đầu v0, đặc biệt phân định rõ ràng giữa con người thực và các thực thể nhân tạo (tượng, ma-nơ-canh).

- Phiên bản: v0.1 (Bản cá nhân)
- Ngày: 2026-11-16
- Người viết: Thành viên 102

---

## 1. Mục tiêu

Nhãn bounding box 2D dùng để huấn luyện và đánh giá mô hình thị giác máy tính phát hiện người và phương tiện giao thông đường phố (phục vụ hệ thống hỗ trợ lái xe nâng cao ADAS hoặc camera giám sát giao thông đô thị thông minh). 

* **Rủi ro an toàn & Nguyên tắc cốt lõi:**
  * Sai sót nghiêm trọng nhất là **bỏ sót (False Negative)** người đi bộ, người điều khiển xe máy hoặc xe cộ đang di chuyển, có thể dẫn đến tai nạn giao thông.
  * Việc **gán nhầm (False Positive)** các vật thể nhân tạo tĩnh như tượng đài (`statue`), ma-nơ-canh (`mannequin`), hình nộm, búp bê đồ chơi hoặc hình ảnh phản chiếu qua gương thành con người thật (`person`) sẽ gây hiện tượng "phanh ma" (phantom braking) làm dừng xe đột ngột, gây nguy hiểm khôn lường cho các phương tiện phía sau.
  * **Nguyên tắc phân định:** `person` chỉ dành riêng cho con người thật còn sống. Tượng và ma-nơ-canh là các vật thể trang trí/nghệ thuật nhân tạo vô tri, dứt khoát không thể xếp vào lớp người.

---

## 2. Lớp và thuộc tính

### 2.1. Định nghĩa lớp đối tượng

| Lớp | Định nghĩa chuẩn xác | Ví dụ (kèm ID ảnh) | Phản ví dụ / Tuyệt đối không gán |
|---|---|---|---|
| `person` | **Con người thật còn sống** đang đi bộ, đứng, ngồi, bán hàng hoặc đang điều khiển phương tiện trên đường. | Người lái xe máy `img_005.jpg`, người đi bộ sang đường `img_014.jpg`, người đứng chờ ở bến xe buýt `img_032.jpg`. | **Tượng đồng/đá (`statue`)** `img_017.jpg`, **ma-nơ-canh (`mannequin`)** `img_033.jpg`, **tượng Predator** `img_011.jpg`, **ảnh chân dung in quảng cáo** `img_018.jpg`, **búp bê/đồ chơi** `img_038.jpg`. |
| `car` | Ô tô con từ 4–7 chỗ gồm sedan, hatchback, SUV, crossover và xe bán tải nguyên bản chưa gắn thùng cao. | Ô tô con lưu thông trên đường `img_013.jpg`, `img_032.jpg`, ô tô con trên sàn xe chở xe `img_002.jpg`. | Mô hình xe đồ chơi trong tủ kính `img_038.jpg` (*loại bỏ, không gán*); xe tuk-tuk `img_004.jpg`. |
| `truck` | Xe tải chở hàng mọi kích cỡ, xe đầu kéo, xe chuyên dụng có cabin tách biệt với thùng hàng. | Xe tải chở ô tô `img_002.jpg`, xe tải chở hàng trong kho `img_007.jpg`, xe tải nhẹ cabin lật `img_036.jpg`. | Tượng đài xe bán tải bằng đá hoa cương `img_027.jpg` (*loại bỏ vì là tượng/monument*); xe van thùng kín liền khối. |
| `van` | Xe ô tô chở người hoặc hàng dạng thùng kín liền khối giữa cabin và khoang sau (không có vách hở). | Xe van di chuyển ban đêm `img_031.jpg`. | Xe bán tải gắn nắp thùng cao tháo rời `img_037.jpg` (xếp vào truck/bán tải). |
| `motorcycle` | Xe hai bánh gắn động cơ đốt trong hoặc động cơ điện (xe máy, mô tô, scooter). | Đoàn người đi xe máy `img_005.jpg`, `img_008.jpg`, xe máy trong hầm `img_026.jpg`. | Xe đạp, xe xích lô không có động cơ `img_006.jpg`, `img_023.jpg`, `img_039.jpg` (*loại bỏ*). |
| `ignore_region` | Vùng không yêu cầu gán nhãn từng vật thể đơn lẻ, dùng cho đám đông dày đặc hoặc cụm vật thể quá nhỏ/xa. | Vùng người và xe quá xa, nhòe ở cuối đường `img_025.jpg`, `img_040.jpg`. | Các phương tiện hoặc người ở cự ly gần nhìn rõ chi tiết (bắt buộc phải gán box riêng). |

* **Khảo sát kỹ các ca ngoài taxonomy khởi đầu:**
  * **Xe 3 bánh / Tuk-tuk (`img_004.jpg`, `img_016.jpg`, `img_035.jpg`):** Bản v0 chưa có lớp. Đề xuất bổ sung lớp `tuk_tuk` trong buổi họp Pha 2.
  * **Xe buýt (`bus`):** Xuất hiện thực tế trong các ảnh có xe buýt lưu thông: `img_009.jpg` (xe buýt lớn chạy sau xích lô ở Huế), `img_031.jpg` (xe buýt/khách ban đêm), `img_040.jpg` (xe buýt đô thị Bangkok).
    * *Lưu ý quan trọng:* Ảnh `img_032.jpg` chỉ là **bến chờ xe buýt (bus stop)** nơi người dân đứng chờ và có ô tô con chạy qua, **hoàn toàn KHÔNG có xe buýt** trong khung hình.
  * **Vật thể nhân tạo mô phỏng (Tượng `statue`, Ma-nơ-canh `mannequin`):** Tuyệt đối không gán vào lớp `person` (xem chi tiết mục 3.5).

---

### 2.2. Thuộc tính với ngưỡng định lượng đo lường được

Mỗi bounding box bắt buộc phải gán đầy đủ 3 thuộc tính sau:

1. **`occluded_level` (select: `0`, `1`, `2`, `3`):** Mức độ diện tích bề mặt của vật thể bị che khuất bởi vật khác hoặc mép ảnh:
   * `0` (Không che - 0%): Nhìn thấy 100% toàn bộ chu vi và thân vỏ của vật thể.
   * `1` (Che nhẹ - 1% đến 30%): Bị che một phần nhỏ (ví dụ bánh xe bị vỉa hè che, chân người bị đuôi xe che nhẹ).
   * `2` (Che vừa đến nặng - 31% đến 70%): Bị che khuất phần lớn diện tích nhưng hình dạng cơ bản vẫn nhận dạng được rõ ràng.
   * `3` (Che khuất nghiêm trọng - > 70%): Bị che gần hết diện tích (ví dụ chỉ thấy đầu người nhô lên hoặc chỉ thấy mũi xe thò ra), nhưng người quan sát vẫn khẳng định 100% có vật thể ở đó.

2. **`truncated` (select: `no`, `yes`):**
   * `no`: Toàn bộ vật thể nằm trọn vẹn 100% bên trong 4 cạnh của khung ảnh.
   * `yes`: Vật thể bị cắt cụt bởi ít nhất một trong 4 mép ảnh (trên, dưới, trái, phải).

3. **`can_review` (checkbox: `true`, `false`):**
   * `false`: Người gán hoàn toàn tự tin về vị trí box và phân lớp đối tượng.
   * `true`: Người gán còn phân vân về ranh giới box hoặc phân lớp để nhóm xem lại ở buổi họp Calibration.

---

## 3. Quy tắc vẽ Bounding Box

### 3.1. Ôm sát phần nhìn thấy (Visible Bounding Box)
* Bounding box phải là hình chữ nhật nhỏ nhất tiếp xúc khít 4 điểm cực đại (trên, dưới, trái, phải) của **phần nhìn thấy được** của vật thể. Không phỏng đoán phần bị che khuất hoàn toàn.

### 3.2. Cặp Người và Xe máy (Rider and Motorcycle)
* **Bắt buộc vẽ 2 box tách biệt:**
  * 1 box lớp `motorcycle`: Ôm toàn bộ xe máy từ bánh trước, bánh sau, tay lái đến đuôi xe.
  * 1 box lớp `person`: Cho mỗi người ngồi trên xe máy (người lái và người ngồi sau), ôm từ đỉnh mũ bảo hiểm/đầu đến phần chân/thân người nhìn thấy được. Box người được phép chồng lấn lên box xe.

### 3.3. Xe chuyên dụng chở xe (Car Carrier - `img_002.jpg`)
* Vẽ 1 box lớn lớp `truck` bao toàn bộ xe tải kéo và khung giàn 2 tầng của xe chở hàng.
* Vẽ riêng từng box lớp `car` ôm khít từng chiếc ô tô con đang nằm trên hai sàn xe tải (nếu nhìn thấy rõ ràng trên $\ge 30\%$ thân xe).

### 3.4. Hàng hóa cồng kềnh trên phương tiện (`img_029.jpg`, `img_035.jpg`)
* Bounding box của phương tiện chỉ ôm khung kết cấu của xe (cabin, thành xe, bánh xe).
* Hàng hóa chất quá khổ vượt trội lên nóc xe (như khối bao tải khổng lồ trên nóc tuk-tuk ở `img_035.jpg`) **không bao vào trong box phương tiện**, tránh làm méo mó tỷ lệ kích thước chuẩn của phương tiện giao thông.

### 3.5. Quy tắc phân định rạch ròi: Người thật (`person`) vs Tượng (`statue`) / Ma-nơ-canh
* **Bản chất vấn đề:** Con người thật có khả năng tự chuyển động, có hành vi tham gia giao thông và cần được hệ thống an toàn ưu tiên bảo vệ tối đa. Tượng (`statue`), ma-nơ-canh, búp bê, tranh ảnh là vật vô tri nhân tạo, không có hành vi sinh học.
* **Quy tắc dứt khoát:**
  * **Tượng đài / Tượng nghệ thuật (`statue`):** Tượng công tước cưỡi ngựa (`img_017.jpg`), tượng nhân vật Predator trang trí (`img_011.jpg`), tượng xe bán tải bằng đá (`img_027.jpg`) $\rightarrow$ **TUYỆT ĐỐI KHÔNG GÁN LÀ `person` HAY `car`**.
  * **Ma-nơ-canh trưng bày (`mannequin`):** Ma-nơ-canh bằng nhựa/thạch cao mặc quần áo sau tủ kính (`img_033.jpg`) $\rightarrow$ **TUYỆT ĐỐI KHÔNG GÁN LÀ `person`**.
  * **Hình in phẳng 2D:** Áp phích chân dung chính khách (`img_018.jpg`) $\rightarrow$ **KHÔNG GÁN**.
  * **Búp bê, mô hình đồ chơi:** Búp bê và mô hình ô tô nhỏ trong tủ kính (`img_038.jpg`) $\rightarrow$ **KHÔNG GÁN**.
  * **Ảnh phản chiếu qua gương:** Hình ảnh ảo nhìn qua gương chiếu hậu (`img_020.jpg`) $\rightarrow$ **KHÔNG GÁN**.
* *Lưu ý về ảnh trống:* Các ảnh như `img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg` là các **ảnh 0 annotation (ảnh không có đối tượng cần gán)**, vẫn phải giữ trong Task CVAT và xuất file COCO hợp lệ.

### 3.6. Vật thể quá nhỏ và Vùng mờ nhòe
* Những vật thể có chiều cao bounding box $< 20\text{ px}$ ở hậu cảnh xa: Không gán box đơn lẻ.
* Khu vực đông đúc người và phương tiện chen chúc ở hậu cảnh xa: Vẽ một box bao quanh và gán lớp `ignore_region`.

---

## 4. Bảng ca khó khảo sát từ 40 ảnh

Dưới đây là 7 tình huống biên thực tế được thành viên 102 khảo sát và lập luận kỹ lưỡng:

| Ảnh | Tình huống thực tế | Mã đề xuất | Quyết định của thành viên 102 | Lý do căn cứ |
|---|---|:---:|---|---|
| `img_017.jpg` | Tượng công tước Duke of Wellington cưỡi ngựa đội nón giao thông. | `R-03` | **Không gán box `person`** (đây là ảnh 0 annotation). | Là tượng kim loại nhân tạo tĩnh (`statue`), không có hành vi sống của con người, không được đánh đồng với `person`. |
| `img_033.jpg` | Ma-nơ-canh thời trang trong tủ kính phố Buchanan. | `R-03` | **Không gán box `person`** (đây là ảnh 0 annotation). | Là ma-nơ-canh bằng nhựa/thạch cao (`mannequin`), không phải thực thể sống, gán là người sẽ gây lỗi phanh khẩn cấp sai lệch. |
| `img_032.jpg` | Khung cảnh bến chờ xe buýt ETS tại Edmonton trong giờ cao điểm. | `R-08` | **Không gán lớp `bus` vì không có xe buýt trong ảnh.** Gán `person` cho người chờ xe và `car` cho ô tô lưu thông trên đường. | Tiêu đề là "Bus Stop" (trạm chờ) nhưng khung hình chỉ có người đứng ở trạm và ô tô con trên lòng đường, tuyệt đối không suy diễn có xe buýt. |
| `img_002.jpg` | Xe tải 2 tầng chở 8 chiếc ô tô con trên sàn xe. | `R-02` | Gán 1 box `truck` cho xe chở hàng và gán riêng 8 box `car` cho 8 xe con trên hai sàn xe tải. | Mô hình cần phát hiện cả phương tiện vận tải và các phương tiện đang được chở để tránh bỏ sót phân loại ô tô con. |
| `img_004.jpg` | Hai xe tuk-tuk 3 bánh sơn màu xanh đậu trên vỉa hè. | `R-04` | Đề xuất bổ sung lớp `tuk_tuk`. Khi gán ở v0, tạm thời tick `can_review=true`. | Xe 3 bánh có hình thái khác biệt với ô tô con và xe máy 2 bánh; gán bừa vào `car` hay `motorcycle` sẽ làm hỏng đặc trưng học. |
| `img_020.jpg` | Dải cầu vồng và hình ảnh phía sau phản chiếu qua gương chiếu hậu ô tô. | `R-05` | **Không gán** bất kỳ box nào cho các đối tượng bên trong mặt kính gương. | Ảnh trong gương là ảnh ảo 2D, đối tượng thực tế không nằm tại vị trí gương xe. |
| `img_035.jpg` | Xe ba bánh chở bao tải hàng hóa chất cao gấp đôi thân xe. | `R-07` | Box phương tiện chỉ ôm khung sắt và bánh xe tuk-tuk, loại trừ khối bao tải nhô cao trên nóc. | Tránh làm biến dạng tỷ lệ khung hình bounding box (aspect ratio) chuẩn của dòng xe 3 bánh. |

---

## 5. QC và Lịch sử phiên bản

### 5.1. Tiêu chí kiểm soát chất lượng
* Áp dụng ngưỡng nghiệm thu của lab:
  * Điểm IoU trung bình giữa 2 người gán: $\ge 0.85$ (đối với box $\ge 50\text{ px}$) và $\ge 0.70$ (đối với box $< 50\text{ px}$).
  * Tỷ lệ đồng thuận phân lớp: $\ge 95\%$.
  * Tỷ lệ ảnh có lỗi khi nghiệm thu toàn lô: $\le 2\%$.
* Mọi trường hợp phân vân đều phải bật cờ `can_review` để mang ra họp đối chiếu nhóm ở Pha 2 và Pha 5.

### 5.2. Lịch sử thay đổi tài liệu
| Phiên bản | Thay đổi chính | Lý do / Ca thực tế đối chiếu |
|:---:|---|---|
| `v0` | Bản khởi đầu do giảng viên cung cấp. | Quá sơ sài, chỉ có 5 lớp định tính, thiếu quy tắc ca khó. |
| `v0.1` | Bản cá nhân của thành viên 102: <br/>- **Phân định dứt khoát người là người thật, tượng (`statue`) và ma-nơ-canh (`mannequin`) là vật tĩnh vô tri, tuyệt đối không gán `person`.**<br/>- **Đính chính ảnh `img_032.jpg` không có xe buýt** (chỉ có người chờ xe và ô tô).<br/>- Bổ sung định lượng ngưỡng % cho `occluded_level`.<br/>- Quy định rõ vẽ tách box người và xe máy.<br/>- Đưa ra quy tắc cho hàng hóa cồng kềnh, xe chở xe. | Khắc phục hoàn toàn các ca biên thực tế phát hiện trong 40 ảnh trước buổi họp thống nhất v1.0 của nhóm. |
