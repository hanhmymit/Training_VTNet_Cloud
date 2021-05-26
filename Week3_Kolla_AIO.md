## Setup OpenStack AIO inside VM with Kolla ##  

*Môi trường*  
*1. Hệ điều hành: Ubuntu 18.04, Ram: 4G,SSD CPU:02*  
*Có hai NIC trên VM ( enp0s8: 192.168.0.10 và enp0s9: 192.168.0.109*  

**Step 1: Cài đặt Kolla**  

1. Cài đặt PIP  
```apt-get update
apt-get install python-pip
pip install -U pip
apt-get install python-dev libffi-dev gcc libssl-dev  
```  
2. Cài đặt ansible  
```apt-get install ansible```  
3. Cài đặt docker  
```apt-get update -qq```    
```apt-get install -y -qq --no-install-recommends docker-ce```  
```curl -sSL https://get.docker.io | bash```
4. Tạo một cấu hình cho systemd cho Kolla và Docker  
```mkdir -p /etc/systemd/system/docker.service.d
tee /etc/systemd/system/docker.service.d/kolla.conf <<-'EOF'
[Service]
MountFlags=shared
EOF  
```  
5. Khởi động lại daemon để áp dụng  
```systemctl daemon-reload
systemctl restart docker
```  
6. Cài đặt docker-py  
```pip install -U docker-py```  
7. Cuối cùng, cài đặt kolla  
```pip install kolla-ansible```  

*Output*  
![image](https://user-images.githubusercontent.com/46991949/119678569-d471bf80-be69-11eb-9e9f-356f55b6d0be.png)

