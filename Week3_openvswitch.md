# Use openvswitch to setup Vxlan network between 2 virtual machines  

*Note:  
Say suppose I have create internal network with subnet 192.168.1.4/24 as internal1 and other internal2 with subnet 192.168.1.4/24  
![image](https://user-images.githubusercontent.com/46991949/118795157-85a1b400-b8c4-11eb-85d1-f2d106ceb98f.png)  

**Step 1: Create 2 Linux virtual machines and install openvswitch**  
1. Create 2 Linux virtual machines 
* VM1: 192.168.1.227  
* VM2: 192.168.1.2

2. Install openvswitc on OVS  
* ```sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils```  
* ```sudo apt-get install openvswitch-switch```  
* ```libvirtd --version```  
![image](https://user-images.githubusercontent.com/46991949/118797900-3f018900-b8c7-11eb-8d3d-3033e6ffc320.png)



