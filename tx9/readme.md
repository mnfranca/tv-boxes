# TV Box TX 9

## Especificações originais

- Modelo: TX9
- Processador: Quad-Core 64 Bits - CPU S905X Cortex ARM A53
- Memória: 2GB DDR3 / 16GB Flash
- Sistema Operacional: Android 8.1
- Saída de Vídeo: Hdmi/Av
- Saída de Áudio: Hdmi/Av
- Conectividade: Porta de rede Rj-45 10/100 ou Wi-Fi 802.11 b/h/n
- Usb: 2 portas Usb 2.0
- Interfaces: Porta Hdmi, Porta Rj-45, Spdif, 2x portas Usb, Slot para cartão Sd e plug de energia.
- Fonte de Alimentação: 5V 2A
- Cor: Preto
- Garantia: 30 dias
- Comprimento: 17 cm
- Largura: 12 cm
- Altura: 7 cm

## Especificações da placa

- Modelo: TX3 Mini
- Processador: Amlogic S905W

## Instalando o Linux

- Gravar a imagem [Armbian_20.10_Arm-64_buster_current_5.9.0.img.xz](https://www.dropbox.com/scl/fi/ap1c72unbuypxl3249c6c/Armbian_20.10_Arm-64_buster_current_5.9.0.img.xz?rlkey=buie7sfec63wnj3x819fygnzx&dl=0) no SDCard usando o aplicativo **balenaEtcher**;

- No SDCard, editar o arquivo **extlinux.conf** e **extlinux.conf-menu**, descomentando as linhas para configurações
para TX3 Mini;

```
FDT /dtb/amlogic/meson-gxl-s905w-tx3-mini.dtb
APPEND root=LABEL=ROOTFS rootflags=data=writeback rw console=ttyAML0,115200n8 console=tty0 no_console_suspend consoleblank=0 fsck.fix=yes fsck.repair=yes net.ifnames=0
```

- Copiar o arquivo **u-boot-s905x-s912** para **uboot.ext**;

- Remover o SDCard com segurança, inserir na TV-Box e ligá-la normalmente na exergia;

- Definir a senha do usuário **root** para **yoda1234**;

- Criar o usuário **user** com senha **yoda1234**;

- Executar o seguinte comando:
  
```bash
./install-aml.sh
```

- Remover o SDCard e reiniciar o dispositivo.

## Definir IP estático:

```bash
sudo nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
address 192.168.100.13
netmask 255.255.255.0
gateway 192.168.100.1
dns-nameservers 8.8.8.8 8.8.4.4
```

```bash
sudo reboot
```

## Definir o timezone

```bash
timedatectl list-timezones | grep Campo_Grande
sudo timedatectl set-timezone America/Campo_Grande
timedatectl
```

## Comandos para mostrando a versão do Linux

```bash
cat /etc/os-release
lsb_release -a
hostnamectl
uname -
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

## Instalando o Home Assistant

- Logue com usuário root;

- Instale a seguinte dependência:

```bash
sudo apt install systemd-journal-remote -y
sudo apt install systemd-resolved -y
```

- Reinicie o sistema;

- Instale o Home Assistant:

```bash
curl -sL https://raw.githubusercontent.com/leofig-rj/Hassio-Tanix-TX3/master/script/hassio_tanix_tx3.sh | bash -s
```

- Quando surgir a pergunta **Select machine type**, selecionar: ***qemuarm-64***.

```bash
sudo reboot
```

## Instalando o Samba

- Instale o Samba com o comando **armbian-config**;

- Edite o arquivo **/etc/samba/smb.conf** e crie o compartilhamento a seguir:

```text
[homeassistant]
         comment = Home Assistant
         path = /usr/share/hassio/homeassistant
         writable = yes
         public = no
         valid users = marcelo
         force create mode = 0644
```

- Permita acesso externo à pasta do Home Assistant com o seguinte comando:

```bash
sudo chmod 777 -R /usr/share/hassio/homeassistant
```

- Crie o usuário no samba, com a mesma senha do Windows, conforme comando a seguir:

```bash
sudo smbpasswd -a marcelo
```

- Ative o serviço do Samba:

```bash
sudo systemctl enable --now smb
```

- Reinicie o sistema.

## Instalando o HACS

- Execute o comando a seguir:

```bash
wget -O - https://get.hacs.xyz | bash -
```

## Unbrick

- [Unbrick 1](./unbrick/readme.md)
- [Unbrick 2](./unbrick-2/readme.md)

## Comandos diversos do Linux

- Mostrar o uso de disco

```bash
df -h /
```

- Limpar o cache do apt

```bash
sudo apt-get autoclean
```