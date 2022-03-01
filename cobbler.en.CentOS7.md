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

**Nota:** En las siguientes sesiones de trabajo (realizadas desde el _CLI_ de Linux), el prompt del sistema es `~$ `
**Nota 2:** Para mas simplicidad de este artículo, los ficheros editados (configurados/modificados) son compartidos en este repositorio. En el código que se expone a continuación, se adjunta en formato de link del tipo `file:///` En los casos de datos sensibles, los mismos son ofuscados. 

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
#.... VER FICHERO

~$ cat /etc/httpd/conf.d/cobbler_web.conf
#.... VER FICHERO

~$ openssl passwd -1       # genero password.
~$ cat /etc/cobbler/settings
#.... VER FICHERO

~$ cat /etc/cobbler/dhcp.template 
#.... VER FICHERO

~$ cat /etc/cobbler/dnsmasq.template 
#.... VER FICHERO

~$ cat /etc/cobbler/dhcp.template 
#.... VER FICHERO

~$ systemctl restart cobblerd xinetd 
~$ cobbler check
~$ cobbler sync
```
