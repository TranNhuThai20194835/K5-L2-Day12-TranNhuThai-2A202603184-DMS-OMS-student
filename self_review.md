# Tự đánh giá — Trần Như Thái · G04

## 1. Tự rà soát 4 cảnh

Nghỉ tay 1 phút, mở lại bài của mình, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ.

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| S01_rgb | frame_meta.light | `guess` | Gán `day` theo cảnh ngoài kính; SVIRO là ảnh dựng nên phải `unknown` (§3) | Đã sửa thành `unknown` |
| S03_rgb | child_seat.occupied; person và face của trẻ, em bé | `guess`, `extra` | Ghi `occupied = yes` (guideline không có `yes`); gán person/face cho trẻ và em bé trong ghế dù SVIRO không gán hình trẻ em ngồi | Đã sửa: `occupied = unknown`, đã xóa person/face của trẻ |
| D01 | eye_left và các điểm left_eye_* | `flag` | cabin_check cảnh báo `eye_left=unknown` nhưng không điểm left_eye_* nào có cờ | đã bật occluded) |
| D02 | left_eye_outer so với right_eye_outer | `ok` hoặc `lr_flip` | cabin_check cảnh báo trái/phải; ảnh D xoay 90° nên cần kiểm theo y (mắt trái có y lớn hơn) |kết quả kiểm y và đã sửa chưa |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: `guess`, tức gán giá trị khi chưa có căn cứ. Cụ thể là `light = day/night` trên 8 ảnh RGB và `occupied = yes` trên 8 box child_seat.

Vì sao mình mắc: Mình nhìn cảnh ngoài kính thấy sáng hay tối nên cho rằng đủ để chọn `day`/`night`, và thấy trẻ ngồi trong ghế nên ghi `yes` mà không đối chiếu tập giá trị cho phép trong bảng §4.1.

Lần sau tránh bằng cách: Trước khi chọn giá trị cho một thuộc tính, mình tra lại bảng §4.1 và §3 của dataset; nếu không có căn cứ thì ghi `unknown`. Mình cũng chạy `cabin_check` sớm hơn, không đợi đến cuối buổi.

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai: `frame_meta.light` ở S01_rgb (mình `day`, bạn `unknown`). Mình học được: guideline là nguồn quyết định, không phải cảm nhận từ ảnh. Ngoài ra, dòng `occupied` cho mình thấy mình phải kiểm giá trị có thuộc tập cho phép không trước khi gán, và từ đó mình mới phát hiện cả hai đều đã gán person/face cho trẻ em nên phải xóa.
