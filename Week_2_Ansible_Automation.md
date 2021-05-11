
# Practice 1: Using Ansible to deploy Wordpress (docker) on Ubuntu VM  

**Step 1: Setup Ansible on Controller machine**  

1. Update the APT package repository cache with the following command  
```sudo apt-add-repository ppa:ansible/ansible```  
```sudo apt update```  
2. install Ansible with the following command  
```sudo apt install ansible```  
3. Run the following command to check if ansible is working correctly  
```ansible --version```   
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117783891-29f47c80-b26d-11eb-8355-9c4abc0248fa.png)

4. Generating SSH Key
```ssh-keygen```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117784349-adae6900-b26d-11eb-90f4-06f2dca41519.png)

**Step 2: Configuring Ubuntu Hosts for Ansible Automation**

1. Update the APT package repository cache with the following command  
```sudo apt update```  
2. Install OpenSSH server with the following command  
```sudo apt install openssh-server -y```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117784792-1e558580-b26e-11eb-9c26-3ada9eb0b576.png)
3. Check if the sshd service is running with the following command  
```sudo systemctl status sshd```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117784969-4e9d2400-b26e-11eb-801b-50acd10ba7ad.png)
4. Configuring the Ansible Control Node  
* As root, add an administrator-level user for the control node. Use the adduser command  
```sudo adduser [username]```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117825259-61791e00-b299-11eb-9fe8-a7e262c4bede.png)
*Note: Username: user01, pass: 123456*  
* The new account is ready. Now, assign administrative access to the account. The following command assigns superuser privileges, allowing the account to use the sudo command  
```sudo usermod -aG sudo [username]```  
5. Configuring an Ansible Host  
* The easiest method of setting up an SSH public key is to copy it using the ssh-copy-id command  
```ssh-copy-id username@remote_host```  
*Output*  
![image](https://user-images.githubusercontent.com/46991949/117826714-933eb480-b29a-11eb-8b58-97261d4cbb0d.png)



