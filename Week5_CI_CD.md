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

## 1. Practice Jenkins CI  
**Step 1: Viết một máy chủ Web đơn giản bằng một ngôn ngữ Python**  
1. Python có một số thư viện được tích hợp để tạo một web server dễ dàng hơn. Ví dụ bạn có thể tạo một SimpleHTTPServer với một câu lệnh đơn giản  
```python3 -m http.server + port(nếu không viết gì mặc định là 8000)```  
2. 
