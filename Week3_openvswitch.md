# Use openvswitch to setup Vxlan network between 2 virtual machines  

**Giới thiệu chung về VXLAN**  

* Virtual Extensible LAN (VXLAN) là giao thức tunneling, thuộc giữa lớp 2 và lớp 3.

* VXLAN là giao thức sử dụng UDP (cổng 4789) để truyền thông và một segment ID độ dài 24 bit còn gọi là VXLAN network identifier (VNID). Chỉ các máy ảo trong cùng VXLAN segment mới có thể giao tiếp với nhau..

* VXLAN ID (VXLAN Network Identifier hoặc VNI) là 1 chuỗi 24-bits so với 12 bits của của VLAN ID. Do đó cung cấp hơn 16 triệu ID duy nhất ( giá trị này của VLAN: 4096 ).

VXLAN frame format:

![image](https://user-images.githubusercontent.com/46991949/118907515-d8717f00-b949-11eb-9eb6-afcfbca57b33.png)

* Frame Ethernet thông thường bao gồm địa chỉ MAC nguồn, MAC đích, Ethernet type và thêm phần VLAN_ID (802.1q) nếu có. Đây là frame được đóng gói sử dụng VXLAN, thêm các header sau:

* VXLAN header: 8 byte bao gồm các trường quan trọng sau:

    Flags: 8 but, trong đó bit thứ 5 (I flag) được thiết lập là 1 để chỉ ra rằng đó là một frame có VNI có giá trị. 7 bit còn lại dùng dữ trữ được thiết lập là 0 hết.

    VNI: 24 bit cung cấp định danh duy nhất cho VXLAN segment. Các VM trong các VXLAN khác nhau không thể giao tiếp với nhau. 24 bit VNI cung cấp lên tới hơn 16 triệu VXLAN     segment trong một vùng quản trị mạng.

    Outer UDP Header: port nguồn của Outer UDP được gán tự động và sinh ra bởi VTEP và port đích thông thường được sử dụng là port 4789 hay được sử dụng (có thể chọn port khác).

    Outer IP Header: Cung cấp địa chỉ IP nguồn của VTEP nguồn kết nối với VM bên trong. Địa chỉ IP outer đích là địa chỉ IP của VTEP nhận frame.

    Outer Ethernet Header: cung cấp địa chỉ MAC nguồn của VTEP có khung frame ban đầu. Địa chỉ MAC đích là địa chỉ của hop tiếp theo được định tuyến bởi VTEP.  
    
**Chuẩn bị**  
1. Topology:  
* Host 1: 192.168.1.2  
    vm1(cirros0): 10.0.0.101/24  
    vswitch br0: 10.0.0.1  
    vswitch br1: 192.168.1.2    
    
* Host 2: 192.168.1.227  
    vm2(cirros1): 10.0.0.102/24  
    vswitch br0: 10.0.0.2  
    vswitch br1: 192.168.1.227    
![image](https://user-images.githubusercontent.com/46991949/118908940-7b2afd00-b94c-11eb-925b-6c9965664dde.png)

2. Mô tả:
* Dưới đây mình sẽ thực hiện 1 bài lab sử dụng vmware để chạy 2 máy ảo host1, host2 đóng vai trò như các node vật lí trong thực tế.
* Trên 2 host này, sẽ được cài hệ điều hành Ubuntu Destop 18.04, cài sẵn các phần mềm Open vSwitch, KVM với QEMU, libvirt-bin để tạo các vm. 2 host này đều sủ dụng card mạng ens33 ( coi như là card mạng vật lý).
* Dùng wireshark để bắt và phân tích gói tin VXLAN

3. Cấu hình:
* Tạo 2 vSwitch br0 và br1 trên cả 2 host.
* Cấu hình chế độ mạng bridge cho vSwitch br1 và card mạng ens33 trên cả 2 host.
* Trên HOST 1, tạo VM1(cirros1) kết nối với vSwitch br0. Trên HOST 2 tạo VM2(cirros2) kết nối với vSwitch br0.

**Step 1: Create 2 Linux virtual machines and install openvswitch**  

1. Open vSwitch và KVM trên cả 2 Host  
* ```sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils```  
* ```sudo apt-get install openvswitch-switch```  
* ```libvirtd --version```  
![image](https://user-images.githubusercontent.com/46991949/118910290-6c454a00-b94e-11eb-9f08-d89426584f46.png)  

2. Tạo 2 vswitch br0 và br1 trên cả 2 Host  
```sudo ovs-vsctl add-br br0```  
```sudo ovs-vsctl add-br br1```  

3. Bật 2 vswitch trên cả 2 Host  
```sudo ip link set dev br0 up```  
```sudo ip link set dev br1 up```  
![image](https://user-images.githubusercontent.com/46991949/118910589-ef66a000-b94e-11eb-870e-ed037da975c7.png)

4. Trên host1 tạo chế độ mạng bridge cho vswitch br1 và card mạng enp0s3 
![image](https://user-images.githubusercontent.com/46991949/118913933-9568d900-b954-11eb-80cd-700fd9276ba9.png)
 
```sudo ovs-vsctl add-port br1 enp0s3```  
```sudo ifconfig enp0s3 0 && ifconfig br1 192.168.1.2/24```

5. Trên host2 tạo chế độ mạng bridge cho vswitch br1 và card mạng enp0s3
```sudo ovs-vsctl add-port br1 enp0s3```   
```sudo ifconfig enp0s3 0 && ifconfig br1 192.168.1.227/24```  

6. Add lại route trên cả 2 host  
```sudo route add default gw 192.168.1.1 br1```  

7. Cấu hình IP cho br0 trên Host
* Trên host1
```sudo ip a add 10.0.0.1/24 dev br0```
* Trên host2
```sudo ip a add 10.0.0.2/24 dev br0```  

8. Cấu hình VXLAN tunnel cho vswitch br0 trên host
* Trên host1
```sudo ovs-vsctl add-port br0 vxlan1 -- set interface vxlan1 type=vxlan option:remote_ip=192.168.1.2```  
* Trên host2  
```sudo ovs-vsctl add-port br0 vxlan1 -- set interface vxlan1 type=vxlan option:remote_ip=192.168.1.227```  
![image](https://user-images.githubusercontent.com/46991949/118945988-2e611980-b980-11eb-869a-2e5e6d67962a.png)

9. Tạo mạng ovs0 với vswitch ovs1 trên cả 2 host   
* nano ovs0.xml  
```<network>
 	<name>ovs0</name>
 	<forward mode='bridge'/>
 	<bridge name='br0'/>
 	<virtualport type='openvswitch'/>
 </network>
 ```  
* ```sudo virsh net-define ovs0.xml```    
![image](https://user-images.githubusercontent.com/46991949/118946656-cced7a80-b980-11eb-8cb8-9569a056d050.png)
*```virsh net-start ovs0```  
* ```virsh net-autostart ovs0```  
![image](https://user-images.githubusercontent.com/46991949/118946882-0625ea80-b981-11eb-9df2-5af325738d60.png)  

10. Create Virtual machines testvm and attach network ovs0 vừa tạo ở trên  
* Trên host1  
```sudo virt-install --name=testvm1 --ram=512 --vcpus=1 --cdrom=/root/ubuntu-16.04.2-server-amd64.iso --os-type=linux --os-variant=ubuntu16.04 --network network:ovs0 --disk path=/var/lib/libvirt/images/testvm2.dsk,size=8```  


 


