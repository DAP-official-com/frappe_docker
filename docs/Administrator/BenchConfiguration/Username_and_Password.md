# Username and Password

Use the SSH to connect your Server and run the command sudo cat /credentials/password.txt to get the username and password of this deployment solution.
These accounts are required for ERPNext image installation and configuration

## ERPNext

- ERPNext administrator username: Administrator
- ERPNext administrator password: stored in the file of your server /credentials/password.txt

## MariaDB

- MariaDB administrator username: root
- MariaDB administrator password: stored in the file of your server /credentials/password.txt

## Linux

- Host Name: Internet IP or Public IP of your Instance
- Connect by: Online SSH on Cloud Console or SFTP/SSH tools on your local computer
- Password: It was set by yourself when created instance
- Username: Different Cloud Platform has differences

|Cloud Platform|Administrator Username|Other|
|---|---|---|
|Azure|It was set by yourself when created instance|[How to enable root access?](https://support.websoft9.com/docs/azure/server-login.html#connect-by-command)|
|AWS CentOS|centos|[How to enable root access?](https://support.websoft9.com/docs/aws/server-login.html#connect-by-command)|
|AWS AmaonLinux|ec2-user|[How to enable root access?](https://support.websoft9.com/docs/aws/server-login.html#connect-by-command)|
|AWS Ubuntu|ubuntu|[How to enable root access?](https://support.websoft9.com/docs/aws/server-login.html#connect-by-command)|
|Alibaba Cloud, HUAWEI CLOUD, Tencent Cloud|root||

## Operating System

Different cloud platform operating system accounts are different. Some cloud platforms can customize the user name when creating a server, and some have a fixed user name of root. Therefore, please check the corresponding content according to the cloud platform: ["Cloud Platform Guide"](https://support.websoft9.com/docs/faq/tech-instance.html#cloud-platform-guide)
