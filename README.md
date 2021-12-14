# zabbix6 Configuration

1-Before executed the command docker-compose.yml,Create volume for mysql and zabbix-frontend containers

docker volume create mysql
docker volume create letsencrypt
docker volume create sites-enabled
docker volume create zabbix
docker volume create www

2-Running this command docker-compose.yml up -d

3- Running this command for Letsencrypt
docker run -it --rm --name certbot \
	    -v "/var/lib/docker/volumes/www/_data:/usr/share/zabbix" \
	    -v "/var/lib/docker/volumes/letsencrypt/_data:/etc/letsencrypt" \
            certbot/certbot certonly

Answers : 2, yourmailaddress, Y, N, yourdomainname, /usr/share/zabbix

4-Just once login zabbix-frontend container 
docker exec -u root -it zabbix-frontend bash
	chown -R zabbix:zabbix /etc/letsencrypt/
	cd /etc/apache2/sites-enabled/
	ln -s /etc/zabbix/apache_ssl.conf zabbix_ssl.conf
	chown -h zabbix:zabbix zabbix_ssl.conf
exit

5-Edit for letsencrypt configuration
cd /var/lib/docker/volumes/zabbix/_data
 vi apache_ssl.conf
 #SSLCertificateFile /etc/ssl/apache2/ssl.crt
 #SSLCertificateKeyFile /etc/ssl/apache2/ssl.key
 SSLCertificateFile /etc/letsencrypt/live/yourdomain.com/fullchain.pem
 SSLCertificateKeyFile /etc/letsencrypt/live/yourdomain.com/privkey.pem
save and exit

6- docker exec -it zabbix-frontend /etc/init.d/apache2 reload
