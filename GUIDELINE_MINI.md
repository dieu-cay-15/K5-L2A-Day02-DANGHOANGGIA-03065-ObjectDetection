# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con cá nhân, sedan, hatchback, SUV, crossover, taxi, xe bán tải (pickup) cabin nhỏ. | Có nắp capo và cốp tách biệt hoặc dáng SUV/hatchback thấp; phân biệt với `van` ở chiều cao thân xe và cấu trúc đầu xe không dạng hộp phẳng. |
| `truck` | Xe tải chở hàng, xe tải thùng kín/thùng bạt, xe ben, xe đầu kéo, xe chuyên dụng hạng nặng. | Có cabin đầu kéo tách biệt với thùng hàng phía sau hoặc khung gầm cao chở hàng nặng; không gộp xe bán tải nhỏ (`car`) hay xe van chở hàng nhỏ (`van`). |
| `bus` | Xe buýt công cộng, xe buýt 2 tầng, xe khách cỡ lớn/trung (coach/minibus chở khách tuyến cố định). | Thân xe dài, cao, nhiều ô cửa kính liên tiếp dọc thân xe, đầu phẳng, có bảng điện tử tuyến đường hoặc cửa lên xuống rộng cho hành khách. |
| `van` | Xe van/minivan/MPV dạng hình hộp chở người hoặc chở hàng nhẹ (như面包车/microvan, MPV cỡ lớn). | Thân hình hộp liền khối, đầu ngắn hoặc phẳng, kích thước nhỏ và ngắn hơn `bus`, cao và vuông hơn `car`/SUV. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Cạnh của bounding box chạm sát các điểm ngoài cùng nhìn thấy được của thân xe (cản trước/sau, nóc xe, lốp/thân hai bên), không lấy dư mặt đường, bóng đổ, bầu trời hay xe lân cận.
- Vật thể bị che khuất: Vẫn gán một box riêng bao quanh đúng phần thân xe nhìn thấy được nếu còn đủ đặc trưng nhận diện chắc chắn lớp của xe; tách riêng từng box khi hai xe chồng lấp nhau.
- Vật thể bị cắt bởi rìa ảnh: Box dừng đúng tại biên ảnh (`0` hoặc `640` px), không suy đoán phần thân xe nằm ngoài khung hình, chỉ gán khi phần còn trong ảnh đủ nhận diện loại xe.
- Vật thể rất nhỏ/mơ hồ: Các chấm xe ở quá xa phía chân trời/gầm cầu vượt bị nhòe, kích thước quá nhỏ (< 18px) không thể phân biệt rõ thuộc lớp `car`/`van`/`truck`/`bus` thì không tự đoán và không gán nhãn.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_005` | Xe chuyên dụng màu trắng dừng sát dải phân cách giữa (`x=402..449, y=132..203`) có cabin đầu xe và khung giá/thùng thiết bị phía sau dễ nhầm giữa `van` và `truck`. | Gán lớp `truck` (`1`) | Xe có kết cấu cabin trước tách biệt với phần thùng/giá chở thiết bị công trình phía sau, thuộc nhóm xe tải chuyên dụng chở hàng/thiết bị (`truck`). |
| `drive_006` | Xe màu bạc dạng hộp vuông ở làn bên phải (`x=506..608, y=332..472`) có giá chở hàng trên nóc, hình dáng trung gian giữa hatchback/SUV (`car`) và microvan (`van`). | Gán lớp `van` (`3`) | Thân xe dạng hộp đứng liền khối, đầu xe ngắn, cửa trượt/cửa sổ vuông đặc trưng của dòng microvan/minivan chở người và hàng nhẹ (`van`). |
| `drive_009` | Xe tải thùng kín màu xanh lá cây (`x=125..242, y=166..318`) đi giữa đoàn xe buýt và xe van màu trắng (`x=216..278, y=185..234`) bị che khuất một phần bởi xe buýt tiền cảnh. | Xe thùng xanh gán `truck` (`1`); xe trắng bên cạnh gán `van` (`3`) | Xe xanh có cabin đầu vuông tách rời thùng hàng kín cao phía sau (`truck`); xe trắng bên cạnh là thân liền khối minivan (`van`), vẽ box bám sát phần nhìn thấy của từng xe. |
