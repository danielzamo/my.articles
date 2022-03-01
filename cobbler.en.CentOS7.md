---
author: dzamo
title: Cobbler en CentOS 7
date: 01/03/2022
---

# Instalar Cobbler en CentOS 7

## Objetivo

> En este artículo se describe como instalar y configurar Cobbler sobre CentOS 7.

### Dependencias cumplidas

Las tareas se han realizado en un servidor con las siguientes especificaciones ya cumplidas. Estas son:

- Distribución Linux: CentOS 7. Con todos los parches actualizados.
- El sistema Linux tiene una configuración mínima inicial.
- Para las tareas, salvo que se indique expresamente, se ha utilizado los comandos `sudo`. De modo que este paquete ya esta correctamente configurado.

## Actualizando el sistema

```bash
~$ sudo yum -y update
```

## Instalar algunos paquetes extras (opcional) y repositorio epel (obligatorio)
```bash
~$ sudo yum -y install vim-enhanced epel-release 
~$ sudo echo -e "\n# Add alias\nalias vi='vim'" >> /etc/profile
~$ source /etc/profile
```

## Instalar y configurar Cobbler
```bash
~$ sudo yum -y install cobbler cobbler-web dnsmasq syslinux pykickstart xinetd
~$ sudo systemctl enable --now cobblerd
~$ sudo setenforce 0                                                             # No es obligatorio
~$ sed -i.org -e 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config    # No es obligatorio
~$ #getenforce 
~$ systemctl enable --now httpd
~$ firewall-cmd --add-port=80/tcp --permanent; firewall-cmd --add-port=443/tcp --permanent; firewall-cmd --add-service=dhcp --permanent; firewall-cmd --add-port=69/tcp --permanent; firewall-cmd --add-port=69/udp --permanent; firewall-cmd --add-port=4011/udp --permanent; firewall-cmd --reload
~$ #systemctl stop firewalld
~$ cat /etc/httpd/conf.d/cobbler.conf
AliasMatch ^/cblr(?!/svc/)(.*)?$ "/var/www/cobbler$1"
AliasMatch ^/cobbler_track(.*)?$ "/var/www/cobbler$1"
#AliasMatch ^/cobbler(.*)?$ "/var/www/cobbler$1"
Alias /cobbler /var/www/cobbler
Alias /cobbler_webui_content /var/www/cobbler_webui_content
WSGIScriptAliasMatch ^/cblr/svc/([^/]*) /var/www/cobbler/svc/services.py
<Directory "/var/www/cobbler">
    SetEnv VIRTUALENV 
    Options Indexes FollowSymLinks
    Order allow,deny
    Allow from all
</Directory>
ProxyRequests off
ProxyPass /cobbler_api http://127.0.0.1:25151/
ProxyPassReverse /cobbler_api http://127.0.0.1:25151/
BrowserMatch "MSIE" AuthDigestEnableQueryStringHack=On
<Directory "/var/www/cobbler/web/">
    Options Indexes FollowSymLinks
    Order allow,deny
    Allow from all
</Directory>
<IfVersion >= 2.4>
    <Location /cblr>
        Require all granted
    </Location>
</IfVersion>

~$ cat /etc/httpd/conf.d/cobbler_web.conf
<Directory "/usr/share/cobbler/web/">
        <IfModule mod_ssl.c>
            SSLRequireSSL
        </IfModule>
        <IfModule mod_nss.c>
            NSSRequireSSL
        </IfModule>
        SetEnv VIRTUALENV 
        Options Indexes MultiViews
        AllowOverride None
        Order allow,deny
        Allow from all
</Directory>
<Directory "/var/www/cobbler_webui_content/">
        <IfModule mod_ssl.c>
            SSLRequireSSL
        </IfModule>
        <IfModule mod_nss.c>
            NSSRequireSSL
        </IfModule>
        Options +Indexes +FollowSymLinks
        AllowOverride None
        Order allow,deny
        Allow from all
</Directory>
WSGISocketPrefix /var/run/wsgi
WSGIScriptAlias /cobbler_web /usr/share/cobbler/web/cobbler.wsgi
WSGIDaemonProcess cobbler_web display-name=%{GROUP}
WSGIProcessGroup cobbler_web
WSGIPassAuthorization On
<IfVersion >= 2.4>
    <Location /cobbler_web>
            Require all granted
    </Location>
    <Location /cobbler_webui_content>
        Require all granted
    </Location>
</IfVersion>

~$ openssl passwd -1       # genero password.
~$ cat /etc/cobbler/settings 
~$ cat /etc/cobbler/dhcp.template 
~$ cat /etc/cobbler/dnsmasq.template 
~$ cat /etc/cobbler/dhcp.template 
~$ systemctl restart cobblerd xinetd 
~$ cobbler check
~$ cobbler sync
```
