# REACT INSTRACTION

---

Centos normally come with nodejs, to check just type command

# dnf module list nodejs

You can enable version you like, example version 12

# dnf module enable nodejs:12

Now, if you install, enabled version will be one to be installed

# dnf install nodejs

# REACT DEPLOYMENT ON PRODUCTION

---

Create your named service, eg: react.service

# nano lib/systemd/system/react.service

Add this note below, to you file
############ note to add ###############

[Unit]
Description=Sample React Deployment Test
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/Documents/personal_project/test1
#this comment ExecStart line below produce core-dump error
#ExecStart=/usr/bin/npm start --prefix /root/Documents/personal_project/test1
ExecStart=/usr/bin/node node_modules/react-scripts/scripts/start.js
Environment=CI=true
StandardOutput=inherit
StandardError=inherit
Restart=on-failure

[Install]
WantedBy=multi-user.target

########################################

Reaload system service

# systemctl daemon-reload

Start your created service ( react ) and enable it

# systemctl start react

# systemctl enable react

Check status of your service

# systemctl status react

Check running module on your react port (3000)

# ss -antpl | grep 3000

# REVERSE PROXY FOR REACT APP WITH NGINX

---

Installing the nginx

# dnf install nginx -y

Create configuration file eg: react.conf in nginx conf.d

# nano /etc/nginx/conf.d/react.config

Add this note below, to created file
############ note to add ###############

server {
listen 80;
#this should be registered domain
server_name react.test.com;

    location / {
        proxy_set_header    X-Forwarded-For $remote_addr;
        proxy_set_header    Host $http_host;
        proxy_pass          http://localhost:3000;
    }

}

########################################

Test your nginx for errors

# nginx -t

Restart your nginx service

# systemctl restart nginx

Enable your port through firewall

# firewall-cmd --permanent --add-port=80/tcp

# firewall-cmd --permanent --add-port=443/tcp

# firewall-cmd --reload

# NOTE

---

Your nginx may sometimes fails to proxy, if so please check your selinux status

# getenforce

If enforced, that is the cause! run this command

# setsebool -P httpd_can_network_connect true

# systemctl restart nginx

Waou, Visit your address you will react

# SECURE YOUR REACT WITH let’s encrypt FREE SSL

---

You may probably endup want ssl to protect your domain, let start by install let’s encrypt

# dnf install epel-release

# dnf install certbot python3-certbot-nginx mod_ssl

Create the ssl for your domain (react.test.com)

# certbot --nginx -d react.test.com

# NOTE

---

1. You will be asked to provide your valid email address and accept the term of service
2. you will need to choose whether or not to redirect HTTP traffic to HTTPS
3. Type 2 and hit Enter to start the installation

## Enjoy your https, be secure!
