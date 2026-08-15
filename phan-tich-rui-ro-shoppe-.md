# Phân tích Rủi ro Hệ thống - Ứng dụng Shopee

## 1. Phân loại rủi ro 5 luồng tính năng quan trọng nhất

| STT | Luồng tính năng (Feature Flow) | Mức độ rủi ro | Lý do (Business Impact) |
| :--- | :--- | :--- | :--- |
| 1 | **Luồng thanh toán trực tuyến** | **Cao** | Liên quan trực tiếp đến dòng tiền. Lỗi trừ tiền trong tài khoản ngân hàng nhưng hệ thống báo chưa thanh toán sẽ gây sai lệch đối soát tài chính và khủng hoảng niềm tin nghiêm trọng với người dùng. |
| 2 | **Luồng đặt hàng (Checkout)** | **Cao** | Đây là luồng nghiệp vụ lõi (core business flow). Nếu đứt gãy luồng dữ liệu (không lưu được đơn, không bắn được thông báo cho người bán), công ty sẽ thất thoát doanh thu trực tiếp. |
| 3 | **Luồng đăng nhập/Đăng ký** | **Cao** | Là "cửa ngõ" của hệ thống. Nếu sập toàn bộ các cổng đăng nhập, người dùng không thể vào hệ thống để thực hiện bất kỳ giao dịch mua bán nào. |
| 4 | **Luồng hủy đơn hàng** | **Trung bình** | Không chặn dòng tiền đổ vào công ty, nhưng nếu bị lỗi (không hủy được) sẽ làm tăng chi phí vận hành (hàng hoàn về do khách từ chối nhận) và gây trải nghiệm tiêu cực. |
| 5 | **Luồng cập nhật hồ sơ (Đổi ảnh đại diện)** | **Thấp** | Việc không tải lên được ảnh đại diện hoặc lỗi hiển thị UI không cản trở luồng mua hàng, không ảnh hưởng đến doanh thu. |

---

## 2. Săn Requirement Mơ Hồ: 5 Câu hỏi cho PO đối với luồng Rủi ro Cao nhất (Thanh toán trực tuyến)

*Nếu được tham gia dự án, tôi sẽ chất vấn PO (Product Owner) những điểm rủi ro ẩn sau:*

1. **Rủi ro Timeout:** "Nếu Cổng thanh toán (như ZaloPay/Momo) bị nghẽn mạng không phản hồi về hệ thống, Shopee sẽ giữ đơn hàng ở trạng thái 'Đang chờ xử lý' trong thời gian tối đa (TTL) là bao lâu trước khi tự hủy?"
2. **Rủi ro Đồng bộ tiền:** "Nếu khách hàng thanh toán bị trừ tiền ở ngân hàng, nhưng rớt mạng ngay lúc Shopee nhận kết quả, luồng hoàn tiền (refund) sẽ tự động quét và kích hoạt hay bắt buộc người dùng phải tự gọi lên tổng đài khiếu nại?"
3. **Rủi ro Phân quyền:** "Với thẻ tín dụng đã liên kết sẵn, hệ thống có yêu cầu nhập lại mã OTP/Mật khẩu khi giá trị đơn hàng vượt quá một ngưỡng tiền cụ thể nào đó không, hay cho phép thanh toán thẳng 1 chạm?"
4. **Rủi ro Spam:** "Có giới hạn số lần bấm nút 'Thử thanh toán lại' cho cùng một đơn hàng thất bại để chống spam API lên hệ thống ngân hàng đối tác không?"
5. **Rủi ro Thanh toán kết hợp:** "Khi thanh toán kết hợp (Ví dụ: Trừ 50k Số dư ShopeePay + 150k Thẻ tín dụng), nếu thẻ tín dụng bị từ chối, thì số tiền 50k trong ShopeePay sẽ bị kẹt (hold) lại hay được hoàn trả ngay lập tức?"

---

## 3. Chiến lược kiểm thử giới hạn (Nếu chỉ có 1 ngày để test)

Nếu chỉ có 1 ngày duy nhất để test ứng dụng này, tôi sẽ ưu tiên test theo **Luồng xuyên suốt (End-to-End Flow)**: từ lúc *Tìm/Chọn sản phẩm -> Thêm giỏ hàng -> Đặt hàng -> Thanh toán trực tuyến thành công*. Đây là xương sống sinh tồn tạo ra doanh thu. 

Sau khi luồng Happy Path (luồng đi suôn sẻ) này hoạt động toàn vẹn, tôi sẽ dồn thời gian còn lại vào các nhánh rẽ dễ gây thất thoát tiền nhất (ví dụ: thanh toán nửa chừng bị lỗi, áp dụng sai mã giảm giá). Toàn bộ các luồng giao diện (UI), trang cá nhân, đổi ảnh đại diện hay tính năng review sản phẩm sẽ bị đánh dấu là **Out-of-scope (Nằm ngoài phạm vi test của đợt này)** để dồn toàn lực bảo vệ dòng tiền.