# Aws-deploy

# amazon lightsail 前三個月免費

# 指令-環境設定
sudo apt-get update

sudo apt-get install -y python3-pip

sudo apt-get install -y nginx

pip3 install -r requirements.txt   (若source code有該檔案的話拉)

sudo snap install core

sudo snap refresh core

sudo snap install --classic certbot

sudo ln -s /snap/bin/certbot /usr/bin/certbot

pip3 install uwsgi


# 指令-設定環境變數:

nano .bashrc

source .bashrc

e.g.  export 變數='xxxxx'

# 指令-nginx

cd /etc/nginx/sites-available/

cd /etc/nginx/sites-enabled/

nano {your_project_name}.conf

sudo ln -s /etc/nginx/sites-available/{your_project_name}.conf /etc/nginx/sites-enabled/{your_project_name}.conf

sudo nginx -s reload

ls -l

sudo certbot --nginx (certbot在網域上安裝憑證)

more /etc/nginx/sites-available/{your_project_name}.conf  (檢查憑證狀態)

sudo certbot delete --cert-name {your_domain_name} (移除憑證)


# 指令-uwsgi

source .profile #安裝後需要讓path生效~不然抓不到uwsgi

nano {your_project_name}.ini

uwsgi {your_project_name}.ini

tail -f {your_project_name}.log

ps -ef | grep uwsgi

kill {your_pid}



#注意事項

# 執行個體注意事項:

1. ssh金鑰 取private key -> 可使用MobaXterm操作

2. 防火牆沒勾選https時噴402

3. 靜態ip <-> godaddy 網域連結

4. uwsgi沒有開啟會噴502 bad gateway

5. app = Flask(__name__)  # 建立app

6. app.run()  # 建立web server. app.run(host="172.26.5.28", port=int(os.environ.get("PORT", 3000)))的port要對齊。

7. godaddy + lets encrypt(免費) 設定網域和SSL

8. nslookup 查詢domain name



<img width="243" alt="2022-01-06_15h03_46" src="https://user-images.githubusercontent.com/66947341/148342723-c2e39e86-f3d1-406c-b74b-38f44735178d.png">

<img width="300" alt="2022-01-06_15h04_17" src="https://user-images.githubusercontent.com/66947341/148342727-48ee0049-93d1-4eec-877a-5761c30ffa9c.png">

<img width="445" alt="2022-01-06_15h04_36" src="https://user-images.githubusercontent.com/66947341/148342728-8924054b-b16e-4c75-ac08-53a5d1dde843.png">



#待練習

組態檔

ssh金鑰

ssl概念

snapd套件管理




# --------01.06-------- 雙VM串接, 一台VM多個loaction

n1+n2串接

n1: nginx設定  nginx中location的ip 指向n2的內部ip

n2: uwsgi設定

server{
       server_name 網域;
       location / {
               include uwsgi_params;
               uwsgi_pass {aws另一台VM內部ip}:{5000};
       }
       location /子目錄 {
               include uwsgi_params;
               uwsgi_pass {127.0.0.1}:{5000}; #本台VM所以用本地ip
       }
       location /子目錄 {
                alias html檔資料夾路徑 e.g. /home/{帳戶名}/{projectname};
       }
       
}

<img width="433" alt="2022-01-07_10h45_37" src="https://user-images.githubusercontent.com/66947341/148483127-767085ee-4c8c-4d61-90ef-065efc76cf53.png">

