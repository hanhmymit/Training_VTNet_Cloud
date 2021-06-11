## Practice Jenkins CI example and Write CD Pipeline  

## 1. Máy chủ Web server    
**1. Tổng quan**  
* Web server là một máy tính lưu trữ những nội dung web.*  
* Một máy chủ web dùng để phục vụ các trang web trên mạng internet hoặc mạng nội bộ.*  
* Nó lưu trữ các pages, scripts, progarms hay các files multimedia và sử dụng giao thức HTTP để gửi các tệp đến trình duyệt web.*  

**2. Cách thức hoạt động**  
![image](https://user-images.githubusercontent.com/46991949/121111392-fb9c9980-c838-11eb-8832-6daa20d082f1.png)
* Ví dụ người dùng muốn xem một trang web như www.google.com, người dụng nhập url vào trình duyệt web với điều kiện người dùng cần kết nối Internet. Khi đó bộ giao thức TCP/IP được sử dụng để thiết lập kết nối.*  
* Khi kết nối được thiết lập, máy khách sẽ gửi một yêu cầu thông qua HTTP và chờ phản hồi từ máy chủ. Phía bên kia máy chủ nhận được yêu cầu, xử lý yêu cầu, gửi lại phản hồi cho máy khách.*  

## 2. CI/CI  
**CI/CD là gì**  
CI/CD là một bộ đôi công việc, bao gồm CI (Continuous Integration) và CD (Continuous Delivery), ý nói là quá trình tích hợp (integration) thường xuyên, nhanh chóng hơn khi code cũng như thường xuyên cập nhật phiên bản mới (delivery).  

**Quy trình CI/CD tham khảo**  
![image](https://user-images.githubusercontent.com/46991949/121673654-50f7d580-cadb-11eb-92c5-fae1a63f3f06.png)

Dưới đây là các bước thông thường của quá trình release tính năng trong một dự án    
* Bước 1: Khởi tạo repository và có branch default là master và dev    
* Bước 2: Trừ owner ra, thì các coder sẽ push code tính năng lên branch dev    
* Bước 3: [Auto] Hệ thống tự động thực hiện test source code, nếu PASS thì sẽ deploy tự động (rsync) code lên server beta.  
* Bước 4: Tester/QA sẽ vào hệ thống beta để làm UAT (User Acceptance Testing) và confirm là mọi thứ OK.  
* Bước 5: Coder hoặc owner sẽ vào tạo Merge Request, và merge từ branch dev sang branch master.  
* Bước 6: Owner sẽ accept merge request.  
* Bước 7: [Auto] Hệ thống sẽ tự động thực hiện test source code, nếu PASS sẽ enable tính năng cho phép deploy lên production server.  
* Bước 8: Owner review là merge request OK, test OK. Tiến hành nhấn nút để deploy các thay đổi lên môi trường production.  
* Bước 9: Tester/QA sẽ vào hệ thống production để làm UAT và confirm mọi thứ OK. Nếu không OK, Owner có thể nhấn nút Deploy phiên bản master trước đó để rollback hệ thống về trạng thái stable trước đó.      

## 3. Chuẩn bị  
**Cài đặt docker**  
**Cài đặt docker-Compose**  

## 4. Triển khai CI/CD với Jenkins  

**Step 1: 

