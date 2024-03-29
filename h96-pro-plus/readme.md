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
apt install gcc-8-base -y
apt --allow-releaseinfo-change update -y
apt upgrade -y
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

3. Particionando o disco:

```bash
fdisk /dev/mmcblk0
```

Tamanhos para cartão de memória de 64GB:

```
/opt > 16GB
/var/lib/docker > 40GB
SWAP > 8GB
```

4. Formatando as partições:

```bash
mkfs.ext4 -F /dev/mmcblk0p1
mkfs.ext4 -F /dev/mmcblk0p2
```

5. Mostrando os detalhes das partições:

```bash
parted /dev/mmcblk0p1 --script print
parted /dev/mmcblk0p2 --script print
```

6. Recuperando o UUID da unidade do sdcard:

```bash
blkid /dev/mmcblk0p1
blkid /dev/mmcblk0p2
```

7. Alterando o arquivo **/etc/fstav** para mapear as pastas **/var/lib/docker** e **/opt** para as partições do sdcard:

```bash
UUID=4c67b9c5-8e01-4ad3-8157-485f027e9a60 /var/lib/docker    ext4    defaults           0 2
UUID=83a56db6-7ac7-4b57-9fea-18780630407f /opt               ext4    defaults           0 2
```

8. Executar a montagem da pasta **/var/lib/docker** no pendrive:

```bash
mount -a
```

9. Reinicie o sistema.

## Criando partição SWAP no cartão de memória

1. Mostrar as partições swap:

```bash
swapon --show
```

2. Mostrar os arquivos de swap:

```bash
cat /proc/swaps
```

3. Mostrar as informações de uso de memória:

```bash
free -m
```

4. Listar as partições do cartão de memória:

```bash
fdisk -l /dev/mmcblk0
```

5. Criar a partição para uso do swap no cartão de memória:

```bash
fdisk /dev/mmcblk0
```

Utilizar os menus do sistema para criar partição e tipo de arquivos para a partição swap.

6. Confirma as alterações de partições:

```bash
partprobe /dev/mmcblk0
```

7. Criar a assinatura de swap na partição criada:

```bash
mkswap /dev/mmcblk0p3
```

8. Ativar a partição de swap temporariamente:

```bash
swapon /dev/mmcblk0p3
```

9. Tornar a partição persistente:

```bash
sudo nano /etc/fstab
```

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
mkdir /opt/mysql
chown -R 200 /opt/mysql
```

```bash
docker pull mysql
docker run --name mysql --privileged --restart=unless-stopped -e MYSQL_ROOT_PASSWORD=jedi1234 -v /opt/mysql:/var/lib/mysql -p 3306:3306 -d mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_bin --lower_case_table_names=1
```

## Instalando o phpMyAdmin para MySQL

```bash
ufw allow 8080
docker pull phpmyadmin
docker run --name phpmyadmin --restart=unless-stopped -d -e PMA_HOST=192.168.100.15 -e UPLOAD_LIMIT=30M -p 8080:80 phpmyadmin
```

Usar usuário **root** e senha **jedi1234**.

## Instalando servidor Git

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

## Instalando o GitBucket

```bash
mkdir /opt/gitbucket
chmod 700 /opt/gitbucket
ufw allow 8082
ufw allow 29418
docker pull gitbucket/gitbucket
```

- Opção 1: 

```
docker run --name gitbucket --restart=unless-stopped --privileged -d -p 8082:8082 -p 29418:29418 -e GITBUCKET_PORT=8082 gitbucket/gitbucket
```

- Opção 2 (utilizando banco de dados MySQL externo): 

```
docker run --name gitbucket --restart=unless-stopped --privileged -d -p 8082:8082 -p 29418:29418 -e GITBUCKET_PORT=8082 -e GITBUCKET_DB_URL="jdbc:mysql://192.168.100.15/gitbucket?useUnicode=true&characterEncoding=utf8&allowPublicKeyRetrieval=true" -e GITBUCKET_DB_USER=root -e GITBUCKET_DB_PASSWORD=jedi1234 gitbucket/gitbucket
```

- Opção 3 (utilizando banco de dados MySQL externo e volume personalizado) **NÃO FUNCIONA**: 

```
docker run --name gitbucket --restart=unless-stopped --privileged -d -p 8082:8082 -p 29418:29418 -v gitbucket:/opt/gitbucket -e GITBUCKET_PORT=8082 -e GITBUCKET_DB_URL="jdbc:mysql://192.168.100.15/gitbucket?useUnicode=true&characterEncoding=utf8&allowPublicKeyRetrieval=true" -e GITBUCKET_DB_USER=root -e GITBUCKET_DB_PASSWORD=jedi1234 gitbucket/gitbucket
```

Usuário padrão **root** e senha padrão **root**. Mudar a senha para **jedi1234**.

## Instalando Active Directory (com Samba)

- Instalação: 

```bash
ufw allow 389
ufw allow 636
docker pull smblds/smblds
docker run --name smblds --restart=unless-stopped --privileged -p 389:389 -p 636:636 -d -e TZ=America/Campo_Grande -e INSECURE_LDAP=true -e INSECURE_PASSWORDSETTINGS=true smblds/smblds:latest
```

- Criação de usuário acessando o terminal do container:

```
docker exec -it containerid /bin/sh
samba-tool user add USERNAME PASSWORD
```

A senha inicial do administrador é **Passw0rd**.

Dados do servidor:

```
Once the above files are installed, your Samba AD server will be ready to use
Server Role:           active directory domain controller
Hostname:              8412da7b9bb5
NetBIOS Domain:        SAMDOM
DNS Domain:            samdom.example.com
DOMAIN SID:            S-1-5-21-3170988374-1446572079-941881466
```

## Instalando servidor Maven

1. Instalar o JDK:

```bash
apt install default-jdk
```

2. Visualizar o caminho da instalação do Java (JAVA_HOME):

```bash
dirname $(dirname $(readlink -f $(which javac)))
```

3. Baixar o pacote do Maven:

```bash
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz
```

4. Descompactar os arquivos do Maven:

```bash
tar xzvf apache-maven-3.9.6-bin.tar.gz
```

5. Adicionar o seguinte trecho ao arquivo **~/.profile**:

```
# Add /opt/apache-maven-3.9.6 to PATH for all users
export PATH="/opt/apache-maven-3.9.6/bin:$PATH"
```

6. Reiniciar o sistema.

## Instalando o Nexus

```bash
apt install apparmor
ufw allow 8081
mkdir /opt/nexus
chown -R 200 /opt/nexus
docker pull klo2k/nexus3
docker run --privileged -d -p 8081:8081 -v /opt/nexus:/nexus-data --name nexus --restart=unless-stopped klo2k/nexus3
```

A senha do usuário **admin** encontra-se no arquivo **admin.password**, na raiz do volume do container.

Para fazer deploy de biblioteca no Nexus, utilizar o comando a seguir:

```bash
mvn deploy:deploy-file \
    -DgroupId=com.example.test \
    -DartifactId=test-module \
    -Dversion=1.0.0 \
    -DgeneratePom=true \
    -Dpackaging=jar \
    -DrepositoryId=sample-rel \
    -Durl=http://nexus.private.net/Your_Nexus_Repository_Path \
    -Dfile=./PATH_TO_JAR_FILE
```

## Instalando o Oracle para Oracle Linux:

1. Instalar o Oracle Linux:

```bash
docker pull arm64v8/oraclelinux:8.9
docker run -itd -h oracle-linux --name oracle-linux --restart=unless-stopped arm64v8/oraclelinux:8.9
```

2. Instalar o Oracle Database

```bash
docker exec -it containerid bash
cat /etc/oracle-release
dnf install oracle-database-preinstall-19c
dnf -y localinstall oracle-database-preinstall-19c-1.0-3.el8.aarch64.rpm
```

Reinicie o sistema.

``` bash
passwd oracle
mkdir -p /home/oracle/product/19.0.0/dbhome_1
mkdir -p /home/oracle/oradata
chown -R oracle:oinstall /home/oracle
chmod -R 775 /home/oracle
mkdir /home/oracle/scripts
```

Arquivo de configuração:

```bash
cat > /home/oracle/scripts/setEnv.sh <<EOF
# Oracle Settings
export TMP=/tmp
export TMPDIR=\$TMP

export ORACLE_HOSTNAME=ol8-19.localdomain
export ORACLE_UNQNAME=cdb1
export ORACLE_BASE=/home/oracle
export ORACLE_HOME=\$ORACLE_BASE/product/19.0.0/dbhome_1
export ORA_INVENTORY=/home/oracle/oraInventory
export ORACLE_SID=cdb1
export PDB_NAME=pdb1
export DATA_DIR=/home/oracle/oradata

export PATH=/usr/sbin:/usr/local/bin:\$PATH
export PATH=\$ORACLE_HOME/bin:\$PATH

export LD_LIBRARY_PATH=\$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=\$ORACLE_HOME/jlib:\$ORACLE_HOME/rdbms/jlib
EOF
```

```bash
echo ". /home/oracle/scripts/setEnv.sh" >> /home/oracle/.bash_profile
```

```bash
cat > /home/oracle/scripts/start_all.sh <<EOF
#!/bin/bash
. /home/oracle/scripts/setEnv.sh

export ORAENV_ASK=NO
. oraenv
export ORAENV_ASK=YES

dbstart \$ORACLE_HOME
EOF
```

```bash
cat > /home/oracle/scripts/stop_all.sh <<EOF
#!/bin/bash
. /home/oracle/scripts/setEnv.sh

export ORAENV_ASK=NO
. oraenv
export ORAENV_ASK=YES

dbshut \$ORACLE_HOME
EOF
```

```bash
chown -R oracle:oinstall /home/oracle/scripts
chmod u+x /home/oracle/scripts/*.sh
```

Scripts para iniciar e parar o banco de dados:

```bash
~/scripts/start_all.sh
~/scripts/stop_all.sh
```

Iniciar a instalação:

```bash
docker exec -it --user oracle bcc bash
. /home/oracle/scripts/setEnv.sh
cd $ORACLE_HOME
unzip -oq /home/LINUX.ARM64_1919000_db_home.zip
export CV_ASSUME_DISTID=OEL8.9
```

```bash
./runInstaller -ignorePrereq -waitforcompletion -silent                        \
    -responseFile ${ORACLE_HOME}/install/response/db_install.rsp               \
    oracle.install.option=INSTALL_DB_SWONLY                                    \
    ORACLE_HOSTNAME=${ORACLE_HOSTNAME}                                         \
    UNIX_GROUP_NAME=oinstall                                                   \
    INVENTORY_LOCATION=${ORA_INVENTORY}                                        \
    SELECTED_LANGUAGES=en,en_GB                                                \
    ORACLE_HOME=${ORACLE_HOME}                                                 \
    ORACLE_BASE=${ORACLE_BASE}                                                 \
    oracle.install.db.InstallEdition=EE                                        \
    oracle.install.db.OSDBA_GROUP=dba                                          \
    oracle.install.db.OSBACKUPDBA_GROUP=dba                                    \
    oracle.install.db.OSDGDBA_GROUP=dba                                        \
    oracle.install.db.OSKMDBA_GROUP=dba                                        \
    oracle.install.db.OSRACDBA_GROUP=dba                                       \
    SECURITY_UPDATES_VIA_MYORACLESUPPORT=false                                 \
    DECLINE_SECURITY_UPDATES=true
```

Avisos da instalação:

```
[WARNING] [INS-32008] Oracle base location cant be same as the user home directory.
   CAUSE: The specified Oracle base is same as the user home directory.
   ACTION: Provide an Oracle base location other than the user home directory.
[WARNING] [INS-32055] The Central Inventory is located in the Oracle base.
   ACTION: Oracle recommends placing this Central Inventory in a location outside the Oracle base directory.
```

Criar o banco de dados:

```bash
dbca -silent -createDatabase                                                   \
     -templateName General_Purpose.dbc                                         \
     -gdbname ${ORACLE_SID} -sid  ${ORACLE_SID} -responseFile NO_VALUE         \
     -characterSet AL32UTF8                                                    \
     -sysPassword jedi1234                                                     \
     -systemPassword jedi1234                                                  \
     -createAsContainerDatabase true                                           \
     -numberOfPDBs 1                                                           \
     -pdbName ${PDB_NAME}                                                      \
     -pdbAdminPassword jedi1234                                                \
     -databaseType MULTIPURPOSE                                                \
     -memoryMgmtType auto_sga                                                  \
     -totalMemory 2000                                                         \
     -storageType FS                                                           \
     -datafileDestination "${DATA_DIR}"                                        \
     -redoLogFileSize 50                                                       \
     -emConfiguration NONE                                                     \
     -ignorePreReqs
```

// Deprecated
```bash
dnf install -y oraclelinux-developer-release-el8
dnf config-manager --set-enabled ol8_developer
cd /home
curl https://yum.oracle.com/repo/OracleLinux/OL8/developer/x86_64/getPackage/oracle-database-preinstall-23c-1.0-1.el8.x86_64.rpm --output orac
le-database-preinstall-23c-1.0-1.el8.x86_64.rpm

dnf -y install oracle-database-preinstall-23c
dnf install -y oracle-database-preinstall-23c-1.0-1.el8.x86_64.rpm

Access the Oracle Database Free software download page:

https://www.oracle.com/database/technologies/free-downloads.html

Download the oracle-database-free-23c-1.0-1.el8.x86_64.rpm RPM file required for performing an RPM-based installation to a directory of your choice.

Install the database software.


Copy
dnf -y localinstall oracle-database-free-23c-1.0-1.el8.x86_64.rpm
Note:Review the RPM log files to determine the system configuration changes. For example, review /var/log/oracle-database-preinstall-23c/results/orakernel.log.
The installation of the Oracle Database software is now complete.

dnf module install -y container-tools:ol8
dnf config-manager --set-enabled ol8_developer
dnf install -y oracle-database-preinstall-23c
curl -JLO https://download.oracle.com/otn-pub/otn_software/db-free/oracle-database-free-23c-1.0-1.el8.x86_64.rpm
dnf localinstall -y  oracle-database-free-23c-1.0-1.el8.x86_64.rpm
```

3. Configurar o Oracle Database

```bash
cat /etc/sysconfig/oracle-free–23c.conf
/etc/init.d/oracle-free-23c configure
export ORACLE_SID=FREE
export ORAENV_ASK=NO
. /opt/oracle/product/23c/dbhomeFree/bin/oraenv
sqlplus / as sysdba
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

### Comandos do docker

- Mostrar os volumes de um container:

```bash 
docker inspect -f '{{ .Mounts }}' containerid
docker inspect -f '{{json  .Mounts }}' containerid | python3 -m json.tool
```

- Excluir volumes não utilizados:

```bash
docker volume prune
```

- Executar linha de comando dentro de container:

```bash
docker exec -it 8412da7b9bb5 /bin/sh
```

## Referências

- [Instruções de instalação](https://forum.armbian.com/topic/27825-proper-way-to-install-armbian-linux-on-h96-pro-plus-s912-or-equivalent-cli-ver/);
- [Atualização do Python](https://computingforgeeks.com/how-to-install-python-on-debian-linux/).
- [Oracle](https://mikedietrichde.com/2023/07/05/oracle-database-19c-on-arm-platform-is-available/)
- [SWAP](https://phoenixnap.com/kb/swap-partition)
