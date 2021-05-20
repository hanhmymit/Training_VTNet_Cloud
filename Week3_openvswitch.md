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

**Step 1: Create 2 Linux virtual machines and install openvswitch**  
1. Create 2 Linux virtual machines 
* VM1: 192.168.1.227  
* VM2: 192.168.1.2

2. Install openvswitc on OVS  
* ```sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils```  
* ```sudo apt-get install openvswitch-switch```  
* ```libvirtd --version```  
![image](https://user-images.githubusercontent.com/46991949/118797900-3f018900-b8c7-11eb-8d3d-3033e6ffc320.png)



