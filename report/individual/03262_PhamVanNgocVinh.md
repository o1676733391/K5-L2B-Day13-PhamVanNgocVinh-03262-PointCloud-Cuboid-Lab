# Báo cáo Day 13 — Phạm Văn Ngọc Vinh, 03262

## 1. Kết luận trước
- **Mã số (MSSV):** `03262` · **Task:** `D13-03262`
- **10 frame của tôi:** `s01f17`, `s01f53`, `s02f08`, `s02f41`, `s03f31`, `s03f47`, `s04f04`, `s04f55`, `s05f06`, `s05f23`
- **Môi trường:** Windows 11 · Trình duyệt: Google Chrome · Phiên bản CVAT: Server v2.82.0 / SDK v2.83.0 · **MSSV bạn ngồi cạnh (đổi kiểm):** `03261`
- **Tóm tắt kết quả:** Tôi đã gán tổng cộng **111 cuboid** trên 10 frame (`s01f17=10`, `s01f53=16`, `s02f08=16`, `s02f41=8`, `s03f31=5`, `s03f47=9`, `s04f04=10`, `s04f55=10`, `s05f06=14`, `s05f23=13`), trong đó frame có ít hộp nhất là `s03f31` (5 cuboid) do cảnh đường tại thời điểm đó thoáng hơn, các xe ở làn bên bị dải phân cách và hàng cây che khuất chỉ còn quá ít điểm. Công cụ `selfcheck` gắn cờ **11/111 cuboid** (chủ yếu do chiều rộng hộp bao cả gương chiếu hậu nên nhỉnh hơn ngưỡng 2,30 m một chút). Tôi tự tin nhất ở phần **xác định hướng góc `yaw` và độ cao đáy `z_bottom` chạm mặt đường** trên các phương tiện trong phạm vi 30 m; phần còn khó nhất là **ước lượng chiều dài thực của các xe con ở xa 35–50 m** khi chỉ thấy mặt đuôi hoặc mũi xe.

- **Cách dùng CVAT:**
  1. **Đổi góc nhìn:** Kéo chuột trái trên khung 3D Perspective để xoay (xoay nhìn thẳng từ trên xuống và cuộn con lăn thu nhỏ để quét toàn cảnh 360° quanh xe), kéo chuột phải để dời tâm nhìn, nháy đúp vào khoảng trống để về góc nhìn mặc định.
  2. **Vẽ và chỉnh một cuboid:** Chọn công cụ Cuboid ở thanh bên trái → chọn đúng Class → bấm **Shape** (không dùng Track), đưa hộp tới cụm điểm của vật rồi nháy đúp để đặt; dùng khung **Top** để chỉnh tâm `(x, y)`, chiều dài `length`, chiều rộng `width` và xoay góc `yaw`, dùng khung **Side** và **Front** để chỉnh chiều cao `height` và hạ đáy `z_bottom` chạm mặt đường (tuyệt đối không xoay ở Side/Front).
  3. **Bật mũi tên hướng và đối chiếu ảnh camera:** Mở **Appearance → Cuboid orientation** để hiển thị vạch đỏ ngắn (`+X` nội tại của hộp) và xoay sao cho vạch đỏ chỉ đúng về đầu xe; sau khi bấm Save (`Ctrl+S`), chạy `python scripts/lab.py preview` để kiểm tra hộp chiếu lên 7 ảnh camera và bản đồ BEV trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f17` và `s01f53`. Ảnh: `t1-s01f17-3d.png`, `t1-s01f17-top.png`, `t1-s01f53-3d.png`, `t1-s01f53-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f17` | Phía trước xe (`−y`, khớp `cam01`) hướng lên cạnh trên của ảnh top-down; trục `+x` (vạch đỏ dài) chỉ sang cạnh trái; trục `+y` (vạch xanh lá dài) chỉ xuống cạnh dưới (phía sau xe). | Mặt đường tương đối phẳng, nằm ở `z ≈ -1.67 m` đến `-1.85 m` (thấp hơn tâm LiDAR khoảng `1.75 m`). | Khoảng `75–90 m` dọc làn đường phía trước (`y ≈ -76 m` đến `-90 m`). | `Car` (sedan, SUV), `Bus` (xe buýt lớn phía trước bên trái ở `29.86 m`). | Phía sau (`+y`, `cam05`, `cam08`, `cam09`) có 4 xe con ở khoảng cách `8.44 m`, `14.70 m`, `21.98 m` và `46.75 m`; bên trái làn đối diện có xe con ngược chiều ở `28.77 m`. | Một vài cụm điểm nhỏ sát dải phân cách và tán cây bên phải (`x < -12 m, y ≈ -25 m`) quá thưa, không rõ là bụi cây hay xe máy bị che. |
| `s01f53` | Phía trước xe (`−y`, khớp `cam01`) hướng lên cạnh trên của ảnh top-down; trục `+x` chỉ sang cạnh trái; trục `+y` chỉ xuống cạnh dưới (phía sau xe). | Mặt đường phẳng ở vùng gần (`z ≈ -1.75 m` đến `-1.84 m`), hơi nâng nhẹ về phía vỉa hè bên phải (`z ≈ -1.33 m`). | Khoảng `46.50 m` về phía sau (`y = +46.48 m`) và `~35 m` về phía trước. | `Car`, `Cyclist` (2 xe hai bánh bên phải), `Pedestrian` (2 người đi bộ trên phần đường/vỉa hè bên phải). | Phía sau (`cam09`) có 3 xe con ở `6.46 m`, `16.08 m`, `46.50 m`; bên phải (`-x`, `cam07`) có 2 `Cyclist` (`10.05 m`, `16.47 m`) và 2 `Pedestrian` (`13.61 m`, `14.11 m`). | Cụm điểm nhỏ rải rác phía sau hàng rào vỉa hè bên phải (`x ≈ -15 m, y ≈ -10 m`) bị cây che khuất không phân biệt được rõ hình khối. |

- **3 điều chỉ camera cho biết (lấy từ `s01f17` và `s01f53`):**
  1. Màu sắc, kiểu dáng chi tiết và vạch kẻ đường/biển báo chữ: ví dụ ở `s01f17`, `cam01` và `cam03` cho thấy rõ chiếc xe gần nhất bên trái (`idx = 1`) là xe hatchback/sedan màu trắng và chiếc xe lớn phía trước (`idx = 6`) là xe buýt màu trắng-đỏ cùng các mũi tên chỉ làn màu trắng trên mặt nhựa đường.
  2. Phân biệt rõ đầu xe và đuôi xe (đèn pha phía trước hay đèn hậu/biển số phía sau) cho các xe ở xa hoặc đứng yên: ví dụ ở `s01f17`, `cam09` cho thấy rõ chiếc xe màu xanh đậm phía sau (`idx = 2`) đang hướng đầu xe về cùng chiều với xe thu dữ liệu.
  3. Xác định chính xác trạng thái người và phương tiện hai bánh: ở `s01f53`, `cam07` giúp xác nhận rõ người đang ngồi điều khiển xe hai bánh (`Cyclist`, `idx = 13, 14`) khác với người đang đi bộ trên vỉa hè (`Pedestrian`, `idx = 15, 16`).
- **3 điều chỉ LiDAR cho biết (lấy từ `s01f17` và `s01f53`):**
  1. Tọa độ 3D và khoảng cách thực chính xác tới từng centimet theo đơn vị mét: ví dụ tâm xe con `s01f17 #1` ở đúng `(x = 3.544 m, y = -7.421 m, z = -1.074 m)`, cách LiDAR `dist_m = 8.22 m`.
  2. Kích thước hình học thực tế (dài × rộng × cao theo mét) không bị biến dạng phối cảnh như ảnh 2D: ví dụ đo trực tiếp được xe buýt `s01f17 #6` dài `12.402 m`, rộng `2.744 m`, cao `3.308 m`.
  3. Độ cao mặt đất (`z_bottom ≈ -1.75 m`) và cấu trúc không gian 360° đồng thời (kể cả các vùng nằm ở góc giao hoặc điểm mù giữa các khung hình camera).

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f08` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`, ngược chiều vạch xanh lá dài `+y`). | Giao lộ lớn có 3 xe buýt (`idx = 4, 5, 16`) tạo vùng bóng che lớn phía sau và đoàn người đi xe đạp/đi bộ qua đường bên phải. |
| `s02f41` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Có xe con đang chuyển hướng/rẽ ở phía sau bên phải (`idx = 5`, `yaw_deg = 353.8°`) và người đi xe hai bánh đi chéo (`idx = 6`, `yaw_deg = 208.8°`). |
| `s03f31` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Cảnh đường thoáng nhất trong 10 frame (chỉ có 5 phương tiện rõ cụm điểm), có 1 xe buýt lớn ở phía sau bên phải (`idx = 4`, `dist_m = 21.71 m`). |
| `s03f47` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Đoàn xe nối đuôi nhau trên cùng làn phía trước bên phải (`idx = 3, 5, 6, 7`), nên các xe sau bị xe trước che khuất một phần (`occluded = 1`). |
| `s04f04` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Bên trái có biển báo giao thông phản quang rất mạnh (intensity cao nổi bật) và dải xe chạy ngược chiều ở làn bên trái (`yaw_deg = 86.0°`). |
| `s04f55` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Có cả dòng xe cùng chiều (`yaw_deg = 268.2°`) lẫn dòng xe ngược chiều bên trái (`idx = 6, 7`, `yaw_deg = 90.7°`). |
| `s05f06` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Mật độ xe dày ở cả phía trước và phía sau (14 cuboid), có 1 xe con rất gần bên phải (`idx = 9`, `dist_m = 3.84 m`, `5753` điểm LiDAR). |
| `s05f23` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `−y`). | Nhiều xe con chạy song song ở nhiều làn (13 cuboid) và có 1 xe tải ở phía sau (`idx = 7`, `dist_m = 47.08 m`) bị xe trước che một phần (`occluded = 1`). |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f17, idx = 1` | `Car` | Đúng (`yaw_deg = 270.0°`, chỉ thẳng về phía trước `−y`) | Chạm đường (`z_bottom = -1.842 m`, `ground_gap_m = 0.05 m`) | Đã kiểm đủ 3 view + `cam01`, `cam06` | `l = 4.388 m`, `w = 2.019 m`, `h = 1.536 m` (khớp chuẩn Car/SUV trong bảng 4.3) |
| (b) xe buýt hoặc xe tải | `s01f17, idx = 6` | `Bus` | Đúng (`yaw_deg = 272.5°`, dọc thân xe buýt về phía trước) | Chạm đường (`z_bottom = -1.988 m`, `ground_gap_m = 0.16 m`) | Đã kiểm đủ 3 view + `cam01`, `cam03` | `l = 12.402 m`, `w = 2.744 m`, `h = 3.308 m` (`l, h` trong chuẩn Bus `10.0–12.5 m`, `3.0–3.5 m`; `w` nhỉnh hơn `2.60 m` do tính cả gương chiếu hậu lớn) |
| (c) người đi bộ hoặc xe hai bánh | `s01f53, idx = 13` | `Cyclist` | Đúng (`yaw_deg = 270.0°`, hướng di chuyển về phía trước) | Chạm mặt nền vỉa hè/làn phải (`z_bottom = -1.334 m`, `ground_gap_m = 0.09 m`) | Đã kiểm đủ 3 view + `cam07` | `l = 1.684 m`, `w = 0.660 m`, `h = 1.644 m` (khớp hoàn toàn chuẩn Cyclist `1.6–1.9 × 0.50–0.70 × 1.60–1.90 m`) |

- **Vòng hiệu chuẩn:** Khi mới đặt cuboid ở đầu buổi, tôi bị nhầm hướng trục nội tại của hộp (dùng trục `−Y` của hộp làm hướng đầu xe thay vì vạch đỏ ngắn `+X` của hộp, làm góc `yaw_deg` bị lệch `90°` và `length` bị đảo với `width`). Sau vòng hiệu chuẩn và đối chiếu guideline cùng cả lớp, chúng tôi chốt quy tắc thống nhất: luôn bật **Cuboid orientation**, xoay hộp ở khung **Top** để **vạch đỏ ngắn (`+X` của cuboid)** chỉ thẳng về đầu xe (xe chạy cùng chiều hướng `−y` phải có `yaw_deg ≈ 270°`, `length > width`), và hạ đáy hộp ở khung **Side/Front** chạm đúng lớp điểm mặt đường quanh xe.
- **Các vật bỏ qua vì không đủ cơ sở:**
  1. `s01f17` (vị trí `x ≈ -14 m, y ≈ -26 m`, bên phải sát hàng cây): cụm chỉ có vài điểm rời rạc bị tán cây và dải phân cách che khuất, ảnh `cam07` quá nhỏ không xác định được là xe máy hay vật cản tĩnh (`LOW_VISIBILITY`).
  2. `s03f31` (vị trí `x ≈ 12 m, y ≈ -45 m`, làn đối diện bên trái ở xa): chỉ có 1 vệt ngang khoảng 6–8 điểm LiDAR (< 15% thể tích), bị dải phân cách giữa đường che phần thân dưới nên không đủ cơ sở suy luận kích thước hộp (`LOW_VISIBILITY` / `UNCERTAIN_SCOPE`).

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Hiện tượng 1, 2, 3 lấy trực tiếp từ các frame của tôi (`D13-03262`); riêng hiện tượng 4 lấy từ frame gợi ý `s02f35` của **task chung `D13-HC485087`**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s02f08` / `t4-1-bong-che.png` / Task cá nhân `D13-03262` | Giả thuyết: Tia laser phát đi theo đường thẳng từ LiDAR trên nóc xe bị thân xe buýt lớn (`idx = 4`, `Bus` ở `dist_m = 17.87 m`) và các xe phía trước chắn toàn bộ đường truyền, tạo vùng khuất hình quạt phía sau. | Phía sau lưng xe buýt `s02f08 #4` là một dải quạt đen hoàn toàn trống điểm mặt đường; chiếc xe con `s02f08 #3` (`Car` ở `dist_m = 33.13 m`) bị che một phần thân chỉ còn `63` điểm LiDAR ở nửa không bị khuất. | Vùng trống sau vật chắn là vùng chưa quan sát được chứ không phải đường trống; với xe bị che một phần như `s02f08 #3`, bật cờ **`occluded = 1`** và vẽ cuboid bao trọn cả phần thể tích bị che suy luận được dựa trên ảnh camera và bảng 4.3. |
| 2. Thưa ở xa | `s01f17` / `t4-2-thua-o-xa.png` / Task cá nhân `D13-03262` | Giả thuyết: Các tia LiDAR (40 tia) tỏa ra theo góc phân kỳ cố định nên khi khoảng cách tăng lên (`30–50 m`), khoảng cách góc giữa các vòng tia và các điểm trên cùng một tia giãn rộng làm mật độ điểm trên đơn vị diện tích giảm mạnh. | Chiếc xe con `s01f17 #5` (`Car` ở `x = 4.054 m, y = 46.572 m, dist_m = 46.75 m`) chỉ còn **`71` điểm LiDAR** (so với `1727` điểm của xe `#1` ở `8.22 m`), chỉ tạo thành 2–3 vạch ngang thưa ở mặt hướng về LiDAR. | Kiểm tra điều kiện thấy được khoảng **15–20% thể tích** kèm đối chiếu ảnh `cam09` và kích thước tham chiếu bảng 4.3 để dựng đủ chiều dài hộp (`l = 3.877 m`); nếu thấy ít hơn 15% và camera không rõ thì bỏ qua, ghi `LOW_VISIBILITY`. |
| 3. Phản xạ thấp / ít điểm bất thường | `s02f08` / `t4-3-phan-xa-thap.png` / Task cá nhân `D13-03262` | Giả thuyết: Lớp sơn màu đen sẫm của xe sedan `s02f08 #6` (`dist_m = 11.13 m`) hấp thụ năng lượng tia hồng ngoại, đồng thời kính chắn gió/kính hậu nghiêng gây phản xạ gương (specular reflection) làm tia laser bật ra hướng khác thay vì quay về đầu thu LiDAR. | Dù ở rất gần (`11.13 m`), phần kính hậu và mui xe tối màu của `s02f08 #6` bị khuyết điểm rõ rệt (chỉ có `845` điểm, cường độ intensity thấp), trái ngược hẳn với các biển báo phản quang có điểm sáng rực. | Ghi nhận nguyên nhân ở mức giả thuyết; không co nhỏ hộp theo vùng khuyết điểm trên kính mà phải đối chiếu `cam01`/`cam03` để kéo chiều cao (`h = 1.486 m`) và chiều dài (`l = 4.266 m`) bao trọn đúng kích thước thật của xe. |
| 4. Điểm lạ | `s02f35` / `t4-4-diem-la.png` / Nguồn: Task chung `D13-HC485087` | Giả thuyết: Hiện tượng đa đường (multipath reflection) khi tia laser phản xạ qua mặt kính/vật kim loại ướt trên mặt đường rồi mới quay về cảm biến, làm thời gian bay (ToF) bị kéo dài và tính ra tọa độ ảo sâu dưới lòng đất. | Khi xoay nghiêng khung 3D của `s02f35`, ở phía sau bên trái (`dist ≈ 29 m`) xuất hiện một cụm khoảng **46 điểm** nằm lơ lửng ở độ cao **`z ≈ -13.5 m`** (thấp hơn mặt đường `z ≈ -1.7 m` tới hơn `11 m`). | Đây là nhiễu hình học (điểm ảo dưới mặt đất), tuyệt đối **không gán nhãn thành vật** và không nới đáy cuboid nào theo các điểm lạ này. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy trực tiếp từ `submission/D13-03262-bangA.csv` và `submission/D13-03262-tukiem.csv`. Mặt đường quanh xe ở khoảng `z ≈ -1.7 m`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f17, idx = 2, Car` (`x = 3.438 m, y = 7.708 m`) | `-0.921 m` | `1.500 m` | `-1.671 m` | `8.44 m` | `8.49 m` | `270.0°` | **Chạm đường chuẩn** (`z_bottom = -1.671 m` sát `-1.7 m`, `ground_gap_m = 0.06 m`). |
| `s02f08, idx = 4, Bus` (`x = 10.174 m, y = -14.690 m`) | `-0.212 m` | `3.245 m` | `-1.835 m` | `17.87 m` | `17.87 m` | `86.8°` | **Chạm đường chuẩn** (`z_bottom = -1.835 m`, lệch `0.135 m < 0.3 m`, `ground_gap_m = 0.13 m`). |
| `s03f47, idx = 1, Car` (`x = 3.227 m, y = -5.978 m`) | `-1.051 m` | `1.486 m` | `-1.794 m` | `6.79 m` | `6.87 m` | `268.2°` | **Chạm đường chuẩn** (`z_bottom = -1.794 m`, `ground_gap_m = -0.01 m`). |

- **(a) Cuboid gần nhất và xa nhất:**
  - **Cuboid gần nhất:** `s05f06, idx = 9, Car` (`x = -3.466 m, y = 1.660 m, z = -0.841 m`) có khoảng cách ngang **`dist_m = 3.84 m`** và khoảng cách 3D **`r = √((-3.466)² + 1.660² + (-0.841)²) = 3.93 m`**.
  - **Cuboid xa nhất trong phạm vi chấm điểm (≤ 50 m):** `s05f23, idx = 8, Car` (`x = 16.671 m, y = -44.439 m, z = -1.126 m`) có khoảng cách ngang **`dist_m = 47.46 m`** và khoảng cách 3D **`r = √(16.671² + (-44.439)² + (-1.126)²) = 47.48 m`**. *(Nếu tính cả các hộp ngoài 50 m trong Bảng A thì hộp xa nhất là `s01f17, idx = 9, Car` tại `x = 4.719 m, y = -90.544 m, z = -1.879 m` với `dist_m = 90.67 m` và `r = 90.69 m`).*
- **(b) So sánh kích thước `l, w, h` với Bảng 4.3:**
  - **Người đi xe hai bánh (`s01f53, idx = 13, Cyclist`):** `l = 1.684 m, w = 0.660 m, h = 1.644 m` — cả 3 chiều đều nằm hoàn toàn trong khoảng chuẩn của *Xe đạp có người lái* trong Bảng 4.3 (`l: 1.6–1.9 m`, `w: 0.50–0.70 m`, `h: 1.60–1.90 m`). Tương tự, người đi bộ `s01f53, idx = 15, Pedestrian` có `l = 0.560 m, w = 0.580 m, h = 1.560 m` nằm trọn trong khoảng chuẩn *Người lớn* (`0.4–0.9 × 0.5–0.9 × 1.50–1.95 m`).
  - **Xe con (`s01f17, idx = 1, Car`):** `l = 4.388 m, w = 2.019 m, h = 1.536 m` — chiều dài `l = 4.388 m` và chiều cao `h = 1.536 m` nằm trọn trong khoảng chuẩn Sedan/Hatchback (`3.8–5.0 m` và `1.40–1.60 m`); riêng chiều rộng `w = 2.019 m` vượt nhẹ khoảng thân xe Sedan (`1.70–1.90 m`, nằm trong khoảng SUV/Minivan `1.80–2.10 m`) vì theo quy tắc mục 2.1 của Guideline, hộp cuboid phải bao trọn cả hai gương chiếu hậu nhô ra hai bên hông xe.
- **(c) Góc `yaw_deg` của xe cùng chiều và ngược chiều:**
  - **Xe cùng chiều phía trước (`s01f17, idx = 1, Car`):** `yaw_deg = 270.0°` (và `s02f08, idx = 6, Car` có `yaw_deg = 271.6°`), sát chuẩn **`270°`** (hướng thẳng về `−y`).
  - **Xe ngược chiều (`s01f17, idx = 7, Car` và `s04f55, idx = 6, Car`):** có `yaw_deg` lần lượt là **`90.5°`** và **`90.7°`**, rất sát chuẩn **`90°`** (hướng ngược lại theo trục `+y`).

Mật độ theo khoảng cách (4 cuboid `Car` ở 4 khoảng cách `dist_m` tăng dần):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f17, idx = 1, Car` | `8.22 m` (`0–10 m`) | `1727` điểm |
| `s01f17, idx = 4, Car` | `14.70 m` (`10–20 m`) | `806` điểm |
| `s01f17, idx = 3, Car` | `21.98 m` (`20–30 m`) | `290` điểm |
| `s01f17, idx = 5, Car` | `46.75 m` (`30–50 m`) | `71` điểm |

- **Quy luật mật độ điểm theo khoảng cách:** Khi khoảng cách từ LiDAR tới xe tăng gấp **`~2 lần`** thì số điểm LiDAR thu được trên xe giảm đi khoảng **`3 đến 4 lần`** (tỷ lệ nghịch xấp xỉ với bình phương khoảng cách $1/d^2$ do sự phân kỳ của cả góc phương vị lẫn góc ngẩng giữa 40 tia laser). Số liệu thực tế từ 4 cuboid của tôi (`1727` điểm ở `8.22 m` → `806` điểm ở `14.70 m` → `290` điểm ở `21.98 m` → `71` điểm ở `46.75 m`) khớp rất sát với đường cong trung vị trên slide bài giảng (`1968` điểm ở `0–10 m`, `718` điểm ở `10–20 m`, `214` điểm ở `20–30 m`, `72` điểm ở `30–40 m`).
- **Tự kiểm và chỉnh sửa sau `preview` / `selfcheck`:**
  - **Các cuboid đã sửa sau tự kiểm:**
    1. `s02f08, idx = 16` (`x = 4.714 m, y = 44.781 m`): Ban đầu gán nhầm class `Car` với `width = 3.09 m` bị `selfcheck` báo vượt kích thước Car; khi đối chiếu ảnh `preview` (`cam09`), đây là chiếc xe buýt lớn ở phía sau nên tôi đã sửa class thành **`Bus`** và căn lại `width = 2.580 m` (`l = 11.616 m, h = 3.276 m`), hết cảnh báo.
    2. `s02f08, idx = 12` (`x = -10.544 m, y = -17.723 m`): Ban đầu để class `Cyclist` có `length = 0.59 m < width = 0.71 m` bị cờ `length < width`; đối chiếu point cloud và `cam07` tại vạch qua đường, tôi đã sửa lại thành **`Pedestrian`** với `length = 0.650 m, width = 0.710 m, height = 1.764 m`, hết cảnh báo.
    3. `s04f04, idx = 5, Car` và `s05f23, idx = 7, Truck`: Ban đầu bị cờ *đáy cao hơn mặt đường* (`ground_gap_m` là `+0.88 m` và `+1.17 m`); tôi đã hạ tâm `z` ở khung Side/Front xuống lần lượt `z = -0.880 m` (`z_bottom = -1.793 m`) và `z = -0.580 m` (`z_bottom = -1.717 m`) để đáy chạm sát mặt đường.
    4. `s01f53, idx = 15, Pedestrian`: Ban đầu `length = 0.19 m` bị cờ quá hẹp, đã nới hộp lên `0.560 × 0.580 × 1.560 m` để ôm trọn cụm điểm của người đi bộ.
  - **Những cảnh báo giữ nguyên kèm lý do:** Còn 11 cảnh báo nhẹ trong `tukiem.csv` (như `s01f53 #4, #6`, `s05f06 #5` có `width = 2.30–2.33 m` vượt ngưỡng `2.30 m` vài centimet do bao cả hai gương chiếu hậu và điểm nhiễu rìa; `s01f53 #15 Pedestrian` báo có `56 điểm sát ngay ngoài mép` do người này đi sát cạnh người đi bộ `#16` và hàng cây vỉa hè; `s05f06 #14 Truck` có `length = 3.470 m` là xe tải nhẹ/chuyên dụng thân ngắn và một phần đầu bị khuất). Tôi giữ nguyên vì hộp đã bám sát cụm điểm thực tế trên point cloud mà không bao lấn sang vật bên cạnh.
- **Đổi kiểm chéo (Peer check):** Đã đổi kiểm với bạn có MSSV **`03261`**. Hai con số được đối chiếu trực tiếp trên `submission/D13-03262-bangA.csv` và `submission/D13-03262-tukiem.csv` là: (1) độ cao đáy `z_bottom = -1.671 m` của `s01f17, idx = 2, Car` (`z = -0.921 m, h = 1.500 m`) và (2) khoảng cách `dist_m = 3.84 m` cùng số điểm `points_in = 5753` của cuboid gần nhất `s05f06, idx = 9, Car`. Kết quả: **Khớp 100%** với Bảng A và Phiếu tự kiểm.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Ý nghĩa từ phía model học trên nhãn 3D:**
  1. *Vì sao nhãn 3D đo bằng mét chứ không phải pixel:* Model phát hiện vật thể 3D trên xe tự hành làm việc trực tiếp trong không gian vật lý 3 chiều của LiDAR để phục vụ lập kế hoạch đường đi và tránh va chạm (cần biết chính xác vật cách bao nhiêu mét, dài rộng bao nhiêu mét, hướng di chuyển góc yaw bao nhiêu độ); trong khi kích thước theo pixel trên ảnh 2D bị co giãn theo phép chiếu phối cảnh (vật ở xa nhỏ lại) và phụ thuộc tiêu cự từng camera.
  2. *Vì sao vật thưa điểm cần quy tắc riêng (ngưỡng 15–20% thể tích):* Nếu gán nhãn cả những cụm chỉ có vài điểm rời rạc bằng cách đoán mò kích thước/hướng, bộ dữ liệu huấn luyện sẽ chứa nhiều nhãn nhiễu (label noise) khiến model học sai đặc trưng và sinh nhiều báo động giả (False Positives); ngược lại, với những vật còn thấy ≥ 15–20% thể tích kèm cơ sở camera, việc dựng hộp đủ kích thước chuẩn theo Bảng 4.3 giúp model học được cách hoàn thiện hình khối đầy đủ của xe ngay cả khi bị thưa điểm ở tầm trung.
- **(b) Cách dùng AI trong bài lab:**
  - **Công cụ:** Antigravity AI Assistant.
  - **Câu hỏi chính:** Cách kiểm tra hệ trục tọa độ VF (`x` trái, `y` sau, `z` lên, phía trước là `−y`), cách xoay lại góc `yaw` khi ban đầu căn nhầm hướng theo trục `−Y` nội tại của hộp thay vì trục `+X` nội tại, và cách rà soát các cờ cảnh báo của `selfcheck`.
  - **Cách dùng kết quả:** Tôi dùng để xoay đồng bộ hệ trục nội tại của các hộp đã vẽ trên CVAT về đúng chuẩn `+X` chỉ về đầu xe (`yaw_deg ≈ 270°` cho xe cùng chiều), sau đó tự kiểm tra lại bằng mắt trên 10 ảnh `work/xem/D13-03262-*.jpg` và chỉnh lại các hộp bị lệch class/độ cao đáy.
- **(c) Điều còn chưa chắc:** Ở các frame có hàng xe đỗ hoặc nối đuôi sát nhau ở khoảng cách 25–40 m (như `s01f53 #7, #8, #9`), tia LiDAR chỉ quét được mặt cản sau và một phần mui xe (`height` chỉ thấy khoảng `0.92–1.52 m`, `length` cụm điểm chỉ thấy `2.48–3.14 m`); việc quyết định nên kéo dài hộp đủ `4.5 m` theo kích thước xe con tiêu chuẩn hay giữ sát phần cụm điểm quan sát được vẫn là điểm tôi muốn nghe coach phân tích sâu thêm khi sang bài gán nhãn theo chuỗi Track ở Ngày 24–26.
