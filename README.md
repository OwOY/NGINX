<img src="https://www.nginx.com/wp-content/uploads/2018/08/NGINX-logo-rgb-large.png">  

<detail></detail>

<details>
<summary style="font-size:30px">Introduction</summary>
  <ol style='font-size:20px'>
    <li><a href="#how-to-download">How To Download</a></li>
    <li><a href="#nginx-config-路徑">Nginx Config 路徑</a></li>
    <li><a href="#nginx-設定">Nginx 設定</a></li>
    <li><a href="#憑證設定">憑證設定</a></li>
    <li><a href="nginx.conf">Sample</a></li>
</details>

<br>
<br>

# How To Download
- Linux
    ```
    sudo apt -y install nginx
    ```
- Docker
    ```
    docker pull nginx
    ```

# Nginx Config 路徑 
- Nginx Default Path  
(一般不會直接修改此檔案，而是在conf.d新增檔案)
    ```
    /etc/nginx/nginx.conf
    ```  
- Nginx Bonus Path
    ```
    /etc/nginx/conf.d/*.conf
    ```

# Nginx 設定
- docker-compose 設定
    ```docker
    version: '3'

    server:
        image: nginx
        container_name: server
        restart: always
        ports:
            - 80:80
            - 443:443
        volumes:
            - ./nginx/nginx.conf:/etc/nginx/conf.d/nginx.conf
            - ./ssl:/etc/nginx/ssl
        depends_on:
            - frontend
            - app
        networks:
            - test_network

    networks:
        test_network:
            driver: bridge
    ```
- 反向代理設定
    ```nginx
    server {
        listen 80;
        server_name _;
        location /proxy1 {
            proxy_pass http://localhost:8000;
        }
        location /proxy2 {
            proxy_pass http://localhost:8001;
        }
    }
    ```
- 負載平衡設定
    ```nginx
    upstream backend {
        server localhost:8000;
        server localhost:8001;
    }
    server {
        listen 80;
        server_name _;
        location / {
            proxy_pass http://backend;
        }
    }
    ```
- Route Rewrite
    ```nginx
    server {
        listen 80;
        server_name _;
        location /api/ {
            rewrite ^/api/(.*)$ /$1 break;
        }
    }
    ```
- 緩衝設定
```nginx
    server {
        listen 80;
        server_name _;
        location /api/ {
            rewrite ^/api/(.*)$ /$1 break;
            
            proxy_buffering off：禁用緩衝，實時將數據發送給客戶端。
            proxy_cache off：禁用緩存，確保每次請求都從後端獲取最新的數據。
            proxy_no_cache 1：禁用代理緩存，確保每次請求都不會受到緩存影響。
            proxy_max_temp_file_size 0：禁用寫入臨時文件，確保數據即時傳送。
            send_timeout 3600s：設定長時間的超時限制，防止 SSE 連接被提前關閉。
        }
    }

```


# 憑證設定
```
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout ssl.key -out ssl.crt 
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
