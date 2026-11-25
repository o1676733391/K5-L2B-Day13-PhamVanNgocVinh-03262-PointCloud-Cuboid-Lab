# Báo cáo Day 13 — Phạm Văn Ngọc Vinh, 03262

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
- **Mã số (MSSV):** `03262` · **Task:** `D13-03262`
- **10 frame của tôi:** `s01f17`, `s01f53`, `s02f08`, `s02f41`, `s03f31`, `s03f47`, `s04f04`, `s04f55`, `s05f06`, `s05f23`
- **Môi trường:** Windows · Trình duyệt: Chrome/Edge · Phiên bản CVAT: v2.83.0 (hoặc v2.82.0) · **MSSV bạn ngồi cạnh:** TODO(học viên)
- **Tóm tắt kết quả:** TODO(học viên): tổng bao nhiêu cuboid, frame nào ít nhất và vì sao; `selfcheck` cờ bao nhiêu cuboid trên tổng số; bạn tin phần nào nhất và phần nào còn yếu nhất (hướng, đáy, kích thước, class, vật xa).

- **Cách dùng CVAT:**
  1. **Đổi góc nhìn:** Kéo chuột trái để xoay khung 3D (xoay nhìn thẳng từ trên xuống và lăn chuột thu nhỏ để nhìn toàn cảnh 360°), kéo chuột phải để dời, lăn con lăn để phóng to/thu nhỏ; nháy đúp vào chỗ trống để về góc nhìn mặc định.
  2. **Vẽ và chỉnh cuboid:** Chọn công cụ Cuboid ở thanh trái → chọn class → bấm **Shape** (không dùng Track), đưa hộp tới cụm điểm của vật rồi nháy đúp để đặt; chỉnh tâm, chiều dài/rộng và xoay góc yaw ở khung **Top**, chỉnh độ cao và hạ đáy chạm mặt đường ở khung **Side** và **Front** (không xoay ở Side/Front).
  3. **Bật mũi tên hướng và xem trên ảnh camera:** Mở **Appearance → Cuboid orientation** để hiện vạch đỏ ngắn (`+X` của cuboid) chỉ về đầu xe; sau khi Save trên CVAT, chạy `python scripts/lab.py preview` để xuất ảnh ghép 7 góc camera và góc nhìn từ trên xuống trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f17` và `s01f53`. Ảnh: `t1-s01f17-3d`, `t1-s01f17-top`, `t1-s01f53-3d`, `t1-s01f53-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f17` | TODO(học viên) | | | | | |
| `s01f53` | TODO(học viên) | | | | | |

TODO(học viên): 3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f08` | TODO(học viên) | |
| `s02f41` | TODO(học viên) | |
| `s03f31` | TODO(học viên) | |
| `s03f47` | TODO(học viên) | |
| `s04f04` | TODO(học viên) | |
| `s04f55` | TODO(học viên) | |
| `s05f06` | TODO(học viên) | |
| `s05f23` | TODO(học viên) | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | TODO(học viên) | | | | | |
| (b) xe buýt hoặc xe tải | TODO(học viên) | | | | | |
| (c) người đi bộ hoặc xe hai bánh | TODO(học viên) | | | | | |

TODO(học viên): vòng hiệu chuẩn: cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào? Và các vật bạn **bỏ qua vì không đủ cơ sở** (frame, vị trí, mô tả, loại nghi vấn như UNCERTAIN_CLASS / LOW_VISIBILITY), hoặc ghi "không có".

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | TODO(học viên) | | | |
| 2. Thưa ở xa | TODO(học viên) | | | |
| 3. Phản xạ thấp / ít điểm bất thường | TODO(học viên) | | | |
| 4. Điểm lạ | TODO(học viên) | | | |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |

TODO(học viên): (a) cuboid **gần nhất** và **xa nhất**: `dist_m` và r của từng cái. (b) Kích thước l, w, h của một người đi bộ (hoặc xe hai bánh) và một xe con so với bảng 4.3: chiều nào ngoài khoảng, vì sao. (c) `yaw_deg` của một xe cùng chiều phía trước và một xe ngược chiều (nếu có): có gần 270° và 90° không.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| TODO(học viên) | | |

TODO(học viên): một câu quy luật ("xa gấp … lần thì số điểm còn khoảng …") và so với đường cong ở slide bài giảng (xe con: khoảng 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m). Rồi phần tự kiểm: hai cuboid bạn **đã sửa** sau `preview` / `selfcheck` (frame, idx, sửa gì), và những cảnh báo bạn **giữ nguyên** kèm lý do.

TODO(học viên): đổi kiểm: MSSV người đã kiểm chéo với bạn, hai con số nào được đối chiếu với bảng A, khớp hay lệch (lệch thì đã sửa gì).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
TODO(học viên): (a) hai câu, nói từ phía model sẽ học trên nhãn này: vì sao nhãn 3D đo bằng mét chứ không phải pixel; vì sao vật thưa điểm cần quy tắc riêng. (b) Dùng AI? Ghi 3 dòng: công cụ, câu hỏi chính, bạn dùng kết quả thế nào. Không dùng: ghi "không dùng". (c) Điều còn chưa chắc, lỗi phát hiện sau hạn (không chấm; viết bằng lời, không chép số từ phiếu hồ sơ lỗi).
