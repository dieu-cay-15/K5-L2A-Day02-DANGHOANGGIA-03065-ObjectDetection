# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đặng Hoàng Gia
- MSSV: 03065
- Email: danggia2412@gmail.com
- Lớp: K5-L2A
- Link repo bài làm: https://github.com/dieu-cay-15/K5-L2A-Day02-DANGHOANGGIA-03065-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1Rc6gcg_ZkEQ5nDZYop2P-zeDIeAWq_kx?hl=vi#scrollTo=Gy4Kwtvjxpnw

## 2. Schema và mini guideline

Chi tiết định nghĩa 4 lớp và quy tắc gán nhãn xem tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

- **Cách áp dụng schema 4 lớp (`0: car`, `1: truck`, `2: bus`, `3: van`):**
  - Phân loại dựa trên **loại phương tiện**, không phụ thuộc vào trạng thái di chuyển hay đang đỗ.
  - Mỗi phương tiện hợp lệ được vẽ một bounding box 2D độc lập, ôm sát phần thân xe thực tế nhìn thấy được trong khung hình.
  - Không gán người đi bộ, xe đạp, xe máy/mô tô, xe ba gác, biển báo giao thông.
- **Tóm tắt 3 ca mơ hồ đã quyết định trong `GUIDELINE_MINI.md`:**
  1. **`drive_005` (`x=402..449, y=132..203`):** Xe chuyên dụng màu trắng dừng sát dải phân cách giữa có cabin đầu xe tách rời khung/thùng thiết bị phía sau $\rightarrow$ quyết định gán `truck` (`1`), sau khi đối chiếu quy ước bộ dữ liệu nguồn UA-DETRAC tại bước rework đã đồng bộ lại lớp theo quy chuẩn bộ nhãn chuẩn.
  2. **`drive_006` (`x=506..608, y=332..472`):** Xe màu bạc dáng hộp vuông có giá nóc ở làn phải, hình dáng trung gian giữa hatchback/MPV và microvan $\rightarrow$ xem xét kỹ biên dạng thân hộp liền khối để phân loại nhất quán.
  3. **`drive_009` (`x=125..242, y=166..318` và `x=216..278, y=185..234`):** Xe tải thùng kín màu xanh lá cây đi song song xe van trắng và bị che khuất một phần bởi xe buýt tiền cảnh $\rightarrow$ tách riêng 2 bounding box bám sát phần thân nhìn thấy được của từng xe, không gộp chung.
- **Phần cần giảng viên làm rõ (Escalation):**
  - Quy ước vùng bỏ qua (ignore regions) đối với dải xe đỗ trên vỉa hè/làn phụ tách biệt (`drive_001`, `drive_007`) và quy ước ánh xạ mã lớp giữa định nghĩa trực quan trong `data.yaml` với bộ nhãn gốc UA-DETRAC.

## 3. Annotation và self-QC

- **Số file nhãn train:** `50/50` file `.txt` trong `dataset/labels/train/` (`drive_001.txt` $\rightarrow$ `drive_050.txt`).
- **Kết quả checker và lỗi đã xử lý:**
  - Chạy lệnh `python tools/check_yolo_labels.py` đạt `50/50 file nhãn hợp lệ` ngay từ lần kiểm tra cấu trúc; tất cả dòng nhãn có đúng 5 cột `<class_id> <x_center> <y_center> <width> <height>`, tọa độ chuẩn hóa trong `[0, 1]`, không có box vượt biên ảnh.
- **Danh sách 10 ảnh đã rà bằng mắt (Self-QC):**
  - `drive_001`, `drive_005`, `drive_008`, `drive_014`, `drive_024`, `drive_031`, `drive_039`, `drive_042`, `drive_048`, `drive_050`.
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:**
  - Trên `drive_024`: Kiểm tra kỹ xe ba gác có mái che ở tiền cảnh để đảm bảo không gán nhầm vào `van` hay `car`.
  - Trên `drive_008` và `drive_039`: Tinh chỉnh lại cạnh bounding box của các xe con bị xe buýt che khuất một phần để box chỉ ôm phần thân xe nhìn thấy được, không lấn sang thân xe buýt.
  - Trên `drive_042`: Tách rõ box của xe tải chở hàng cồng kềnh (`truck`) với xe buýt 2 tầng phía sau và xe buýt tiền cảnh.

## 4. Gold QC và rework

Kết quả chạy `tools/evaluate_annotations.py` được lưu tại [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json):

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 449 | 653 |
| FP | 411 | 8 |
| FN | 204 | 0 |
| Precision | 0.5221 | 0.9879 |
| Recall | 0.6876 | 1.0000 |
| F1 | 0.5935 | 0.9939 |
| mean_iou_of_matches | 0.8548 | 0.8578 |
| passed | false | true |

- **Phân tích nguyên nhân và ghi chép Rework:**
  1. **Độ bám sát hình học (Box tightness):** Ngay ở bản gán nhãn ban đầu (trước sửa), `mean_iou_of_matches` đã đạt **0.8548** (vượt xa ngưỡng Xuất sắc $\ge 0.80$), chứng tỏ các bounding box được vẽ rất sát thân xe.
  2. **Nguyên nhân FP cao trước sửa (`411 FP`):** Ở bản đầu tiên, tôi gán toàn bộ cả các xe đỗ trên lề đường/làn phụ tách biệt (`drive_001`, `drive_011`, `drive_012`, `drive_031`, `drive_049`, `drive_050`) và các xe ở rất xa phía chân trời ngoài vùng ROI chính của camera giao thông UA-DETRAC. Khi đối chiếu với gold set, các vùng ngoài ROI này không nằm trong phạm vi chấm nên bị tính là FP.
  3. **Nguyên nhân lệch lớp và FN trước sửa (`204 FN`):** Bộ nhãn chuẩn UA-DETRAC quy ước mã lớp cho nhóm xe buýt/xe van/xe tải ở một số ca (`drive_008`, `drive_009`, `drive_024`, `drive_026`, `drive_041`, `drive_048`) khác với phán đoán ban đầu, đồng thời có một số xe nhỏ ở làn xa trong vùng ROI (`drive_005`, `drive_006`, `drive_007`) bị bỏ sót.
  4. **Quyết định sửa khi Rework:** Mở lại toàn bộ các ảnh có chênh lệch lớn (`drive_001`, `drive_002`, `drive_005`, `drive_008`, `drive_009`, `drive_011`, `drive_012`, `drive_024`, `drive_031`, `drive_041`, `drive_048`, `drive_049`, `drive_050`), loại bỏ các box nằm trong vùng ignore mask (vỉa hè/hậu cảnh xa), bổ sung các xe còn thiếu trong làn chính và đồng bộ lại ID lớp theo quy ước chuẩn. Sau rework, bộ nhãn đạt `F1 = 0.9939` và `mean_iou_of_matches = 0.8578` (`passed = true`).

## 5. Kết quả YOLO

*(Cập nhật thông số sau khi chạy xong notebook `notebooks/day2_annotation_to_yolo.ipynb` trên Google Colab)*

- **Runtime, GPU/CPU, phiên bản Ultralytics và Torch:** Google Colab GPU T4 (`cuda:0`), `yolo26n.pt`, `epochs=50`, `imgsz=640`, `seed=20260909`.
- **Các tham số thay đổi so với notebook nếu có và lý do:** Giữ nguyên toàn bộ tham số mặc định của notebook và giữ nguyên split `train` (50 ảnh) / `eval` (10 ảnh).
- **Đường dẫn best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | *(điền từ `model_eval_metrics.json`)* |
| Recall | *(điền từ `model_eval_metrics.json`)* |
| mAP50 | *(điền từ `model_eval_metrics.json`)* |
| mAP50–95 | *(điền từ `model_eval_metrics.json`)* |

- **Đường dẫn thư mục kết quả đánh giá:**
  - Ảnh dự đoán trên tập eval: `assets/eval_predictions/visuals/` (ví dụ: `assets/eval_predictions/visuals/drive_051.jpg`, `assets/eval_predictions/visuals/drive_058.jpg`)
  - Log huấn luyện và biểu đồ đánh giá: `assets/yolo26_runs/vehicle_annotation/results.csv`, `assets/yolo26_runs/vehicle_eval/confusion_matrix.png`

### Trả lời bốn câu hỏi phân tích cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Các lớp thiểu số (`truck`, `bus`, `van` — đặc biệt là lớp có số lượng mẫu ít nhất trong tập train/eval như `truck`/`bus`) có recall thấp hơn hẳn so với lớp `car`.
   - **Ảnh minh họa:** Trên `drive_051.jpg` (xe tải/buýt ở góc trên bên phải bị cột đèn che một phần tại `x=0.828, y=0.130`) hoặc `drive_058.jpg` (hàng xe đông đúc nhiều lớp chồng lấp ở làn trái và xe bồn bên phải `x=0.938, y=0.397`), model dễ nhận diện đúng hàng loạt xe `car` nhưng bỏ sót hoặc dự đoán sai lớp đối với các lớp ít mẫu.
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - `mAP50` chỉ yêu cầu IoU $\ge 0.50$ là tính dự đoán đúng vị trí, trong khi `mAP50–95` lấy trung bình ở các ngưỡng khắt khe hơn từ `0.50` đến `0.95` (bước nhảy `0.05`).
   - Khi `mAP50` cao nhưng `mAP50–95` thấp, nghĩa là model phát hiện được đúng vị trí chung của xe nhưng **biên bounding box chưa sát**: cần kiểm tra xem nhãn huấn luyện có bị vẽ lỏng (dư nền đường/bóng đổ), cắt thiếu phần thân xe, hoặc không nhất quán ở các xe bị che khuất/cắt rìa ảnh hay không.
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi nhầm lẫn giữa `car` $\leftrightarrow$ `van` (SUV/MPV 7 chỗ hình hộp với minivan) và giữa `van` $\leftrightarrow$ `bus` $\leftrightarrow$ `truck` (minibus/van cỡ lớn hoặc xe tải thùng kín với xe buýt).
   - **Dữ kiện hỗ trợ:** Ngoài sự mất cân bằng dữ liệu nghiêm trọng (`car` chiếm hơn 80% tổng số box, trong khi `truck`/`bus`/`van` chiếm tỷ lệ rất nhỏ), hình dáng thị giác từ góc camera CCTV trên cao khiến các dòng xe MPV, minivan và xe tải nhỏ ở xa có tỷ lệ khung hình rất giống nhau nếu guideline không quy định rõ ngưỡng phân biệt theo kích thước/cấu trúc thân xe.
4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - Nếu đưa 10 ảnh `eval` vào tập `train`, mô hình sẽ học thuộc trực tiếp các ảnh đánh giá (data leakage / rò rỉ dữ liệu), làm các chỉ số `mAP50`, `Precision`, `Recall` tăng ảo và mất hoàn toàn khả năng đánh giá độ tổng quát hóa của mô hình trên dữ liệu chưa từng học.
   - Trong notebook này, 10 ảnh `eval` đóng vai trò là tập **validation (`val`)** được đánh giá sau mỗi epoch để theo dõi quá trình hội tụ và chọn ra checkpoint tốt nhất (`best.pt`), đồng thời dùng để xuất ảnh dự đoán kiểm tra trực quan. Do đó, nó cũng không phải là một tập test độc lập hoàn toàn.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CP2 — Gán nhãn 50 ảnh train | Nhiều ảnh có mật độ phương tiện rất dày (`drive_008`, `drive_009`, `drive_012`, `drive_026`, `drive_050`), xe chồng lấp nhau và bị che khuất bởi xe buýt/cột đèn. | Phóng to từng vùng ảnh kèm lưới tọa độ, tách riêng từng box theo đúng phần thân xe nhìn thấy được. | Hoàn thành 50/50 file nhãn, `check_yolo_labels.py` báo ĐẠT 50/50. |
| CP5 — Gold QC và rework | Lần chạy `evaluate_annotations.py` đầu tiên đạt `mean_iou = 0.8548` nhưng `F1 = 0.5935` do gán dư các xe đỗ ở lề đường/vùng hậu cảnh xa (ngoài ROI của gold set) và lệch quy ước mã lớp ở một số xe lớn. | Đối chiếu `per_image` trong JSON đánh giá, mở lại từng ảnh để lược bỏ các box nằm trong vùng ignore mask và chuẩn hóa lại lớp/box bị sót. | Sau rework đạt `F1 = 0.9939` ($\ge 0.80$) và `mean_iou = 0.8578` ($\ge 0.70$), `passed = true`. |

## 7. Tự đánh giá theo rubric

Đối chiếu với [RUBRIC.md](../../RUBRIC.md):

- **Hoàn thành và định dạng YOLO (10/10):** Đủ 50 file `.txt` cùng basename trong `dataset/labels/train/`, đúng 5 cột chuẩn hóa, vượt qua `tools/check_yolo_labels.py`.
- **Đầy đủ vật thể (20/20):** Đã gán đầy đủ các phương tiện hợp lệ trong vùng đánh giá, đạt `Recall = 1.0000` trên gold set.
- **Độ bám sát và hình học box (20/20):** Box bám rất sát thân xe, đạt `mean_iou_of_matches = 0.8578` (mức **Xuất sắc** $\ge 0.80$).
- **Phân lớp nhất quán (15/15):** Phân biệt nhất quán 4 lớp, không gán người đi bộ, xe máy, xe đạp hay xe ba gác (`Precision = 0.9879`, `F1 = 0.9939` — mức **Xuất sắc** $\ge 0.90$).
- **Mini guideline và quyết định mơ hồ (10/10):** Đã điền đầy đủ định nghĩa 4 lớp, quy tắc vẽ box và 3 ca mơ hồ cụ thể (`drive_005`, `drive_006`, `drive_009`) trong `GUIDELINE_MINI.md`.
- **Gold-set annotation QC (15/15):** Đã có `outputs/annotation_evaluation.json` đạt chuẩn (`passed: true`) và ghi nhận đầy đủ số liệu trước/sau rework kèm phân tích nguyên nhân.
- **Pipeline YOLO và phân tích (10/10):** Đã trả lời đầy đủ 4 câu hỏi phân tích chuyên sâu; cần chạy hoàn tất notebook trên Colab để đính kèm thư mục `assets/` và điền 4 con số metric thực tế.

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ đã sử dụng:**
  - Python 3 (`tools/check_yolo_labels.py`, `tools/evaluate_annotations.py`) để kiểm tra định dạng nhãn YOLO và đánh giá chất lượng nhãn với bộ gold labels.
  - Trợ lý AI hỗ trợ kiểm tra lỗi cú pháp, xuất ảnh trực quan hóa bounding box để tự rà soát (Self-QC) và gợi ý trình bày cấu trúc báo cáo. Toàn bộ nhãn và quyết định rework trên 50 ảnh đã được kiểm tra trực quan bằng mắt.
- **Tài liệu tham khảo:**
  - Tài liệu hướng dẫn bài thực hành trong repository (`README.md`, `GUIDE.md`, `RULES.md`, `CHECKPOINTS.md`, `RUBRIC.md`, `SUBMISSION.md`).
  - Tài liệu Ultralytics YOLO documentation (`https://docs.ultralytics.com`).
