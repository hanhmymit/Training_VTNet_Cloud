## Setup OpenStack AIO inside VM with Kolla ##  

*Môi trường*  
*1. Hệ điều hành: Ubuntu 18.04, Ram: 4G,SSD CPU:02*  
*Network*
* NAT: enps3 10.0.2.15
* Bridgr Adapter: enps8 : 192.168.0.110
* Host-only Adapter: enp0s9: 192.168.56.101

**Step 1: Cài đặt Kolla**  

1. Cài đặt PIP  
```apt-get update
sudo apt install python3-dev libffi-dev gcc libssl-dev  
```  
2. Cài đặt môi trường ảo
```sudo apt install python3-venv```
3. Tạo môi trường ảo và kích hoạt môi trường  
```python3 -m venv path/to/venv```    
```source path/to/venv/bin/activate```  
2. Cài đặt ansible  
Cài đặt pip phiên bản mới  
```pip install -U pip```  
```apt-get install ansible```  
3. Cài đặt docker  
```apt-get update -qq```    
```apt-get install -y -qq --no-install-recommends docker-ce```  
```curl -sSL https://get.docker.io | bash```  
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

**Step 1: Cài đặt các gói yêu cầu bắt buộc trên ubuntu 18.04  

1. Cập nhật và nâng cấp các gói  
```sudo apt update``` 
```sudo apt upgrade```  
2. Cài đặt gói yêu cầu  
```sudo apt install python3-dev python3-venv libffi-dev gcc libssl-dev git```  
3. Cài đặt môi trường ảo để triển khai Kolla-ansible. Để tránh xung đột giữa các gói hệ thống và các gói Kolla-ansible  
```python3 -m venv $ HOME / kolla-openstack```  
Kích hoạt môi trường  
```source $HOME/kolla-openstack/bin/activate```  
4. Nâng cấp pip  
```pip install -U pip```  

**Step 2: Cài đặt Ansible trên Ubuntu 18.04**  
1. cài đặt Ansible. Kolla yêu cầu ít nhất Ansible 2.8 trở lên đến 2.9  
```pip install 'ansible<2.10'```  
2. Tạo một tệp cấu hình có thể nghe được trên thư mục chính của bạn với các tùy chọn sau  
```vim $HOME/ansible.cfg```  
Nội dung của file ansible.cfg  
```[defaults]
host_key_checking=False
pipelining=True
forks=100
```  

**Step 3: Cài đặt Kolla-ansible trên Ubuntu 18.04**  

1. Cài đặt Kolla-ansible trên Ubuntu 18.04 bằng cách sử dụng pip từ môi trường ảo ở trên  
2. Tiến hành cài đặt  
```pip install kolla-ansible```  

**Step 4: Định cấu hình Kolla-ansible cho Triển khai OpenStack**  

1. Tạo thư mục cấu hình Kolla  
```sudo mkdir etc/kolla```  
2. Cấp quyền sở hữu thư mục cấu hình Kolla cho người dùng mà bạn đã kích hoạt môi trường ảo triển khai Koll-ansible  
```sudo chown $USER:$USER /etc/kolla```  
3. Sao chép tệp cấu hình Kolla chính globals.ymlvà tệp mật khẩu dịch vụ OpenStack passwords.ymlvào thư mục cấu hình Kolla ở trên từ môi trường ảo  
```sudo cp $HOME/kolla-openstack/share/kolla-ansible/etc_examples/kolla/* /etc/kolla/```  
4. Cấu hình mạng  
```nano /etc/kolla/globals.yml``` 
Trong file globals.yml  
```config_strategy: "COPY_ALWAYS"
kolla_base_distro: "ubuntu"
kolla_install_type: "binary"
openstack_release: "ussuri"
kolla_internal_vip_address: "192.168.0.114"
kolla_internal_fqdn: "{{ kolla-openstack.kifarunix-demo.com }}"
kolla_external_vip_address: "{{ kolla_internal_vip_address }}"
kolla_external_fqdn: "{{ kolla_internal_fqdn }}"
network_interface: "enp0s10"
neutron_external_interface: "enp0s8"
neutron_plugin_agent: "openvswitch"
enable_haproxy: "yes"
enable_cinder: "yes"
enable_cinder_backend_lvm: "yes"
keystone_token_provider: 'fernet'
cinder_volume_group: "openstack_cinder"
nova_compute_virt_type: "qemu"
```  
5. Tạo mật khẩu Kolla  
passwords.yml Tệp cấu hình Kolla lưu trữ các mật khẩu dịch vụ OpenStack khác nhau. Bạn có thể tự động tạo mật khẩu bằng cách sử dụng Kolla-ansible kolla-genpwdtrong môi trường ảo của mình.  
```kolla-genpwd```  
Tất cả các mật khẩu đã tạo sẽ được điền vào /etc/kolla/passwords.yml  

**Step 5: Triển khai OpenStack tất cả trong một với Kolla-Ansible trên Ubuntu 18.04**  

1. Khởi động cấu hình localhost của bạn trước khi triển khai vùng chứa bằng bootstrap-serverslệnh con  
```kolla-ansible -i all-in-one bootstrap-servers```  

