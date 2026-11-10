# Checkpoints — Ngày 2

Thời lượng gốc: 4 giờ. Làm theo thứ tự trong [GUIDE.md](GUIDE.md); hình thức và nộp bài xem [SUBMISSION.md](SUBMISSION.md).

| Checkpoint | Phút | Sản phẩm |
|---|---:|---|
| CP1 — Schema và guideline | 25 | Mini guideline và 3 ca mơ hồ |
| CP2 — Gán nhãn | 110 | 50 file YOLO train |
| CP3 — Self-QC | 25 | Checker đạt, đã rà ít nhất 10 ảnh |
| CP4 — Khóa nhãn | 20 | Commit bản ban đầu trước khi nhận gold |
| CP5 — Gold QC và rework | 20 | Annotation evaluation JSON và ghi chép sửa nhãn |
| CP6 — YOLO train/eval | 40 | Colab, metrics và ảnh prediction trên eval |

## CP1 — Schema và guideline

- **Cần làm:** đọc schema car=0, truck=1, bus=2, van=3; điền mini guideline trước khi gán nhãn.
- **Sản phẩm:** định nghĩa bốn lớp, quy tắc box và ít nhất ba ca mơ hồ có tên ảnh/quyết định/lý do.
- **Cần hiểu:** class mô tả loại vật thể; xe đỗ, bị che hoặc bị cắt khung vẫn cần xử lý theo guideline.
- **Tự kiểm tra:** giải thích được quyết định trong ba ca mà không cần xem gold.

## CP2 — Gán nhãn 50 ảnh train

- **Cần làm:** gán đúng phạm vi bốn lớp, mỗi xe một box theo phần nhìn thấy; xuất YOLO detection.
- **Sản phẩm:** 50 file trong `dataset/labels/train/`, cùng basename ảnh. Ảnh không có xe hợp lệ vẫn có file rỗng.
- **Cần hiểu:** tâm/rộng/cao chuẩn hóa, ID lớp cố định; không trộn eval vào train.
- **Tự kiểm tra:** đủ 50 file, không đổi tên ảnh, không dùng gold train thay công việc tự gán nhãn.

## CP3 — Self-QC

- **Cần làm:** chạy `python3 tools/check_yolo_labels.py`; mở ngẫu nhiên ít nhất 10 ảnh để rà nhãn.
- **Sản phẩm:** nhãn đúng định dạng và danh sách ảnh đã kiểm trong báo cáo.
- **Cần hiểu:** checker kiểm cấu trúc, không chứng minh đủ xe/đúng class/box sát.
- **Tự kiểm tra:** checker đạt 50/50 và giải thích được cách kiểm nội dung bằng mắt.

## CP4 — Khóa bộ nhãn ban đầu

- **Cần làm:** commit, push và nộp/khóa nhãn theo mốc lớp trước khi nhận gold.
- **Sản phẩm:** commit bản ban đầu để truy lại sau rework.
- **Cần hiểu:** gold chỉ được phát sau khi cả lớp khóa nhãn; không sửa lịch sử để thay bản đã khóa.
- **Tự kiểm tra:** nhãn đã có trên GitHub; ghi commit vào báo cáo; chưa nhận/xem gold trước mốc phát.

## CP5 — Gold QC và rework

- **Cần làm:** nhận gold đúng mốc, chạy evaluator theo GUIDE, mở lại ảnh và sửa nhãn của mình nếu chưa đạt.
- **Sản phẩm:** `outputs/annotation_evaluation.json`; kết quả trước/sau và lý do rework trong báo cáo.
- **Cần hiểu:** matching cùng class, IoU≥0.50; F1 annotation khác mAP model, FP/FN chưa đủ để kết luận nguyên nhân.
- **Tự kiểm tra:** trước khi train cần cả F1≥0.80 và mean IoU≥0.70. Nếu vẫn chưa đạt sau rework, báo giảng viên; không sửa gold để vượt gate.

## CP6 — Train, đánh giá và phân tích YOLO

- **Cần làm:** chạy notebook với nhãn đã QC; train YOLO26n và val/predict trên 10 ảnh eval, giữ nguyên split.
- **Sản phẩm:** link Colab đã chạy; precision, recall, mAP50, mAP50–95 và ảnh prediction; trả lời bốn câu hỏi cuối notebook trong báo cáo.
- **Cần hiểu:** eval cũng là validation dùng chọn best checkpoint, không phải test set độc lập. mAP thấp không tự chứng minh annotation sai.
- **Tự kiểm tra:** đủ 50 train/10 eval; không commit gold train vào repo; có kết quả thực tế và giải thích một ảnh cụ thể.
