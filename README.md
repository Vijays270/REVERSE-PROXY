<b> 
#REVERSE PROXY CONFIGURATIONS - squid and web server </b>

#ON SQUID SERVER
 1.Install Squid
#apt update
#apt install squid

2.Configure Squid as a Reverse Proxy

Ports to be Enabled


#vi /etc/squid/squid.conf

4.Set Up HTTP Port and Reverse Proxy Settings - </etc/squid/squid.conf>
# Squid reverse proxy configuration

#cache_dir ufs /var/spool/squid 100 16 256
cache_peer www.webserver.com parent 80 0 no-query originserver
cache_mem 256 MB
visible_hostname www.proxy.com

http_port 80 accel defaultsite=www.webserver.com
http_access allow all



4. Restart Squid
#systemctl restart squid

5.Verify Squid is Running on Port 80
#netstat -tuln | grep 80
#curl -I http://192.168.242.186 - CHECK FROM 3RD PARTY SERVER

6.Check the web server from squid
#curl -I http://192.168.242.133
#telnet 192.168.242.186 8080
#telnet 192.168.242.133 80

after config of apache2

Check Squid Logs: Check Squid logs to confirm that requests are being processed and forwarded:
	
Monitor the logs of the Squid proxy server. These logs provide information about the requests that Squid processes and how it handles them.
#tail -f /var/log/squid/access.log
#tail -f /var/log/squid/cache.log



1. Configure Web Server
#apt update
#apt install apache2

Ports to be Enabled
 
#ufw enable
##ufw reload
#ufw status

#vi /etc/apache2/sites-available/000-default.conf  - take a backup 
 <VirtualHost *:80>
    ServerName www.webserver1.com
    DocumentRoot /var/www/html

    # Ensure Apache logs the actual client's IP address
    RemoteIPHeader X-Forwarded-For
    RemoteIPTrustedProxy 192.168.242.186

    # Custom log format to include the client's IP address
    LogFormat "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b" combined
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

2.Enable the Required Apache Modules

#a2enmod remoteip
#a2enmod log_config
   
3.Restart apache

#systemctl restart apache2


