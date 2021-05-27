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

**Step 2: Cấu hiinhf kolla**  

1. Hãy sao chép cấu hình mẫu sau  
```cp -r /usr/local/share/kolla-ansible/etc_examples/kolla /etc/kolla/
cp /usr/local/share/kolla-ansible/ansible/inventory/all-in-one .
```  
2. Cấu hình mạng  
```nano /etc/kolla/globals.yml```  
Trong globals.yml gõ  
```kolla_internal_vip_address: "192.168.0.109"
network_interface: "enp0s9"
neutron_external_interface: "enp0s8"
designate_backend: "bind9"
designate_ns_record: "sample.openstack.org"
tempest_image_id:
tempest_flavor_ref_id:
tempest_public_network_id:
tempest_floating_network_name:  
```  
Sau đó tạo tất cả mật khẩu để triển khai  
```kolla-genpwd```  
3. Khởi động môi trường  
```kolla-ansible -i all-in-one bootstrap-servers```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/119752153-a7072f00-bec6-11eb-82ca-01579ba8c4cd.png)
Để có thể lấy tất cả các hình ảnh mà chúng ta cần cho các vùng chứa có thể kiểm soát được  
```kolla-ansible pull```  
Ban đầu, chúng ta sẽ gặp lỗi full  
![image](https://user-images.githubusercontent.com/46991949/119752269-e5045300-bec6-11eb-9b43-6dc6bcc2482a.png)
Sau đó chúng ta sẽ phải cập nhật cấu hình để bao gồm một phiên bản cụ thể của thẻ ( 4.0.0 )  
```

