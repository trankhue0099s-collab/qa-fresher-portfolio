\# Báo cáo Kiểm thử: Đối soát luồng Đặt hàng (Checkout)



\## 1. Bối cảnh

Thực hiện kiểm thử luồng thanh toán (Checkout) trên hệ thống E-commerce nội bộ.

\*\*Mục tiêu:\*\* Xác minh tính nhất quán của dữ liệu từ Giao diện (Frontend) -> Giao tiếp API (Network) -> Lưu trữ Cơ sở dữ liệu (Backend).



\## 2. Bằng chứng API (DevTools)

Sau khi khắc phục cấu hình Payment Method, tiến hành đặt hàng lại.

\* \*\*Endpoint:\*\* `POST /?wc-ajax=checkout`

\* \*\*Status Code:\*\* `200 OK`

\* \*\*Payload truyền lên:\*\* (Gắn ảnh chụp Payload của bạn vào đây)



\* \*\*Response trả về:\*\* `{"result":"success","redirect":"..."}`

\* \*\*Lệnh cURL tái tạo lỗi cho Dev:\*\*

\[Dán đoạn mã cURL bạn đã copy lúc nãy vào đây]



\## 3. Đối soát Cơ sở dữ liệu (SQL)

Sử dụng truy vấn để kiểm tra dữ liệu thực tế lưu ngầm:



```sql

SELECT id, status, date\_created\_gmt

FROM wp\_wc\_orders

ORDER BY date\_created\_gmt DESC

LIMIT 1;

