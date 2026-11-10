# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

#### Định nghĩa lớp
| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| :--- | :--- | :--- |
| **car** | 0 | Xe con (4-7 chỗ), sedan, hatchback, SUV, bán tải cỡ nhỏ | Kích thước nhỏ hơn truck/bus, mui thấp. |
| **truck** | 1 |Xe tải chở hàng cỡ trung và lớn, xe ben, xe container | Có thùng chở hàng rõ rệt phía sau. |
| **bus** | 2 |Xe khách chở người cỡ lớn (trên 16 chỗ), xe buýt công cộng | Kích thước dài, nhiều cửa sổ dọc thân xe. |
| **van** | 3 |Xe tải van cỡ nhỏ/trung, xe chở khách loại nhỏ (9-16 chỗ) | Dạng hộp, không có thùng hở như truck nhưng to hơn car thông thường. |

#### Quy tắc chung
* **Box bám sát vật thể nghĩa là:** Vẽ bounding box bao bọc khít sát các điểm ngoài cùng của vật thể (không để thừa quá nhiều khoảng trống và không cắt lẹm vào vật thể).
* **Vật thể bị che khuất:** Nếu bị che khuất một phần dưới 50% và vẫn nhận diện rõ lớp, thực hiện vẽ box ước lượng phần bị che. Nếu bị che khuất quá nhiều (>50%) và không rõ hình dạng, có thể bỏ qua tùy theo độ phân giải.
* **Vật thể bị cắt bởi rìa ảnh:** Chỉ vẽ box cho phần xuất hiện trong ảnh nếu phần đó đủ để nhận diện chính xác lớp vật thể.
* **Vật thể rất nhỏ/mơ hồ:** Bỏ qua các vật thể có kích thước quá nhỏ (ví dụ: dưới 10-15 pixels) hoặc quá mờ không thể phân biệt bằng mắt thường.

#### Ghi lại quyết định cho ít nhất ba ca mơ hồ
| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| :--- | :--- | :--- | :--- |
| *drive_01* | Xe bán tải lớn bị khuất đuôi | Gán nhãn là **car** (hoặc **truck** tùy schema cụ thể) | Dựa trên mục đích sử dụng cá nhân và kích thước cabin |
| *drive_02* | Xe khách 16 chỗ ở xa | Gán nhãn là **van** | Kích thước và kiểu dáng thiết kế dạng hộp đặc trưng |
| *drive_03* | Xe tải nhỏ cabin liền thùng kín | Gán nhãn là **truck** | Có thiết kế chở hàng chuyên dụng phía sau |
