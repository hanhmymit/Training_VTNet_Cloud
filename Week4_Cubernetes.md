## Install Kubernetes on PC by using mini-kube and specify Kubernetes components and Deploy Wordpress on Kubernetses ##  

*Giới thiệu*  
*Minikube là một công cụ mã nguồn mở cho phép bạn thiết lập một cụm Kubernetes một nút trên máy cục bộ của bạn. Cụm được chạy bên trong một máy ảo và bao gồm Docker, cho phép bạn chạy các vùng chứa bên trong nút.*  


## A: Install Kubernetes on PC by using mini-kube and specify Kubernetes components ##
**Step 1: Cập nhật hệ thống và cài đặt các gói yêu cầu**  
```sudo apt-get update -y```  
```sudo apt-get upgrade -y```  
Ngoài ra, hãy đảm bảo cài đặt (hoặc kiểm tra xem bạn đã có chưa) các gói bắt buộc sau:  
```sudo apt-get install curl```  
```sudo apt-get install apt-transport-https```  

**Step 2: Cài đặt VirtualBox Hypervisor**  

Sẽ cần một máy ảo trong đó bạn có thể thiết lập cụm nút đơn của mình với Minikube  
Để cài đặt VirtualBox trên Ubuntu , hãy chạy lệnh  
```sudo apt install virtualbox virtualbox-ext-pack```  

**Step 3: Cài đặt Minikube**  

1. Đầu tiên, tải xuống bản nhị phân Minikube mới nhất bằng lệnh wget  
```wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64```  
2. Sao chép tệp đã tải xuống và lưu trữ vào thư mục / usr / local / bin / minikube với  
```sudo cp minikube-linux-amd64 /usr/local/bin/minikube```  
3. Cấp quyền điều hành tệp bằng lệnh chmod  
```sudo chmod 755 /usr/local/bin/minikube```  
4. xác minh rằng bạn đã cài đặt thành công Minikube bằng cách kiểm tra phiên bản của phần mềm  
```minikube version```  
![image](https://user-images.githubusercontent.com/46991949/119974088-76b2b400-bfde-11eb-9dad-221041554ba2.png)

**Step 4:  Cài đặt Kubectl**  

1. Tải xuống kubectl bằng lệnh sau  
```curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl```  
2. Làm cho tệp nhị phân có thể thực thi bằng cách nhập  
```chmod +x ./kubectl```  
3. Sau đó, di chuyển nhị phân vào đường dẫn của bạn bằng lệnh  
```sudo mv ./kubectl /usr/local/bin/kubectl```  
4. Xác minh cài đặt bằng cách kiểm tra phiên bản cá thể kubectl của bạn  
```kubectl version -o json```  
![image](https://user-images.githubusercontent.com/46991949/119974434-d741f100-bfde-11eb-8d50-ff138cf4826b.png)  

**Step 5: Khởi động Minikube**  

```minikube start```   
*Note:Nếu gặp lỗi như sau:  
![image](https://user-images.githubusercontent.com/46991949/119990283-5856b380-bff2-11eb-99ad-a88c663c1afc.png)  
*Thì có thế fix bằng cách bỏ qua kiểm tra CPU (kể từ v1.5.2) bằng cách sử dụng      
```minikube start --extra-config=kubeadm.ignore-preflight-errors=NumCPU --force --cpus 1
```   
## B: Deploy a WordPress blog on Minikube with persistent data ##  

Tạo thư mục /wordpress-minikube
**Step 1: Tạo mật khẩu bí mật Kubernetes**  

1. Tạo một bản trình bày base64 cho mật khẩu của bạn. Lệnh dưới đây sẽ làm điều đó cho bạn  
```echo -n '1f2d1e2e67df' | base64```  

Lưu ý rằng đó 1f2d1e2e67dflà mật khẩu của bạn. Hãy thoải mái sử dụng bất kỳ mật khẩu nào bạn chọn. Đầu ra của lệnh trên sẽ là mật khẩu được mã hóa base64 của bạn . Sao chép nó.
Bây giờ, hãy tạo một tệp secrets.ymlvà dán mật khẩu được mã hóa base64 của bạn vào dòng cuối cùng.  
  ```apiVersion: v1
  kind: Secret
  metadata:
    name: mysql-pass
  type: Opaque
  data:
    password:
```  
Thực hiện lệnh này để tạo bí mật  
```kubectl apply -f secret.yml```  


