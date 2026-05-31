# Automated Approach For Security Reconnaissance And Obfuscated JavaScript Analysis

Dự án này tập trung khai thác một bề mặt tấn công (Attack Surface) thường bị bỏ qua: Các tệp tin JavaScript trên giao diện người dùng (Frontend). Hệ thống được thiết kế để tự động hóa quy trình trinh sát (Reconnaissance), thu thập các tệp .js ẩn/bị làm rối mã (obfuscated), và tiến hành phân tích tĩnh nhằm trích xuất các tài nguyên nhạy cảm (API Keys, Credentials, Endpoints nội bộ) trước khi bước vào giai đoạn khai thác chuyên sâu

Đối với đồ án này đã thực hiện được quy trình tự động hóa dò quét và đã có thể lấy ra được những file .js trên trang web mục tiêu. 

## Mô hình hiện tại

<img width="1392" height="362" alt="step1" src="https://github.com/user-attachments/assets/5fc2fd87-6806-445f-ac7d-503ab6a2f4b7" />

> Đây là quá trình thực hiện dò ra các subdomain từ domain mục tiêu ban đầu (dựa trên subfinder), sau khi đã có các subdomain đó ta tiến hành phân giải tên miền dựa vào CloudFlare có sẵn và hoàn toàn miễn phí để có thể khai thác các thông tin cụ thể hơn. 

<img width="975" height="835" alt="step2" src="https://github.com/user-attachments/assets/68550929-70cc-4782-ba98-178b6c74b1cb" />

> Đầu ra của DNS có thể được gắn nhãn đối với các thông tin trả về NXDOMAIN (status:3) hoặc CNAME trỏ ra ngoài thì ta sẽ đánh dấu nó có khả năng bị lỗi takeover và tiến hành kiểm tra bằng nuclei, các kết quả còn lại (type: A) qua lớp lọc httpx để lọc dựa trên status code (tiến hành loại bỏ những status code 301, 302, 503 - bước này giúp giảm và chắc lọc giúp tối ưu và tăng hiệu suất.)

<img width="1834" height="554" alt="step3" src="https://github.com/user-attachments/assets/67f8a0ae-a235-4d91-a6bc-0b359108ae8f" />

> Sau giai đoạn lọc bằng httpx thì ta sẽ gồm 2 nhánh rẽ để xử lí, nhánh phía sẽ tiến hành xử lý đối với các status code 404 đi qua tool nuclei để phân tích các dạng takeover. Nhánh thứ 2 sẽ lấy các trang web có status code 200, 300 để thực hiện cào và lấy các file .js thông qua katana. Đối với node katana đầu tiên ta thực hiện cào cơ bản đối với những trang web nhẹ và ít phức tạp. 

<img width="965" height="448" alt="step4" src="https://github.com/user-attachments/assets/63864bca-b3fe-47b7-996d-46bf01a92919" />

> Có thể thấy được đã có 1 số file .js xuất hiện từ các subdomain đầu vào. Tiếp tới là phần xử lí đối với những file .js đã được quét ra thì sẽ được tới bước làm đầu vào của jsluice, các trang có quy mô lớn, phức tạp, có nhiều liên kết trang web con như bài toán kể tới ở đây là trang "kenh14.vn" thì ta cần xử lí gọn lại bằng việc viết thuật toán để lấy ra những trang web đại diện cho mỗi cấu trúc, vì chắc chắn kenh14.vn với số lượng page nhiều như vậy thì việc tái sử dụng template là điều không thể tránh khỏi. Ở đây ta đã thực hiện thuật toán chỉ lấy ra những mẫu của từng dạng cấu trúc.

<img width="864" height="833" alt="step5" src="https://github.com/user-attachments/assets/a932aa75-4c66-4d81-8d0d-1441303bea06" />

> Và như thế là ta đã có thể giải quyết được bài toán lo sợ về việc quét thiếu trường hợp mà dính vào bẫy tài nguyên (phải quét toàn bộ lượng lớn trang web của kenh14.vn), điều này vừa giúp tăng thời gian quét, giảm tài nguyên hao phí cho những web có cùng cấu trúc.

<img width="962" height="312" alt="step6" src="https://github.com/user-attachments/assets/a584ac2b-e3bf-4aa2-b841-7015800872aa" />

> Dựa vào đó ta đã kiểm thêm các file .js mà trước đó quét cơ bản không thể thấy được.

<img width="1209" height="454" alt="step7" src="https://github.com/user-attachments/assets/171809e0-a6e9-4f4e-86e2-f9e691f00d37" />

> Tiếp đó ta sẽ cho qua tools jsluice để thực hiện lấy ra những thông tin quan trọng ẩn nấu trong file .js 

Và đó là những gì hiện tại em đã làm được và sẽ tiếp tục phát triển đồ án này trong thời gian sắp tới. Phía trên chỉ là mục đích ví dụ, minh họa không có ý định khai thác, tấn công trái phép.
