---
author: dzamo
title: Cobbler en CentOS 7
date: 01/03/2022
---

# Instalación y configuración mínima de Cobbler sobre CentOS 7

![GUI Cobbler][cobbler.init]

## Objetivo

> En este artículo se describe como instalar y configurar Cobbler sobre CentOS 7.

## Qué es Cobbler?

Cobbler es un servidor de instalación de Linux gratuito y de código abierto que se puede utilizar para automatizar los entornos de instalación de red desde una ubicación central. Le permite instalar sistemas operativos automáticamente en múltiples sistemas simultáneamente y también lo ayuda a administrar actualizaciones de paquetes, administración de configuración, administración de DNS, DHCP y mucho más. Es una alternativa a Satelllite de RHEL.

Con Cobbler, entre otras tareas de administración (oruestación), se puede realizar instalaciones de servidores basados en varios sistemas operativos. Se puede instalar y reinstalar rápidamente servidores físicos y máquinas virtuales a través del arranque en red (PXE), y también puede administrar servicios de DHCP, DNS, etc.

En este trabajo, se instala y configura Cobbler en un servidor CentOS 7, que es un guest VM basado en VMWare Player.

### Dependencias cumplidas

Las tareas se han realizado en un servidor con las siguientes especificaciones ya cumplidas. Estas son:

- Distribución Linux: CentOS 7. Con todos los parches actualizados.
- El sistema Linux tiene una configuración mínima inicial.
- Para las tareas, salvo que se indique expresamente, se ha utilizado los comandos `sudo`. De modo que este paquete ya esta correctamente configurado.
- Ip donde se instala: `172.16.38.32`
- Resolución DNS - IP utilizada en el laboratorio: `malbec.my-lenovo.net 172.16.38.32`

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
~$ firewall-cmd --add-port=80/tcp --permanent; firewall-cmd --add-port=443/tcp --permanent; 
~$ firewall-cmd --add-service=dhcp --permanent; firewall-cmd --add-port=69/tcp --permanent; 
~$ firewall-cmd --add-port=69/udp --permanent; firewall-cmd --add-port=4011/udp --permanent; 
~$ firewall-cmd --reload
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
## Ingreso a la gui web de Cobbler

En [esta captura][cobbler.init] se muestra el ingreso a la interface web del Cobbler instalado. Las credenciales para logarse inicialmente es:

|usuario|contraseña
|:--:|:--:
|cobbler|cobbler


[cobbler.init]: img/cobbler.init.png
