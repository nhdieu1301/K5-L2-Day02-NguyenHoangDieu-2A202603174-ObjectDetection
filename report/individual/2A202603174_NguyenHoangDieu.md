# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Hoàng Diệu
- MSSV: 2A202603174
- Email: nhdieu1301@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/nhdieu1301/K5-L2-Day02-NguyenHoangDieu-2A202603174-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1aSOO7F0NmgMHDTY_IPD3dhYIcehGFKn1?usp=sharing

## 2. Schema và mini guideline

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_049` | Nhiều xe rất nhỏ ở xa, không phân biệt được lớp. | Gán car | Schema chỉ có 4 lớp, xe quá nhỏ không đủ nhận diện lớp. |
| `drive_009` | Xe nằm sát nhau, che khuất lẫn nhau. | Gán | Đề: bị che vẫn cần box nếu phần nhìn thấy đủ để nhận lớp. |
| `drive_042` | Xe bị cắt bởi rìa ảnh. | Gán car | Xe bán tải thuộc car / thân hộp chở người thuộc van. |
Phần cần giảng viên làm rõ: ranh giới `van`/`car`

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_10, drive_13, drive_46, drive_23, drive_31, drive_43, drive_17, drive_18, drive_07, drive_02
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:  `drive_049`: Nhiều xe rất nhỏ ở xa, không phân biệt được lớp.  `drive_009`: Xe nằm sát nhau, che khuất lẫn nhau. `drive_042`: Xe bị cắt bởi rìa ảnh.
- Commit khóa nhãn ban đầu: `3a46c90`

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 447 | — |
| FP | 570 | — |
| FN | 206 | — |
| Precision | 0.4395 | — |
| Recall | 0.6845 | — |
| F1 | 0.5353 | — |
| mean_iou_of_matches | 0.7772 | — |
| passed | false | — |

Kết quả đầy đủ (kèm số liệu từng ảnh) nằm trong [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json).

**Đánh giá theo ngưỡng của đề (IoU 0.50):**
- `mean_iou_of_matches` = 0.7772, đạt yêu cầu (≥ 0.70).
- `F1` = 0.5353, **không đạt** yêu cầu (≥ 0.80), nên `passed = false`.

**Rework: không thực hiện.** Em không sửa nhãn sau khi có kết quả gold QC, nên các chỉ số trên là kết quả duy nhất. Lý do không rework: ưu tiên hoàn thành phần train và đánh giá YOLO.

**Hệ quả:** Bộ nhãn chưa đạt ngưỡng annotation nhưng vẫn được dùng để train YOLO, nên kết quả ở mục 5 phản ánh chất lượng nhãn này, không phải bộ nhãn đã được chuẩn hóa.

**Chưa kiểm:** Em chưa mở lại từng ảnh để đối chiếu với gold, nên chưa xác định được nguyên nhân cụ thể của FP (570) và FN (206). Số liệu cho thấy một số ảnh có số box của em chênh nhiều so với gold (ví dụ `drive_049`: 33 box so với 6; `drive_044`: 31 so với 8; `drive_038`: 45 so với 17), nhưng đây chỉ là chênh lệch số lượng, chưa phải kết luận về lỗi.
## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU Tesla T4 (14913 MiB), Ultralytics 8.4.208, Torch 2.14.1+cu130, Python 3.13.16.
- Các tham số thay đổi so với notebook nếu có và lý do: [không thay đổi, dùng đúng cell train của notebook: `yolo26n.pt`, epochs=50, imgsz=640, batch=-1, seed=20260909] (bạn sửa lại nếu thực tế có đổi tham số).
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`
- Nhãn dùng để train: bộ nhãn khóa ở commit `3a46c90`. Bộ này chưa đạt gold QC (F1 = 0.5353 < 0.80) và không rework, nên kết quả dưới đây phản ánh cả chất lượng nhãn đó.
- Link Colab: https://colab.research.google.com/drive/1aSOO7F0NmgMHDTY_IPD3dhYIcehGFKn1?usp=sharing

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4105 |
| Recall | 0.2303 |
| mAP50 | 0.2723 |
| mAP50–95 | 0.2017 |

Theo từng lớp (cell đánh giá `best.pt`):

| Lớp | Số vật thể eval | P | R | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0.833 | 0.638 | 0.739 | 0.523 |
| truck | 5 | 0.675 | 0.200 | 0.239 | 0.206 |
| bus | 2 | 0 | 0 | 0.008 | 0.004 |
| van | 12 | 0.134 | 0.083 | 0.103 | 0.074 |

Kết quả và ảnh dự đoán: [`assets/yolo26_runs/`](assets/yolo26_runs/), [`assets/eval_predictions/`](assets/eval_predictions/).

Ảnh/vật thể cụ thể: trong [`assets/eval_predictions/visuals/drive_053.jpg`](assets/eval_predictions/visuals/drive_053.jpg), model dự đoán 3 `bus` (và 7 `car`, 1 `van`), trong khi cả tập eval chỉ có 2 `bus` thật và P/R của `bus` đều bằng 0. Vậy các box `bus` này là nhận sai lớp hoặc dư.

Lưu ý: tập eval vừa là validation (chọn `best.pt`) vừa dùng để báo cáo, nên đây không phải test độc lập. `bus` chỉ có 2 mẫu và `truck` 5 mẫu, nên chỉ số của các lớp này dao động mạnh.

### Trả lời bốn câu hỏi cuối notebook

1. **Lớp recall thấp nhất:** `bus`, recall = 0 (0/2 vật thể). Ảnh minh họa là `drive_053`, model gán 3 box `bus` nhưng không khớp vật thể `bus` thật nào. [Ghi thêm ảnh có `bus` thật mà model bỏ sót, nếu bạn tìm thấy.] `van` (0.083) và `truck` (0.200) cũng rất thấp. `car` có recall cao nhất (0.638) vì có 94/113 vật thể nên model học nhiều nhất.

2. **mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box:** mAP50–95 tính trung bình ở các ngưỡng IoU từ 0.50 đến 0.95, nên nếu mAP50 cao mà mAP50–95 thấp thì box tìm đúng xe nhưng chưa khít. Cần kiểm tra box có bám sát thân xe không (chừa nền thừa hoặc cắt mất một phần xe), box có nhất quán giữa các ảnh không (cùng loại xe, cùng tình huống che khuất), và box có lệch về một phía không. Trong bài này mAP50 chỉ đạt 0.272 (mAP50–95 là 0.202), nên vấn đề chính chưa phải độ khít của box mà là model bỏ sót và nhận sai lớp, nhất là `bus`, `van`, `truck`. Riêng lớp `car` (0.739 so với 0.523) mới thể hiện khoảng cách do độ khít của box.

3. **Lỗi class liên quan guideline:** model nhận sai chủ yếu ở nhóm xe lớn/hộp (`van`, `bus`, `truck`). Dữ kiện: `van` có 12 vật thể nhưng P = 0.134, R = 0.083. Ở `bus`, model dự đoán 9 box (conf 0.25) trên 10 ảnh trong khi gold chỉ có 2. Điều này có thể liên quan guideline vì ranh giới `van`/`bus`/`car` trong định nghĩa của em còn mơ hồ, nên nhãn train có thể gán không nhất quán ở các xe này. [Mở `assets/yolo26_runs/vehicle_eval/confusion_matrix.png`, ghi cặp lớp bị nhầm nhiều nhất và ảnh minh họa, rồi đối chiếu với các ca mơ hồ trong `GUIDELINE_MINI.md`.] Do dữ liệu ít, đây chỉ là nhận xét, chưa phải kết luận chắc chắn.

4. **Vì sao không dùng 10 ảnh eval để train, vai trò validation:** nếu train bằng chính ảnh eval thì model đã thấy đáp án, mAP cao giả và không phản ánh khả năng với ảnh mới (rò rỉ dữ liệu). Trong notebook, 10 ảnh này là tập validation: đo chất lượng sau mỗi epoch và chọn checkpoint `best.pt`, không tham gia cập nhật trọng số.

## 6. Khó khăn và cách xử lý

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chạy `evaluate_annotations.py` trên CMD (Windows) | Báo `error: the following arguments are required: --student-labels, --gold-labels`. Các dòng `--student-labels ...` bị CMD báo "not recognized as an internal or external command". Lệnh `delete` cũng không được nhận. | Lệnh trong README viết kiểu bash (xuống dòng bằng `\`), mà CMD không dùng `\` để nối dòng. Gõ toàn bộ lệnh trên **một dòng** (hoặc dùng `^` để xuống dòng trong CMD). | Chạy được, tạo `outputs/annotation_evaluation.json`. |
| Gold QC lần 1 | F1 = 0.5353 < 0.80 (Precision 0.4395, Recall 0.6845; FP = 570, FN = 206). `mean_iou_of_matches` = 0.7772 đạt. | Xem số box theo từng ảnh trong `per_image`: một số ảnh có số box của em nhiều hơn gold rõ rệt (ví dụ `drive_049`: 33 so với 6; `drive_044`: 31 so với 8; `drive_038`: 45 so với 17). | Chưa rework, nên F1 vẫn dưới ngưỡng. Chưa mở ảnh đối chiếu nên chưa kết luận được nguyên nhân cụ thể. |
| Train và đánh giá YOLO trên Colab | Kết quả thấp: mAP50 = 0.2723, mAP50–95 = 0.2017; recall của `bus` = 0, `van` = 0.083. | Chạy notebook theo tham số có sẵn trên GPU Tesla T4, không chỉnh để "làm đẹp" số. | Chưa cải thiện vì nhãn chưa đạt ngưỡng và chưa train lại. Số liệu `bus`/`truck` chỉ dựa trên 2 và 5 vật thể nên không ổn định. |

## 7. Tự đánh giá theo rubric

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md) và danh sách bài nộp trong README.

**Đã có kết quả:**
- Nhãn YOLO 50 ảnh train, đã commit và push (commit `3a46c90`); notebook Colab clone repo và xác nhận đủ 50 ảnh train, 10 ảnh eval.
- Kết quả gold QC lần 1: `outputs/annotation_evaluation.json`.
- Colab đã chạy hết các cell: train 50 epoch, đánh giá `best.pt` trên 10 ảnh eval, xuất ảnh dự đoán và `day2_colab_outputs.zip`. Chỉ số: Precision 0.4105, Recall 0.2303, mAP50 0.2723, mAP50–95 0.2017.
- `GUIDELINE_MINI.md`: [đã điền định nghĩa lớp, quy tắc chung và 3 ca mơ hồ / chưa hoàn thiện, ghi đúng tình trạng].

**Chưa đạt:**
- Annotation chưa qua cổng gold: F1 = 0.5353 < 0.80 (`mean_iou_of_matches` = 0.7772 đạt ngưỡng 0.70). Không rework, nên chưa đạt điều kiện đi tiếp.
- Model train trên bộ nhãn chưa đạt ngưỡng, nên chỉ số YOLO thấp và chỉ có giá trị minh họa; recall của `bus` = 0, `van` = 0.083.

**Chưa kiểm được / còn thiếu:**
- Chưa mở ảnh đối chiếu với gold để xác định nguyên nhân FP (570) và FN (206).
- [Kết quả `check_yolo_labels.py` và danh sách ≥ 10 ảnh đã rà bằng mắt: ghi "đã có" hoặc "chưa làm".]
- [Câu 1 và 3 mục 5: ảnh/vật thể cụ thể và confusion matrix: ghi "đã xem" hoặc "chưa xem".]
- [`assets/yolo26_runs/` và `assets/eval_predictions/` đã đưa vào repo chưa.]
- Tập eval vừa là validation vừa dùng để báo cáo, nên kết quả không phải test độc lập.

## 8. Công cụ AI và tài liệu tham khảo

## 8. Công cụ AI và tài liệu tham khảo

**Công cụ AI:** Claude (Anthropic), dùng ở các bước sau:
1. Giải thích lỗi chạy lệnh trên CMD (dấu `\` không nối dòng, lệnh `delete` không có trong Windows) và đưa ra lệnh viết trên một dòng.
2. Soạn khung bảng định nghĩa lớp và quy tắc chung cho `GUIDELINE_MINI.md` dựa trên schema trong README.
3. Soạn khung các mục của báo cáo này và điền các số liệu em đã chạy (kết quả `evaluate_annotations.py`, output notebook Colab).
4. Gợi ý cách trả lời bốn câu hỏi cuối notebook.

**Giới hạn của công cụ:** Claude không xem được ảnh trong bộ dữ liệu, không mở được link Colab, và không có nhãn gold. Mọi nhận xét về ảnh, vật thể cụ thể, ca mơ hồ và lỗi class phải do em tự quan sát.

**Phần em đã tự kiểm tra:** [Ghi đúng thực tế, ví dụ: tự chạy các lệnh và dán kết quả; tự mở ảnh `drive_...` để viết ca mơ hồ; tự xem confusion matrix và ảnh dự đoán trong `assets/` để trả lời câu 1 và 3; đối chiếu số liệu trong báo cáo với `annotation_evaluation.json` và output Colab. Chỉ giữ những việc em thật sự đã làm.]

**Nguồn ngoài repo:** [Không có / ghi nguồn đã dùng, ví dụ tài liệu Ultralytics].

Báo cáo không chứa mật khẩu, token hay gold train labels.
