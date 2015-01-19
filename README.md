# Setup-OpenBay-BR2

##################################SetupOpenBay BR Limux Debian 7###########################################

apt-get update
apt-get -y upgrade
apt-get -y install nano unzip php5-fpm wget php5-mysql
nano /etc/apt/sources.list

####REPOSITÓRIOS######

deb http://nginx.org/packages/debian/ [Codename] nginx
deb-src http://nginx.org/packages/debian/ [Codename] nginx

apt-get update

gpg --keyserver subkeys.pgp.net --recv (sua key)

gpg --export --armor (sua key) | sudo apt-key add -  

apt-get install nginx

mkdir -p /var/www/openbay

#################################Cole nesse documento as seguintes linhas #################################

server {
listen 80;
server_name [Sub- / Domain or IP];

root /var/www/openbay/www;
index index.php;

set $yii_bootstrap “index.php”;

charset utf-8;
gzip on;
gzip_comp_level 5;
gzip_types text/plain text/css text/javascript application/javascript application/json application/x-javascript text/xml application/xml application/xml+rss;

location / {
index index.php $yii_bootstrap;
try_files $uri/ $uri /index.php?$query_string;
}

location ~ \.(js|css|png|jpg|gif|swf|ico|svg)$ {
fastcgi_hide_header Set-Cookie;

open_file_cache max=10000 inactive=14d;
open_file_cache_errors on;
open_file_cache_min_uses 3;
open_file_cache_valid 1m;
output_buffers 1 2m;

expires 14d;
}

location ~* \.php$ {
fastcgi_split_path_info ^(.+\.php)(.*)$;
set $fsn /$yii_bootstrap;
if (-f $document_root$fastcgi_script_name){
set $fsn $fastcgi_script_name;
}
fastcgi_pass localhost:9000;
include fastcgi_params;
fastcgi_param SCRIPT_FILENAME $document_root$fsn;
fastcgi_param PATH_INFO $fastcgi_path_info;
fastcgi_param PATH_TRANSLATED $document_root$fsn;
# fastcgi_param HTTPS $https_value;
}
}

###############################################################Fim ################################################################

nano /etc/nginx/conf.d/openbay.conf


#################Abrir o arquivo:################

nano /etc/php5/fpm/php.ini

#################Procurar pela linha "cgi.fix"##############


#################Trocar##########

;cgi.fix_pathinfo=1 por cgi.fix_pathinfo=0

#################Adcionar nas ultimas linhas ############

extension=pdo.so
extension=pdo_mysql.so

###########Salve!############

#############Abra o documento###########

nano /etc/php5/fpm/pool.d/www.conf

##########Procure por###########

listen = /var/run/php5-fpm.sock

###########Troque por ##########

listen = localhost:9000

###########Salve!############

service php5-fpm restart

###############Baixe os aruivos do openbay para a pasta root##################

cd openbay-master/src

mv * /var/www/openbay/

cd /var/www/openbay

chown -R www-data:www-data *

service nginx restart

###############Abra o navegador e coloque o dominio inserido em openbay.conf###############
