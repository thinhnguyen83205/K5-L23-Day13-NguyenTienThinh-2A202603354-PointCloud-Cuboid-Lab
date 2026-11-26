# Báo cáo Day 13 — Nguyễn Tiến Thịnh, 2A202603354

## 1. Kết luận trước

- Mã số: `2A202603354`; task: `D13-2A202603354`.
- 10 frame: `s01f02`, `s01f17`, `s02f18`, `s02f52`, `s03f08`, `s03f51`, `s04f35`, `s04f52`, `s05f03`, `s05f41`.
- Môi trường: Windows; CVAT 2.83.0; trình duyệt không nhớ rõ. Tài khoản CVAT trong cấu hình: `thinhnguyen`.
- Có 55 cuboid. Mức ít nhất là 5 cuboid, ở 7 frame; lý do riêng của từng frame chưa được ghi lại.
- Phiếu tự kiểm hiện tại có 0/55 cuboid bị gắn cờ. Đây là kiểm tra tự động, không thay thế việc nhìn lại từng cuboid và xác nhận class, hướng, kích thước.

Trong CVAT, tôi xoay và dời khung 3D bằng chuột, dùng con lăn để phóng to/thu nhỏ. Tôi vẽ từng hộp ở chế độ **Shape**, sau đó chỉnh tâm và kích thước ở Top, chỉnh độ cao ở Side/Front. Tôi bật **Cuboid orientation** để xem hướng hộp và dùng `preview` để đối chiếu hộp với các ảnh camera.

## 2. Đọc frame (Bước 2)

Trong ảnh top-down của các preview, đầu xe thu dữ liệu hướng về mép trên. Theo hệ tọa độ VF, `+x` hướng sang trái và `+y` hướng về phía sau xe (xuống dưới trong ảnh). Mặt đường ở hai frame đọc kỹ nhìn tương đối phẳng; độ cao tham chiếu khoảng `z = -1,7 m` so với LiDAR. Ảnh CVAT: `t1-s01f02-3d.png`, `t1-s01f02-top.png`, `t1-s01f17-3d.png`, `t1-s01f17-top.png`.

| Frame | Hướng và trục trong top-down | Mặt đường | Cuboid xa nhất trong bảng A | Loại vật đã gán | Phía sau / hai bên; cụm chưa đọc được |
|---|---|---|---|---|---|
| `s01f02` | Đầu xe ở mép trên; `+x` sang trái, `+y` xuống dưới | Tương đối phẳng | Car, khoảng 23,68 m | 7 Car, 1 Truck | Các camera hướng sau và hai bên cho thấy xe ngoài hướng nhìn phía trước. Chưa ghi cụ thể cụm điểm nào không đọc được. |
| `s01f17` | Đầu xe ở mép trên; `+x` sang trái, `+y` xuống dưới | Tương đối phẳng | Bus, khoảng 29,96 m | 4 Car, 1 Bus | Có xe ở các hướng camera sau/bên; chưa ghi cụ thể cụm điểm nào không đọc được. |

Từ hai frame này, camera giúp nhận biết màu sắc, hình dáng thân xe và phân biệt một số loại vật; LiDAR cung cấp vị trí và khoảng cách 3D theo mét. LiDAR còn cho thấy vật ở phía sau hoặc ngoài góc nhìn của một camera; ảnh camera không tự cung cấp độ sâu 3D chính xác.

| Frame lướt | Phía trước trong top-down | Điều lạ |
|---|---|---|
| `s02f18` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s02f52` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s03f08` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s03f51` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s04f35` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s04f52` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s05f03` | Mép trên | Chưa ghi nhận điều lạ riêng. |
| `s05f41` | Mép trên | Chưa ghi nhận điều lạ riêng. |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Các giá trị dưới đây được lấy từ bảng A và phiếu tự kiểm hiện tại. Ảnh CVAT cuboid mẫu: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class, kích thước (dài × rộng × cao) | Yaw | Đáy / tự kiểm | So với bảng 4.3; kiểm tra |
|---|---|---|---:|---|---|
| Xe con gần | `s01f02`, #1 | Car, `5,235 × 1,952 × 1,512 m` | `266,6°` | `z_bottom=-1,778 m`, ground gap `0,02 m`, 2261 điểm | Kích thước nằm trong khoảng Car tổng quát của selfcheck. Preview đặt hộp trên xe; Top/Side/Front cần xác nhận trong CVAT. |
| Xe buýt | `s01f17`, #1 | Bus, `12,403 × 2,814 × 2,913 m` | `273,4°` | `z_bottom=-1,953 m`, ground gap `0,19 m`, 712 điểm | Chiều rộng lớn hơn khoảng tham chiếu 2,5–2,6 m; cần đối chiếu lại với điểm và ảnh, không tự ép kích thước theo cảnh báo. |
| Xe hai bánh có người | `s04f35`, #6 | Cyclist, `1,695 × 0,721 × 1,441 m` | `269,7°` | `z_bottom=-1,385 m`, ground gap `0,32 m`, 501 điểm | So với xe đạp có người: chiều dài phù hợp, rộng hơi lớn và cao thấp hơn khoảng tham chiếu; ground gap cũng đáng xem lại ở Side/Front. |

Ghi nhận về vòng hiệu chuẩn và các vật tôi bỏ qua vì không đủ cơ sở chưa có trong dữ liệu project hiện tại.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Ảnh chụp CVAT cho bốn hiện tượng đã được lưu theo tên bên dưới. Với các frame gợi ý trong task chung, cần ghi nguồn task khi mô tả trong báo cáo cuối.

| Hiện tượng | Frame / nguồn gợi ý | Cần xác nhận trên point cloud và ảnh | Ảnh |
|---|---|---|---|
| Bóng che | `s02f00` hoặc `s03f00`, task chung `D13-HC485087` | Tài liệu lab gợi ý vùng khuất phía sau xe buýt hoặc tường; vùng thiếu điểm có thể là chưa quan sát được, không phải đường trống. | `t4-1-bong-che.png` |
| Thưa ở xa | Ứng viên trong task cá nhân: `s04f52`, #5 Car, `32,88 m`, 125 điểm | Số điểm trong hộp ít hơn nhiều so với xe gần; cần xem hình dạng có đủ căn cứ gán nhãn hay không. | `t4-2-thua-o-xa.png` |
| Phản xạ thấp / ít điểm bất thường | `s04f02`, task chung `D13-HC485087` là frame gợi ý trong tài liệu lab | So sánh vùng phản xạ mạnh với xe/vật có ít điểm; nguyên nhân chỉ ghi là giả thuyết sau khi xem camera và LiDAR. | `t4-3-phan-xa-thap.png` |
| Điểm lạ | `s02f35`, task chung `D13-HC485087` là frame gợi ý trong tài liệu lab | Tài liệu lab gợi ý một cụm điểm ở thấp hơn mặt đường; cần tự kiểm tra trong khung 3D, không gán cụm đó thành vật. | `t4-4-diem-la.png` |

## 5. Số từ cuboid của tôi (Bước 5)

Với `r = √(x²+y²+z²)`, các ví dụ lấy từ bảng A hiện tại:

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) | Số điểm / ground gap | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|---|
| Gần nhất: `s05f03`, #4, Car | -0,801 | 1,723 | -1,662 | 3,41 | 3,50 | 270,4 | 5425 điểm / 0,13 m | Đáy gần mặt đường. |
| Xa nhất: `s04f52`, #5, Car | -0,915 | 1,504 | -1,667 | 32,88 | 32,89 | 268,4 | 125 điểm / 0,04 m | Đáy gần mặt đường; điểm thưa hơn xe gần. |
| `s04f35`, #6, Cyclist | -0,665 | 1,441 | -1,385 | 10,33 | 10,33 | 269,7 | 501 điểm / 0,32 m | Đáy cao hơn đường ước tính; cần kiểm tra lại trong Side/Front. |

So sánh kích thước: Cyclist ở `s04f35` có kích thước `1,695 × 0,721 × 1,441 m`; nếu hiểu là xe đạp có người thì chiều rộng hơi lớn và chiều cao thấp hơn khoảng tham chiếu, còn nếu là xe máy có người thì chiều dài thấp hơn nhẹ. Car gần nhất (`s05f03` #4) có kích thước `4,753 × 2,200 × 1,723 m`; dài và cao phù hợp khoảng SUV/minivan tham chiếu, rộng hơi lớn hơn khoảng 1,8–2,1 m. Cần dùng ảnh và cụm điểm để quyết định, không chỉ dựa vào số.

Yaw minh họa: xe ở phía trước với `s05f03` #4 có yaw `270,4°`; xe ở phía trước ngược chiều với `s02f52` #1 có yaw `90,0°`. Hai hướng gần với giá trị tham chiếu lần lượt 270° và 90°.

| Cuboid Car | dist_m (m) | points_in |
|---|---:|---:|
| `s05f03` #4 | 3,41 | 5425 |
| `s03f08` #4 | 10,04 | 1234 |
| `s02f52` #4 | 20,53 | 299 |
| `s04f52` #5 | 32,88 | 125 |

Trong bốn ví dụ này, khoảng cách tăng khoảng 9,6 lần từ 3,41 m lên 32,88 m, còn số điểm giảm từ 5425 xuống 125 (còn khoảng 2,3%). Đây là các vật ở frame khác nhau nên chỉ minh họa xu hướng thưa điểm theo khoảng cách, không phải phép đo kiểm soát cùng một vật.

Sau selfcheck, tôi đã xem lại `s05f03` #2 Car và #3 Truck. Bảng A hiện tại ghi chiều dài lần lượt `5,500 m` và `4,250 m`; không còn cảnh báo nào trong phiếu tự kiểm hiện tại. Tôi không lưu lại số đo trước khi sửa nên không ghi chênh lệch trước/sau. Không có cảnh báo hiện hành được giữ lại; tuy vậy, chiều rộng của một số Bus vẫn nên được đối chiếu trực quan với điểm và ảnh.

Đối chiếu chéo với bạn học: không thực hiện.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét vì mô hình cần học vị trí và kích thước vật trong không gian; kích thước biểu diễn bằng pixel thay đổi theo camera và khoảng cách. Điểm LiDAR thưa do khoảng cách, góc quét và che khuất, nên cần quy tắc riêng để không đoán quá mức từ một phần nhỏ của vật.

- Công cụ: VSCode, CVAT
- Cách dùng: đối chiếu số liệu với file hiện có; các nhận xét hình ảnh và quyết định cuối cùng cần tự kiểm tra trong CVAT.
