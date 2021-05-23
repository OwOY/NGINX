# NGINX
- 下載Nginx  
```
sudo yum -y install nginx
```

- 檢視Nginx配置檔  
```
sudo nano /etc/nginx/nginx.conf
```
## 防火牆設定
- 若沒有firewalld 可用下列指令下載
```
yum install -y firewalld
```
- 防火牆狀態  
```
systemctl status firewalld
```
- 防火牆開啟&關閉  
```
systemctl start firewalld
systemctl stop firewalld
systemctl reload firewalld
```
- 防火牆開機時自啟動  
```
systemctl enable firewalld
```
- 開放port(http、https、8089)   
```
firewalld-cmd --permanent --zone=public --add-service=http
firewalld-cmd --permanent --zone=public --add-service=https
firewall-cmd --zone=public --add-port=8089/tcp --permanent
sudo firewall-cmd --reload
```
- 列出防火牆設定內容  
```
firewall-cmd --list-all
```

# 意外處理
### nginx 403
- 確認安全增強式linux狀態
```
/usr/sbin/sestatus
```
- 關閉安全增強式linux
1. 修改config檔案
```
nano /etc/selinux/config
```
2. 將狀態改為關閉
#SELINUX=enforcing 
SELINUX=disabled
