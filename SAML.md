# SAML Server Project
Purpose of project was to help better understand user management, permissions, security, firewall configuration, scripting and systemd timers.
Will be adding on to this project later to use external authentication with LDAP

Distribution used: Ubuntu Server 24.
1. User Management
2. Security
3. Firewall Config
4. Backup/Cron Job

### Install SAMBA
sudo apt install samba -y

### Create Share Directory
sudo mkdir -p /srv/test_share/HR
sudo chmod -R 770 /srv/test_share

### Configure smb.conf \n
<img width="209" height="165" alt="image" src="https://github.com/user-attachments/assets/dd0170bc-c6d0-4496-a4d6-c987becb1a27" />

### Create Groups:
sudo groupadd HR  
sudo groupadd Engineering  
sudo groupadd IT  
sudo groupadd Logistics  

### Add HR user and add to HR group
sudo useradd -M -s /usr/sbin/nologin alice  
sudo usermod -aG HR alice

### Change group ownership to HR
sudo chown :HR HR

### Set SGID bit on the sub-directories so new files will owned by the group
<img width="767" height="159" alt="image" src="https://github.com/user-attachments/assets/64413529-3b2a-4ab5-9229-92a8e6dce507" />


### Set valid users in smb.conf file  
<img width="950" height="552" alt="image" src="https://github.com/user-attachments/assets/2c4a4822-863c-43b5-875d-97d9a60879bd" />

## Firewall Config
ufw doesn't come packaged with Ubuntu server, so I need to install it  
sudo apt install ufw -y 
sudo uf enable  
sudo ufw allow from 192.168.0.4 to any port 137,138,139,445 proto tcp 

<img width="1146" height="175" alt="image" src="https://github.com/user-attachments/assets/5a5dbba2-1391-4c74-8956-04f95c057a34" />

Testing this connection from another machine on my network and I wasn't able to connect using the alice account, confirming that the connection can only be made from .4  
<img width="637" height="232" alt="image" src="https://github.com/user-attachments/assets/6ad7d8cd-2147-4c11-8f63-6f857de81caa" />

## Encryption
Next, I wanted clients and the server to use max level encryption for security purposes  
<img width="782" height="163" alt="image" src="https://github.com/user-attachments/assets/38bfbfbb-0f5a-4fb5-af84-f8c3a09e9cf7" />

## Backup script/ Systemd Timer
created a backups folder at /backups  and a scripts folder at /scripts  
In scripts, created backups.sh with the following -  
<img width="966" height="260" alt="image" src="https://github.com/user-attachments/assets/856f4cdf-f1d8-4ca0-b3a5-91cc0ad63877" />

Testing the script, I get the compressed tarball at /backups  
<img width="484" height="42" alt="image" src="https://github.com/user-attachments/assets/e68dd823-a149-4351-ab77-082cdc9b7c22" />

Now, I needed to setup a systemd timer to execute the job everyday at 1 am

### Timer
Creating .service file in /etc/systemd/system  
<img width="1226" height="225" alt="image" src="https://github.com/user-attachments/assets/a08f3f46-15e5-4262-810c-fa9d6e768ed2" />

Next, a .timer file is needed  
<img width="864" height="275" alt="image" src="https://github.com/user-attachments/assets/d0ea429c-00c2-445a-b216-b5a8f04788ba" />

<ins>Now we need to enable the timer:  </ins>  
sudo systemctl daemon-reload  
sudo systemctl enable saml_backup.timer  
sudo systemctl start saml_backup.timer  

From this screenshot you can see that the saml_backup.timer is enabled
<img width="2224" height="292" alt="image" src="https://github.com/user-attachments/assets/e098ca29-715a-47ef-b666-b5ab5d542fd9" />

## This concludes the project for now. Next, I will be trying to setup a Windows environment that way I can use external authentication and groups
EOF 8/3/25 

