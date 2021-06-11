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
# Continuous Integration for basic web project with jenkins

## Step by step

### basic web:
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/basicweb.png)

1. build jenkins with docker

```bash
$ docker run 
-v /usr/bin/docker:/usr/bin/docker 
-v /home/jenkins/jenkins_data:/var/jenkins_home  
-p 8080:8080  --user 1001:998 --name jenkins-server 
-d jenkins/jenkins:lts
```

![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/startjenkins.png)

2. public port 8080 using ngrok:

```
$ ngrok http 8080
```
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/ngrok.png)
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/ngrok2.png)

3. add jenkinsfile to github main branch:

````jenkinsfile
pipeline {
  agent none
  stages {
    stage("Test") {
      agent {
          docker {
            image 'python:3.8-slim-buster'
            args '-u 0:0'
          }
      }
      steps {
        sh "pip install poetry"
        sh "poetry install"
        sh "poetry run pytest"
      }
    }
  post {
    success {
      echo "SUCCESSFUL"
    }
    failure {
      echo "FAILED"
    }
  }
}
````

4. add webhook from github with Payload url == "https://4c6ad187ad9b.ngrok.io/github-webhook/"
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/webhooks.png)

5. create pipeline from jenkins
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/1.png)
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/2.png)

6. build
![](https://raw.githubusercontent.com/toanduc0671/NguyenDucToan-week5/main/images/build-fail.png)
