# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con chở người cỡ nhỏ: sedan, SUV, taxi, xe bán tải (cabin nhỏ). | Phân biệt với `van`: thân xe thấp, không có khoang hộp dài. Xe bán tải vẫn là `car`, không phải `truck`. |
| `truck` | Xe tải chở hàng, xe đầu kéo, có thùng hàng hoặc rơ-moóc. | Phân biệt với `car`: kích thước lớn, thùng hàng riêng biệt. |
| `bus` | Xe buýt, minibus chở khách. | Thân dài, nhiều cửa sổ hai bên, cao. Khác `van` ở kích thước lớn hơn nhiều. |
| `van` | Xe van, minivan chở hàng hoặc chở người. | Thân hộp, cao hơn `car` nhưng nhỏ hơn `bus`. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: cạnh box chạm sát điểm ngoài cùng của thân xe (không chừa khoảng trống lớn, không cắt mất phần xe), chỉ bao một xe, không bao bóng đổ.
- Vật thể bị che khuất: vẽ box nếu phần nhìn thấy đủ để nhận ra lớp; box bao phần nhìn thấy của xe.
- Vật thể bị cắt bởi rìa ảnh: vẽ box đến sát rìa ảnh, nếu phần còn lại đủ để nhận ra lớp.
- Vật thể rất nhỏ/mơ hồ: không gán nếu không phân biệt được lớp (ví dụ xe ở rất xa). Mô tô và các loại khác ngoài 4 lớp thì không gán.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_049` | Nhiều xe rất nhỏ ở xa, không phân biệt được lớp. | Gán car | Schema chỉ có 4 lớp, xe quá nhỏ không đủ nhận diện lớp. |
| `drive_009` | Xe nằm sát nhau, che khuất lẫn nhau. | Gán | Đề: bị che vẫn cần box nếu phần nhìn thấy đủ để nhận lớp. |
| `drive_042` | Xe bị cắt bởi rìa ảnh. | Gán car | Xe bán tải thuộc car / thân hộp chở người thuộc van. |
