# Danh sách lớp v0 — Trần Như Thái · G04

# class_list_v0 — Day12-G04

Cơ sở: Guideline Cabin v1.0 (GUIDE.md §4) và các khung S01–S08, D01–D06 đã gán.

## DMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` + `seatbelt` | seat = driver; seatbelt: worn_correct / worn_incorrect / not_worn / unknown | Xác định tài xế và nhắc thắt dây an toàn | Một phần: thấy dây qua hình dạng, nhưng dây chìm trong bóng tối thì `unknown` |
| 2 | `face` | box mặt (trán đến cằm, tai đến tai) | Điểm xuất phát để đọc mắt, hướng nhìn và tình trạng tài xế; mặt không thấy thì không vẽ | Có, nhận bằng hình dạng và độ sáng |
| 3 | `eye_left` / `eye_right` | open / closed / unknown (gán riêng từng mắt) | Phát hiện mắt nhắm lâu (dấu hiệu buồn ngủ); `closed` chỉ mô tả khung hình này | Có, trừ khi đốm phản xạ hoặc kính che mắt thì `unknown` |
| 4 | `glasses` | none / clear / sun / unknown | Biết mắt có bị kính che hay không, để tin hay không tin kết quả đọc mắt | Một phần: kính râm trên NIR có thể trông trong suốt, nên khó phân biệt `clear` và `sun` |
| 5 | `gaze_zone` | road / mirror_l / mirror_r / mirror_c / cluster / center / lap / other | Đánh giá mức tập trung vào đường và phát hiện xao nhãng | Có, suy từ hướng mắt và đầu; kém khi mặt nhỏ hoặc mắt bị che |
| 6 | `face13` | 13 điểm mặt, cờ occluded / outside | Đo chi tiết khe mắt, miệng, tư thế đầu; chỉ vẽ khi mặt rộng từ 60 px | Có với điểm thấy rõ; điểm bị che thì đánh cờ, không đặt cho đủ |
| 7 | `hand` | whose: driver / passenger; side: left / right; on_wheel: yes / no / unknown | Biết tay có trên vô lăng không (xe lái tự động thì `no` vẫn bình thường), nhận diện thao tác tay | Có, nhận bằng hình dạng |
| 8 | `phone` | state: held / mounted / lap | Phát hiện dùng điện thoại khi lái | Một phần: chỉ vẽ khi thấy cạnh, góc hoặc màn hình; hộp sáng trên màn hình không tính |

## OMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` + `seat` | seat theo xe: rear_left / rear_middle / rear_right | Đếm và định vị người ngồi hàng sau (trái ảnh là `rear_right`) | Có (hình dạng) |
| 2 | `face` (chỉ box) | box mặt, không có thuộc tính mắt | Xác nhận một hình người là người thật, không phải ghế hay đồ vật | Có (hình dạng) |
| 3 | `seatbelt` (OMS) | not_worn / unknown (SVIRO không có worn_*) | Nhắc thắt dây an toàn cho ghế sau | Một phần; SVIRO không có mẫu `worn_*` nên chưa dạy được lớp này |
| 4 | `child_seat` | seat; occupied: no / unknown (không có `yes`) | Biết ghế trẻ em đang trống hay không rõ | Có (hình dạng); bên trong ghế khi tối hoặc nhòe thì `unknown` |
| 5 | `infant_seat` | seat; facing: rear / front | Nhắc quên em bé trong xe; tắt túi khí nếu ghế em bé đặt ở ghế phía trước | Có (hình dạng) |
| 6 | `object` | seat; type: bag / box / cup / other | Phát hiện đồ bị bỏ quên; tránh nhầm đồ vật với người | Có (hình dạng); không dùng màu để đoán loại |
| 7 | tag `frame_meta` | modality: rgb / ir; light: day / night / unknown; camera_blocked: yes / no | Biết ảnh thuộc loại nào và camera có bị che không để chọn cách xử lý | `light` trên SVIRO luôn `unknown` vì ảnh dựng nên |

## 2 câu hỏi leo thang

1. Câu hỏi: Ghế trẻ em có trẻ ngồi thì `child_seat.occupied` nên là gì? Guideline chỉ cho `no` hoặc `unknown`, không có `yes`, mà OMS cần biết ghế có trẻ để nhắc quên trẻ.
   Khung ví dụ: S01_rgb (`child_seat` `rear_right`), S03_rgb, S04_rgb, S08_rgb.
   Bạn tạm gán thế nào và vì sao: Tạm gán `occupied = unknown`, giữ box ghế, không gán `person` hay `face` cho trẻ. Lý do: SVIRO không gán hình trẻ em ngồi, hình trong ghế quá tối hoặc nhòe để xác nhận, và guideline không có giá trị `yes`. Nhưng `unknown` không phân biệt được ghế có trẻ với ghế không rõ bên trong, nên mất thông tin quan trọng của OMS.

2. Câu hỏi: `frame_meta.light` mô tả ánh sáng cảnh ngoài kính hay độ sáng bên trong cabin? §3 nói ảnh SVIRO dựng nên `light = unknown`, nhưng ảnh RGB có cảnh ngoài kính trông như ban đêm.
   Khung ví dụ: S03_rgb, S08_rgb (cảnh ngoài kính tối), so với S01_rgb (cảnh ngoài kính sáng).
   Bạn tạm gán thế nào và vì sao: Tạm gán `light = unknown` cho mọi ảnh S##, theo §3 và quy tắc "không có căn cứ thì unknown". Lần review chéo trước tôi gán `day` và `night` theo cảnh ngoài kính, bạn cùng nhóm gán `unknown`, và bên `unknown` đúng.
