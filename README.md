## :rocket: Semana do MikroTik - TechLabs
:star_struck: Repositório de Instalação e Configuração.

[Grupo Do WhatsApp Semana do MikroTik](https://academy.techlabs.net.br/course/grupo-whatsapp)

## :computer: Requisitos de Software

*Instalação Será realizada no Proxmox - Mas pode ser instalado em um VMWARE ou VirtualBox usando seu PC Windows*

* PNETLab Bare Metal - *Obrigatorio Ubuntu 18.04 Server*
* PHPIPAM - *Instalação no Container*

---

* Links Para Download:
    * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (Recomendado)
    * [VMware](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)
    * [Proxmox VE](https://www.proxmox.com/en/downloads/proxmox-virtual-environment)
    * [Ubuntu 18.04.6 LTS Server .iso](https://releases.ubuntu.com/18.04/ubuntu-18.04.6-live-server-amd64.iso)
    * [Ubuntu 22.04.4 LTS Server .iso](https://releases.ubuntu.com/jammy/ubuntu-22.04.4-live-server-amd64.iso)
    * [PNETLAB Bare Metal](https://pnetlab.com/pages/documentationslug=install-bare-metal)
    * [PNETLAB Imagem .ova](https://pnetlab.com/pages/download)
---

## :computer: Instalação do PNETLAB e PHPIPAM

[![Assista ao vídeo](https://img.youtube.com/vi/_kztuDH6xj0/0.jpg)](https://www.youtube.com/watch?v=_kztuDH6xj0)

## :computer: Instalação do PNETLAB

### Instalação do Servidor

* Configuração do Disco:
```
Deve informar todo o disco disponível
```

* Configuração do Usuário:
    * Informe os Dados do Usuário:
```
Seu Nome [Nilson Pessim]
Nome do Servidor [pnetlab]
Usuário do Servidor [nilson]
Senha do Servidor [nilson]
```

* Pacotes Adicionais:
```
Instalar o OpenSSH
```

* Após Instalação do Ubuntu Server:

    * Faça Login via SSH:
```
ssh [user]@[ip]
```

* Adicionar Repositório: */etc/apt/sources.list*
```
deb [trusted=yes] http://repo.pnetlab.com ./
```

* Check a Conexão do Servidor com a Internet (Resolução de Nomes)
```
echo "nameserver 8.8.8.8" > /etc/resolv.conf
apt-get -y update && apt -y upgrade 
apt-get -y purge netplan.io
apt-get install pnetlab -y
```

* Reinicie o Servidor
```
reboot
```

* [Para Realizar o Upgrade do PNETLAB](https://pnetlab.com/pages/documentation?slug=how-to-upgrade-pnetlab)


## Instalação do MikroTik

* Configuração MikroTik RouterOSv6:
```
mkdir -p /opt/unetlab/addons/qemu/mikrotik-6.49.15
cd /opt/unetlab/addons/qemu/mikrotik-6.49.15
wget https://download.mikrotik.com/routeros/6.49.15/chr-6.49.15.vmdk.zip
unzip chr-6.49.15.vmdk.zip
/opt/qemu/bin/qemu-img convert -f vmdk -O qcow2 chr-6.49.15.vmdk hda.qcow2
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

* Configuração MikroTik RouterOSv7:
```
mkdir -p /opt/unetlab/addons/qemu/mikrotik-7.15.1
cd /opt/unetlab/addons/qemu/mikrotik-7.15.1
wget https://download.mikrotik.com/routeros/7.15.1/chr-7.15.1.vmdk.zip
unzip chr-7.15.1.vmdk.zip
/opt/qemu/bin/qemu-img convert -f vmdk -O qcow2 chr-7.15.1.vmdk hda.qcow2
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

---

### :computer: Instalação do PHPIPAM

* Atualizar os Repositórios e Pacotes do Sistema:
```
apt -y update && apt -y upgrade
```

* Instalação dos Pacotes Necessários (Servidor Web e Banco de Dados):
```
apt -y install apache2 libapache2-mod-php php mariadb-client mariadb-server
apt -y install php-gmp php-mysql php-mbstring php-gd php-curl php-xml php-pear
a2enmod rewrite
systemctl restart apache2
```

* Configurar Banco de Dados:
```
mariadb -p

CREATE DATABASE phpipam;
GRANT ALL PRIVILEGES ON phpipam.* TO 'phpipam'@'localhost' IDENTIFIED BY 'techlabs';
FLUSH PRIVILEGES;

quit;
```

* Configurar PHPIPAM:
```
cd /tmp
wget https://github.com/phpipam/phpipam/releases/download/v1.6.0/phpipam-v1.6.0.tgz
tar vxf phpipam-v1.6.0.tgz
```

* config.php:
```
$db['host'] = "localhost";
$db['user'] = "phpipam";
$db['pass'] = "techlabs";
$db['name'] = "phpipam";
```

* Acesso Padrão caso falhe a Instalação: 
```
admin / ipamadmin
```

## :computer: Repositório Completo - Semana do MikroTik

* [Google Drive](https://drive.google.com/drive/folders/1eAZyWBiSuX6CJuBf3fBO0_AUx3JD_LoU?usp=sharing)
