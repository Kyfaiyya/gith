
Konfigurasi Awal

**Paradis (Router/DHCP Relay)**
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.234.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.234.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.234.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.234.4.1
	netmask 255.255.255.0

**Tybur (DHCP Server)**
auto eth0
iface eth0 inet static
	address 192.234.4.2
	netmask 255.255.255.0
	gateway 192.234.4.1

**Fritz (DNS Server)**
auto eth0
iface eth0 inet static
	address 192.234.4.3
	netmask 255.255.255.0
	gateway 192.234.4.1

**Warhammer (Database Server)**
auto eth0
iface eth0 inet static
	address 192.234.3.2
	netmask 255.255.255.0
	gateway 192.234.3.1

**Beast (Load Balancer Laravel)**
auto eth0
iface eth0 inet static
	address 192.234.3.3
	netmask 255.255.255.0
	gateway 192.234.3.1

**Colossal (Load Balancer PHP)**
auto eth0
iface eth0 inet static
	address 192.234.3.4
	netmask 255.255.255.0
	gateway 192.234.3.1

**Annie (Laravel Worker)**
auto eth0
iface eth0 inet static
	address 192.234.1.2
	netmask 255.255.255.0
	gateway 192.234.1.1

**Bertholdt (Laravel Worker)**
auto eth0
iface eth0 inet static
	address 192.234.1.3
	netmask 255.255.255.0
	gateway 192.234.1.1

**Reiner (Laravel Worker)**
auto eth0
iface eth0 inet static
	address 192.234.1.4
	netmask 255.255.255.0
	gateway 192.234.1.1

**Armin (PHP Worker)**
auto eth0
iface eth0 inet static
	address 192.234.2.2
	netmask 255.255.255.0
	gateway 192.234.2.1
up echo 'nameserver 192.234.4.3' > /etc/resolv.conf   

**Eren (PHP Worker)**
auto eth0
iface eth0 inet static
	address 192.234.2.3
	netmask 255.255.255.0
	gateway 192.234.2.1
up echo 'nameserver 192.234.4.3' > /etc/resolv.conf   

**Mikasa (PHP Worker)**
auto eth0
iface eth0 inet static
	address 192.234.2.4
	netmask 255.255.255.0
	gateway 192.234.2.1
up echo 'nameserver 192.234.4.3' > /etc/resolv.conf   

**Erwin (Client)**
auto eth0
iface eth0 inet dhcp
hwaddress ether 82:72:f8:e4:51:d0

**Zeke (Client)**
auto eth0
iface eth0 inet dhcp
hwaddress ether 3a:ef:29:94:f1:8a

Script Awal

**Paradis (DHCP Relay)**
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.234.0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start

**Fritz (DNS Server)**
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y

**Tybur (DHCP Server)**
echo 'nameserver 192.234.4.3' > /etc/resolv.conf   
apt-get update
apt install isc-dhcp-server -y

**Warhammer (Database Server)**
echo 'nameserver 192.234.4.3' > /etc/resolv.conf  
apt-get update
apt-get install mariadb-server -y
service mysql start

**PHP Worker**
apt-get update
apt-get install nginx -y
apt-get install wget unzip -y
apt-get install php7.3-fpm php7.3-common php7.3-mysql php7.3-gmp php7.3-curl php7.3-intl php7.3-mbstring php7.3-xmlrpc php7.3-gd php7.3-xml php7.3-cli php7.3-zip -y

**Laravel Worker**
echo 'nameserver 192.234.4.3' > /etc/resolv.conf   
apt-get update
apt-get install mariadb-client -y

**Colossal (Load Balancer PHP)**
echo 'nameserver 192.234.4.3' > /etc/resolv.conf   
apt-get update
apt-get install bind9 -y
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y
service nginx start

**Client**
apt-get update
apt install lynx -y
apt install htop -y
apt install apache2-utils -y
apt-get install jq -y

# 0

**Konfigurasi pada Fritz (DNS Server)**

echo 'zone "marley.it35.com" {
    type master;
    file "/etc/bind/sites/marley.it35.com";
};
zone "eldia.it35.com" {
    type master;
    file "/etc/bind/sites/eldia.it35.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/sites
cp /etc/bind/db.local /etc/bind/sites/marley.it35.com
cp /etc/bind/db.local /etc/bind/sites/eldia.it35.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     marley.it35.com. root.marley.it35.com. (
                        2024102301      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      marley.it35.com.
@       IN      A       192.234.1.2    ; IP Annie
www     IN      CNAME   marley.it35.com.' > /etc/bind/sites/marley.it35.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     eldia.it35.com. root.eldia.it35.com. (
                            2024102301         ; Serial
                            604800              ; Refresh
                            86400              ; Retry
                            2419200              ; Expire
                            604800 )            ; Negative Cache TTL
;
@       IN      NS      eldia.it35.com.
@       IN      A       192.234.2.2    ; IP Armin
www     IN      CNAME   eldia.it35.com.' > /etc/bind/sites/eldia.it35.com

echo 'options {
    directory "/var/cache/bind";

    forwarders {
        192.168.122.1;
    };

    // dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart

# 1

**Topologi**

# 2

**Konfigurasi pada Tybur (DHCP Server)**

echo '
subnet 192.234.1.0 netmask 255.255.255.0 {
range 192.234.1.5 192.234.1.25;
range 192.234.1.50 192.234.1.100;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart

# 3

**Konfigurasi pada Tybur (DHCP Server)**

echo '
subnet 192.234.1.0 netmask 255.255.255.0 {
	range 192.234.1.5 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.09 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart

# 4

**Konfigurasi pada Paradis (DHCP Relay)**

echo '
SERVERS="192.234.4.2"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
' > /etc/default/isc-dhcp-relay

echo '
net.ipv4.ip_forward=1
' > /etc/sysctl.conf

service isc-dhcp-relay restart

**Konfigurasi pada Tybur (DHCP Server)**

echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server

echo '
subnet 192.234.1.0 netmask 255.255.255.0 {
	range 192.234.1.05 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
	option routers 192.234.1.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.3;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.09 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
	option routers 192.234.2.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.3;
}

subnet 192.234.3.0 netmask 255.255.255.0 {
	option routers 192.234.3.1;
}

subnet 192.234.4.0 netmask 255.255.255.0 {
	option routers 192.234.4.1;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart

# 5

**Konfigurasi pada Tybur (DHCP Server)**
echo '
INTERFACESv4="eth0"
INTERFACESv6=""
' > /etc/default/isc-dhcp-server

echo '
subnet 192.234.1.0 netmask 255.255.255.0 {
	range 192.234.1.5 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
	option routers 192.234.1.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.3;
	default-lease-time 360;
	max-lease-time 5220;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.9 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
	option routers 192.234.2.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.3;
	default-lease-time 1800;
	max-lease-time 5220;
}

subnet 192.234.3.0 netmask 255.255.255.0 {
	option routers 192.234.3.1;
}

subnet 192.234.4.0 netmask 255.255.255.0 {
	option routers 192.234.4.1;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart

# 6

**Konfigurasi pada PHP Worker**

service nginx start
service php7.3-fpm start

mkdir -p /var/www/eldia.it35.com

wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=1TvebIeMQjRjFURKVtA32lO9aL7U2msd6' -O /root/bangsaEldia.zip
unzip -o /root/bangsaEldia.zip -d /var/www/eldia.it35.com

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/eldia.it35.com
ln -s /etc/nginx/sites-available/eldia.it35.com /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

echo '
server {
  listen 80;
  listen [::]:80;

  root /var/www/eldia.it35.com;
  index index.php index.html index.htm;

  server_name eldia.it35.com;

  location / {
    try_files $uri $uri/ =404;
  }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
  }

  location ~ /\.ht {
    deny all;
  }
}' > /etc/nginx/sites-available/eldia.it35.com

service nginx restart

# 7

**Konfigurasi pada Colossal (Load Balancer PHP)**

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

echo ' upstream worker {
        #    hash $request_uri consistent;
        #    least_conn;
        #    ip_hash;
    server 192.234.2.2;
    server 192.234.2.3;
    server 192.234.2.4;
}

server {
    listen 80;
    server_name eldia.it35.com www.eldia.it35.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html index.php;

    server_name _;

    location / {
        proxy_pass http://worker;
    }
} ' > /etc/nginx/sites-available/lb_php

ln -s /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

service nginx restart

**Konfigurasi pada Fritz (DNS Server)**

echo 'zone "marley.it35.com" {
    type master;
    file "/etc/bind/sites/marley.it35.com";
};
zone "eldia.it35.com" {
    type master;
    file "/etc/bind/sites/eldia.it35.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/sites
cp /etc/bind/db.local /etc/bind/sites/marley.it35.com
cp /etc/bind/db.local /etc/bind/sites/eldia.it35.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     marley.it35.com. root.marley.it35.com. (
                        2024102301      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      marley.it35.com.
@       IN      A       192.234.1.2    ; IP Annie
www     IN      CNAME   marley.it35.com.' > /etc/bind/sites/marley.it35.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     eldia.it35.com. root.eldia.it35.com. (
                            2024102301         ; Serial
                            604800              ; Refresh
                            86400              ; Retry
                            2419200              ; Expire
                            604800 )            ; Negative Cache TTL
;
@       IN      NS      eldia.it35.com.
@       IN      A       192.234.3.4    ; IP Colossal
www     IN      CNAME   eldia.it35.com.' > /etc/bind/sites/eldia.it35.com

echo 'options {
    directory "/var/cache/bind";

    forwarders {
        192.168.122.1;
    };

    // dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart

# 8 & 9

        #    hash $request_uri consistent;
        #    least_conn;
        #    ip_hash;

Ada 4 algoritma, round robin, hash, least connection, ip hash. Uncomment salah satu atau tidak sama sekali

# 9

Uncomment least connection, buat mengurangi worker beri comment worker

# 10

**Konfigurasi pada Colossal (Load Balancer PHP)**

mkdir /etc/nginx/supersecret
htpasswd -b -c /etc/nginx/supersecret/htpasswd arminannie jrkmit35

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

echo ' upstream worker {
        #    hash $request_uri consistent;
        #    least_conn;
        #    ip_hash;
    server 192.234.2.2;
    server 192.234.2.3;
    server 192.234.2.4;
}

server {
    listen 80;
    server_name eldia.it35.com www.eldia.it35.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html index.php;

    server_name _;

    location / {
        proxy_pass http://worker;
    }

    auth_basic "Restricted Content";
    auth_basic_user_file /etc/nginx/supersecret/htpasswd;
} ' > /etc/nginx/sites-available/lb_php

ln -s /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

service nginx restart

# 11

**Konfigurasi pada Colossal (Load Balancer PHP)**

mkdir -p /etc/nginx/supersecret
htpasswd -b -c /etc/nginx/supersecret/htpasswd arminannie jrkmit35

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

echo ' upstream worker {
        #    hash $request_uri consistent;
        #    least_conn;
        #    ip_hash;
    server 192.234.2.2;
    server 192.234.2.3;
    server 192.234.2.4;
}

server {
    listen 80;
    server_name eldia.it35.com www.eldia.it35.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html index.php;

    server_name _;

    location / {
        proxy_pass http://worker;
    }

    location /titan {
        proxy_pass http://attackontitan.fandom.com;
        proxy_set_header Host attackontitan.fandom.com;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    auth_basic "Restricted Content";
    auth_basic_user_file /etc/nginx/supersecret/htpasswd;
} ' > /etc/nginx/sites-available/lb_php

ln -s /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

service nginx restart

# 12

**Konfigurasi pada Colossal (Load Balancer PHP)**

mkdir -p /etc/nginx/supersecret
htpasswd -b -c /etc/nginx/supersecret/htpasswd arminannie jrkmit35

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

echo ' upstream worker {
        #    hash $request_uri consistent;
        #    least_conn;
        #    ip_hash;
    server 192.234.2.2;
    server 192.234.2.3;
    server 192.234.2.4;
}

server {
    listen 80;
    server_name eldia.it35.com www.eldia.it35.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html index.php;

    server_name _;

    location / {
        allow 192.234.1.77;
        allow 192.234.1.88;
        allow 192.234.2.144;
        allow 192.234.2.156;
        deny all;
        proxy_pass http://worker;
    }

    location /titan {
        proxy_pass http://attackontitan.fandom.com;
        proxy_set_header Host attackontitan.fandom.com;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    auth_basic "Restricted Content";
    auth_basic_user_file /etc/nginx/supersecret/htpasswd;
} ' > /etc/nginx/sites-available/lb_php

ln -s /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

service nginx restart

config pada tybur (zeke saja buat test)

echo 'host Zeke{
        hardware ethernet fa:61:fb:1a:8d:5b;
        fixed-address 192.246.1.77;
}
' >> /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart

# 13

**Konfigurasi pada Warhammer (Database)**

apt-get update
apt-get install mariadb-server -y
service mysql start

echo '
[client-server]
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
[mysqld]
skip-networking=0
skip-bind-address
' > /etc/mysql/my.cnf 

sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/mariadb.conf.d/50-server.cnf

service mysql restart

**Lalu jalankan step di bawah**

mysql -u root -p

(password gausa diisi langsung enter)

CREATE USER 'it35'@'%' IDENTIFIED BY 'it35';
CREATE USER 'it35'@'localhost' IDENTIFIED BY 'it35';
CREATE DATABASE dbit35;
GRANT ALL PRIVILEGES ON *.* TO 'it35'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'it35'@'localhost';
FLUSH PRIVILEGES;

**Cek di setiap worker**

mysql --host=192.234.3.2 --port=3306 --user=it35 --password=it35 dbit35 -e "SHOW DATABASES;"
