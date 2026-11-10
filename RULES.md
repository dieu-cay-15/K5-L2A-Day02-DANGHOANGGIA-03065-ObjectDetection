# Quy định làm bài

## Bài cá nhân và sử dụng AI

Mỗi học viên tự làm bài và chịu trách nhiệm về nhãn, mini guideline, notebook và phần phân tích của mình. Có thể dùng AI để hỏi khái niệm, đọc lỗi hoặc góp ý cách diễn đạt; ghi công cụ đã dùng và phần đã tự kiểm tra trong báo cáo. Không nộp nhãn hoặc phân tích do người khác/AI làm thay như bài của mình.

Giữ phương pháp annotation theo hướng dẫn của lớp. Không dùng gold train hoặc truy vết nguồn dữ liệu để lấy đáp án trước khi khóa nhãn.

## Hợp tác và sao chép

Có thể trao đổi cách dùng công cụ và cách hiểu schema. Không sao chép bộ nhãn, guideline hoặc kết quả chạy của người khác. Ghi nguồn khi dùng tài liệu hoặc code ngoài bài. Nếu có ca mơ hồ, ghi ảnh, quyết định và hỏi giảng viên thay vì tự đổi schema.

## Gold, split và dữ liệu

- Chỉ nhận gold train sau khi cả lớp khóa/nộp nhãn; giữ commit ban đầu để đối chiếu rework.
- Không sửa gold labels; chỉ sửa nhãn do mình tạo và ghi lý do.
- Không đưa ảnh/nhãn eval vào train. Không tìm nhãn ngoài nguồn bài học.
- Không commit gold train, file nội bộ giảng viên hoặc upload chúng lên Colab.
- Không đưa dữ liệu cá nhân, dữ liệu khách hàng hoặc dữ liệu nhạy cảm ngoài bài vào repo.

## Tài khoản và secret

Không commit mật khẩu, API key, token hoặc file `.env`. Tự đăng nhập CVAT/Colab/GitHub trên tài khoản của mình; không ghi thông tin đăng nhập vào báo cáo hoặc ảnh chụp. Che secret nếu cần chụp lỗi.

## Nộp muộn, sửa sau deadline và lỗi kỹ thuật

Deadline và nơi nộp theo [SUBMISSION.md](SUBMISSION.md). Gia hạn, mức xử lý nộp muộn và việc chấp nhận commit sau deadline theo thông báo chính thức của Key Coach trên kênh lớp/LMS; không tự coi việc push bổ sung là đã được gia hạn. Các giới hạn điểm bắt buộc của lab nằm trong [RUBRIC.md](RUBRIC.md), không tự cộng thêm mức phạt chưa được thông báo.

Nếu lỗi công cụ/runtime, lưu thông báo, thời điểm, bước đang làm và cách đã thử; báo giảng viên trong buổi và ghi vào báo cáo. Không tạo số liệu thay kết quả chưa chạy được.
