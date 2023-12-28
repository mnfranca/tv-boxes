# TV Box H96 Pro+

## Especificações originais

- Marca: Alfawise
- Modelo: H96Pro+
- CPU: S912
- Memória: 2GB / 16GB Flash
- Sistema Operacional: Android 7.1
- Voltagem: 5V DC

<img src="./aida64.jpg" height="400">

## Instalando o Linux (método 1)

- Remover todas as partições do cartão de memória a ser utilizado no processo. Criar uma nova partição FAT32;

- Gravar a imagem [Armbian_20.10_Arm-64_buster_current_5.9.0.img.xz](https://www.dropbox.com/scl/fi/ap1c72unbuypxl3249c6c/Armbian_20.10_Arm-64_buster_current_5.9.0.img.xz?rlkey=buie7sfec63wnj3x819fygnzx&dl=0) no **cartão de memória** usando o aplicativo [balenaEtcher](https://www.dropbox.com/s/airlf91bq0633wb/balenaEtcher-Setup-1.7.9.zip?dl=0);

- Remover o cartão de memória com segurança e inserir novamente no PC;

- Editar o arquivo \uEnv.txt no cartão de memória e deixar o conteúdo da seguinte forma:

```txt
LINUX=/zImage
INITRD=/uInitrd

# aml s9xxx
FDT=/dtb/amlogic/meson-gxm-q200.dtb
APPEND=root=LABEL=ROOTFS rootflags=data=writeback rw console=ttyAML0,115200n8 console=tty0 no_console_suspend consoleblank=0 fsck.fix=yes fsck.repair=yes net.ifnames=0
```

- Remover o cartão de memória com segurança;

- Inserir o cartão de memória na TV-Box e ligá-la;

- Acessar inicialmente com usuário **root** e senha **1234**;

- Alterar a senha do usuário **root** para **jedi1234**;

- Criar o usuário **user** com a senha **jedi1234**;

- Executar o aplicativo **armbian-config** para definir o time zone para **America/Campo_Grande** e o nome do host como **home-assistant-jedi**;

- Remover monitor, teclado e mouse da TV-Box e reiniciá-la;

- Abrir uma janela de prompt de comando no PC e acessar a TV-Box com o comando **ssh root@home-assistant-jedi**;

- Instalar o Linux na memória interna da TV-Box com o seguinte comando:

```bash
./install-aml.sh
```

- Executar o comando **shutdown now** para desligar a TV-Box. Desligar o cabo de exergia da TV-Box, remover o cartão de memória e religá-la.

## Atualizando o Linux

```bash
apt update
apt upgrade
```

## Atualizando o Linux Debian 10 (buster) para Debian 11 (bullseye) 

```bash
sudo apt install gcc-8-base -y
sudo apt --allow-releaseinfo-change update -y
sudo apt upgrade -y
```

- Reinicie o sistema;

- Altere os repositórios para apontar para Debian 11:

```bash
sudo nano /etc/apt/sources.list
```

- Edite o arquivo da seguinte forma:

```
# Debian 11 Bullseye
deb http://deb.debian.org/debian bullseye main contrib non-free
deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb http://security.debian.org/debian-security bullseye-security main
deb http://ftp.debian.org/debian bullseye-backports main contrib non-free
```

- Execute a atualiação:

```bash
sudo apt update -y
sudo apt full-upgrade -y
```

- Reinicie o sistema.

## Atualizando o Linux Debian 11 (bullseye) para Debian 12 (bookworm)

```bash
sudo apt update -y
sudo apt upgrade -y
```

- Altere os repositórios para apontar para Debian 12:

```bash
sudo nano /etc/apt/sources.list
```

- Edite o arquivo da seguinte forma:

```
# Debian 12 Bookworm
deb http://deb.debian.org/debian/ bookworm main non-free-firmware
deb http://deb.debian.org/debian/ bookworm-updates main non-free-firmware
deb http://security.debian.org/debian-security/ bookworm-security main non-free-firmware
deb http://deb.debian.org/debian/ bookworm-backports main non-free-firmware
```

- Atualize o sistema:

```bash
 sudo apt update -y
 sudo apt upgrade -y
 sudo apt dist-upgrade -y
```

- Reinicie o sistema.

## Instalando o Docker

- Instale o Docker com o comando **armbian-config**.

### Expandindo partições para uso do Docker

1. Reservar um sdcard dedicado para funcionar como armazenamento adicional;

2. Listando as unidades de armazenamento disponíveis no dispositivo:

```bash
fdisk -l
lsblk
```

3. Formatando as partições:

```bash
mkfs.ext4 -F /dev/mmcblk0p1
mkfs.ext4 -F /dev/mmcblk0p2
mkfs.ext4 -F /dev/mmcblk0p3
```

4. Mostrando os detalhes das partições:

```bash
parted /dev/mmcblk0p1 --script print
parted /dev/mmcblk0p2 --script print
parted /dev/mmcblk0p3 --script print
```

5. Recuperando o UUID da unidade do sdcard:

```bash
blkid /dev/mmcblk0p1
blkid /dev/mmcblk0p2
```

6. Alterando o arquivo **/etc/fstav** para mapear as pastas **/var/lib/docker** e **/opt** para as partições do sdcard:

```bash
UUID=4c67b9c5-8e01-4ad3-8157-485f027e9a60 /var/lib/docker    ext4    defaults           0 2
UUID=83a56db6-7ac7-4b57-9fea-18780630407f /opt               ext4    defaults           0 2
```

7. Executar a montagem da pasta **/var/lib/docker** no pendrive:

```bash
mount -a
```

8. Reinicie o sistema.

## Instalando o MySQL

1. Instalar o aplicativo para liberação da porta de rede do MySQL:

```bash
apt install ufw
```

2. Liberar a porta de rede do MySQL:

```bash
ufw allow 3306
```

3. Mostrar as portas de rede abertas:

```bash
netstat -tulpn | grep LISTEN
```

4. Instalar o MySQL via docker:

```bash
docker run --name mysql --privileged --restart=unless-stopped -e MYSQL_ROOT_PASSWORD=jedi1234 -p 3306:3306 -d mysql
```

## Instalado servidor Git

1. Instalar o aplicativo:

```bash
apt install git
```

2. Criar o usuário gestor do Git (senha jedi1234):

```bash
adduser git
passwd git
```

3. Configurar SSH para o Git:

```bash
su -l git
cd ~
mkdir .ssh
chmod 700 .ssh/
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
cd .ssh
ssh-keygen -t rsa
```

Ao solicitar **passphrase**, informar **jedi1234**;

4. Adicionar a chave criada no arquivo **authorized_keys**

```bash
cat id_rsa.pub >> authorized_keys
```

5. Criar a pasta dos respositórios Git:

```bash
cd ~ 
mkdir test.git
cd test.git
git config --global init.defaultBranch main
git init --bare
```

6. Com usuário **root**, editar o arquivo abaixo para permitir o acesso via SSH

```bash
nano /etc/ssh/sshd_config
```

7. Adicionar a seguinte linha no final do arquivo:

```
AllowUsers root git
```

9. Reiniciar o dispositivo

10. Acessar o repositório remoto criado:

```bash
git remote add origin git@192.168.100.15:test.git
```

--------------
2. Criar a variável de ambiente para o GitLab:

```bash
export GITLAB_HOME=/opt/gitlab
```

3. Liberar a porta de rede do GitLab:

```bash
ufw allow 443
ufw allow 80
ufw allow 22
```

4. Instalar o GitLab via docker:

```bash
docker run --detach \
  --hostname gitlab.jedi.com \
  --publish 443:443 --publish 80:80 --publish 24:22 \
  --name gitlab \
  --privileged \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

## Instalando o Oracle 

1. Baixe a imagem do Oracle:

```bash
docker pull container-registry.oracle.com/database/express:latest
```

2. Execute o container do Oracle:

```bash
docker run -d --name oracle-xe --platform linux/amd64 -p 1521:1521 -p 5500:5500 -e ORACLE_PWD=jedi1234 container-registry.oracle.com/database/express
```

## Configurando o Samba

- Instalar e configurar o Samba utilizando o comando **armbian-config**;

- Dar acesso externo à pasta do Home Assistant com o seguinte comando:

```bash
chmod 777 -R /home/homeassistant/
```

## Instalando Home Assistant Container

```bash
apt install apparmor jq wget curl udisks2 libglib2.0-bin network-manager dbus lsb-release systemd-journal-remote -y
```

```bash
curl -fsSL get.docker.com | sh
```

```bash
docker run -d --name homeassistant --privileged --restart=unless-stopped -e TZ=America/Campo_Grande -v /home/homeassistant:/config --network=host ghcr.io/home-assistant/home-assistant:stable
```

- A partir de um PC na mesma rede em que estiver a TV-Box, acessar **http://home-assistant-jedi:8123/**;

- Criar o usuário **admin** com senha **jedi1234**.

### Instalando HACS

- Executar os comandos a seguir:

```bash
docker exec -it homeassistant bash
```

```bash
wget -O - https://get.hacs.xyz | bash -
```

- Reiniciar o Home Assistant;

- Instalar a integração HACS no Home Assistant.

## Unbrick

- [Unbrick](./unbrick/readme.md)

## Comandos Linux úteis

- Mostrar a versão da distribuição do Linux:

```bash
cat /etc/os-release
```

## Referências

- [Instruções de instalação](https://forum.armbian.com/topic/27825-proper-way-to-install-armbian-linux-on-h96-pro-plus-s912-or-equivalent-cli-ver/);
- [Atualização do Python](https://computingforgeeks.com/how-to-install-python-on-debian-linux/).
- [Oracle](https://mikedietrichde.com/2023/07/05/oracle-database-19c-on-arm-platform-is-available/)
