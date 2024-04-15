<img src="https://www.nginx.com/wp-content/uploads/2018/08/NGINX-logo-rgb-large.png">  

- 下載Nginx  
```
sudo yum -y install nginx
```
- 檢視Nginx配置檔  
```
sudo nano /etc/nginx/nginx.conf
```
- 開啟Nginx
```
systemctl enable nginx
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
firewall-cmd --zone=public --permanent --add-service=http
firewall-cmd --zone=public --permanent --add-service=https
firewall-cmd --zone=public --add-port=8089/tcp --permanent   #永久新增
```
- 重新載入firewall設定
```
sudo firewall-cmd --reload
```
- 移除port
```
firewall-cmd --zone=public --remove-service=http
firewall-cmd --zone=public --remove-service=http --permanent  #永久移除
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
```
#SELINUX=enforcing 
SELINUX=disabled
```
# 新增憑證
```
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout ssl.key -out ssl.csr 
```
- req  
使用 X.509 Certificate Signing Request（CSR） Management 產生憑證。
- x509  
建立自行簽署的憑證。
- nodes  
不要使用密碼保護，因為這個憑證是 NGINX 伺服器要使用的，如果設定密碼的話，會讓伺服器每次在啟動時書需要輸入密碼。
- days 365  
設定憑證的使用期限，單位是天，如果不想時常重新產生憑證，可以設長一點。
- newkey rsa:2048  
同時產生新的 RSA 2048 位元的金鑰。
- keyout  
設定金鑰儲存的位置。
- out  
設定憑證儲存的位置。
