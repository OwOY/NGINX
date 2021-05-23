<img src="https://www.nginx.com/wp-content/uploads/2018/08/NGINX-logo-rgb-large.png">  

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
## Nginx設定  
```
# etc/nginx/nginx.conf
server {
        listen       80;
        server_name  34.145.108.85;
        root         /home/asd1234op/code/temp/4D;   #flask或是網頁位置

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
                   proxy_pass http://0.0.0.0:8089;
                   proxy_set_header Host $host;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
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
