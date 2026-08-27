\# Báo cáo Kiểm thử: Đối soát luồng Đặt hàng (Checkout)



\## 1. Bối cảnh

Thực hiện kiểm thử luồng thanh toán (Checkout) trên hệ thống E-commerce nội bộ.

\*\*Mục tiêu:\*\* Xác minh tính nhất quán của dữ liệu từ Giao diện (Frontend) -> Giao tiếp API (Network) -> Lưu trữ Cơ sở dữ liệu (Backend).



\## 2. Bằng chứng API (DevTools)

Sau khi khắc phục cấu hình Payment Method, tiến hành đặt hàng lại.

\* \*\*Endpoint:\*\* `POST /?wc-ajax=checkout`

\* \*\*Status Code:\*\* `200 OK`

\* \*\*Payload truyền lên:\*\* 
<img width="1580" height="784" alt="image" src="https://github.com/user-attachments/assets/e79d9588-a433-4048-9c3a-7a2114462cfe" />



\* \*\*Response trả về:\*\* `{"result":"success","redirect":"..."}`

\* \*\*Lệnh cURL tái tạo lỗi cho Dev:\*\*

curl 'http://localhost:10004/?wc-ajax=add_to_cart' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Cookie: wordpress_test_cookie=WP%20Cookie%20check; wordpress_logged_in_a024acb662ffd2f30d002a94ed1ea95c=khue%7C1788022656%7CG37PeN0NTGigOxn8pKeeV0OdKA9c2vnDPs1sGQGLOEi%7C928d071d78e9676e97b99650fa7d5de1014a89f1bde3ac78302e92e278a34e73; tk_ai=492SUSxVqMqRV6OaZKMkjNv3; sbjs_migrations=1418474375998%3D1; sbjs_current_add=fd%3D2026-08-27%2017%3A28%3A51%7C%7C%7Cep%3Dhttp%3A%2F%2Flocalhost%3A10004%2Fpost.php%3Fpost%3D15%26action%3Dedit%7C%7C%7Crf%3D%28none%29; sbjs_first_add=fd%3D2026-08-27%2017%3A28%3A51%7C%7C%7Cep%3Dhttp%3A%2F%2Flocalhost%3A10004%2Fpost.php%3Fpost%3D15%26action%3Dedit%7C%7C%7Crf%3D%28none%29; sbjs_current=typ%3Dtypein%7C%7C%7Csrc%3D%28direct%29%7C%7C%7Cmdm%3D%28none%29%7C%7C%7Ccmp%3D%28none%29%7C%7C%7Ccnt%3D%28none%29%7C%7C%7Ctrm%3D%28none%29%7C%7C%7Cid%3D%28none%29%7C%7C%7Cplt%3D%28none%29%7C%7C%7Cfmt%3D%28none%29%7C%7C%7Ctct%3D%28none%29; sbjs_first=typ%3Dtypein%7C%7C%7Csrc%3D%28direct%29%7C%7C%7Cmdm%3D%28none%29%7C%7C%7Ccmp%3D%28none%29%7C%7C%7Ccnt%3D%28none%29%7C%7C%7Ctrm%3D%28none%29%7C%7C%7Cid%3D%28none%29%7C%7C%7Cplt%3D%28none%29%7C%7C%7Cfmt%3D%28none%29%7C%7C%7Ctct%3D%28none%29; sbjs_udata=vst%3D1%7C%7C%7Cuip%3D%28none%29%7C%7C%7Cuag%3DMozilla%2F5.0%20%28Windows%20NT%2010.0%3B%20Win64%3B%20x64%29%20AppleWebKit%2F537.36%20%28KHTML%2C%20like%20Gecko%29%20Chrome%2F122.0.6261.95%20Safari%2F537.36; wp_woocommerce_session_a024acb662ffd2f30d002a94ed1ea95c=1%7C1788456570%7C1787938170%7C80e0df39fa9573c9f170abc7e37886a9; wp-settings-time-1=1787852659; tk_qs=; sbjs_session=pgs%3D12%7C%7C%7Ccpg%3Dhttp%3A%2F%2Flocalhost%3A10004%2Fcart%2F' \
  -H 'Origin: http://localhost:10004' \
  -H 'Referer: http://localhost:10004/cart/' \
  -H 'Sec-Fetch-Dest: empty' \
  -H 'Sec-Fetch-Mode: cors' \
  -H 'Sec-Fetch-Site: same-origin' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.6261.95 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'sec-ch-ua: "Chromium";v="122", "Not(A:Brand";v="24", "Google Chrome";v="122"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Windows"' \
  --data-raw 'price=100000&product_sku=&product_id=15&quantity=1'



\## 3. Đối soát Cơ sở dữ liệu (SQL)

Sử dụng truy vấn để kiểm tra dữ liệu thực tế lưu ngầm:



```sql

SELECT id, status, date\_created\_gmt

FROM wp\_wc\_orders

ORDER BY date\_created\_gmt DESC

LIMIT 1;

