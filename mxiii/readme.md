# TV Box MXIII 4K

## Especificações originais

- Modelo: MXIII 4K
- CPU: RK-3229-BOX V4

<img src="./motherboard-1.jpg" height="400">

<img src="./motherboard-2.jpg" height="400">

## Instalação do Linux

### Sites de referência

1. [Armbian](https://www.armbian.com/);
2. [Download](https://www.armbian.com/download/?device_support=Supported);
3. [RK322X](https://forum.armbian.com/topic/12656-csc-armbian-for-rk322x-tv-boxes/) (compatível com MXIII 4K).

### Preparando a instalação do Linux

1. Utilizar a imagem do utilitário [Multitool](https://www.dropbox.com/scl/fi/5hobx8t6v74uqrkcdd0mw/multitool.img.xz?rlkey=5iv2n239cdiqk03i8zbbifyi3&dl=0);
2. Utilizar a imagem do linux [Armbian_22.02.0-trunk_Rk322x-box_bullseye_legacy_4.4.194_minimal.img.xz](https://www.dropbox.com/scl/fi/z7ro4gyenqcwc78ggi6jo/Armbian_22.02.0-trunk_Rk322x-box_bullseye_legacy_4.4.194_minimal.img.xz?rlkey=316lcbd9wo6xo2pbt7yg8fai8&dl=0);
3. Gravar a imagem do Multitool em um cartão de memória usando o aplicativo [balenaEtcher](https://www.dropbox.com/s/airlf91bq0633wb/balenaEtcher-Setup-1.7.9.zip?dl=0);
4. Copiar a imagem do linux na pasta **images** do cartão de memória;

### Instalando o Linux

1. Inserir o cartão de memória na TV Box;
2. Conectar um teclado USB na TV Box. Utilizar uma das portas USB que ficam perto da entrada do cartão de memória;
3. Conectar um monitor na TV Box;
4. Conectar a TV Box na energia;
5. O led azul na frente da TV Box deve piscar;
6. A interface do Multitool deve aparecer no monitor (talvez seja necessário gravar as imagens no cartão de memória mais de uma vez!);
7. Instalar a imagem na TV Box usando a opção "Install Armbian via steP-nand;
8. Desligar a TV Box;
9. Remover o cartão de memória.

### Configurando o Linux

#### Mostrando a versão do Linux

```bash
lsb_release -d
```

#### Atualizando o sistema

```bash
apt update
apt upgrade
apt install armbian-config
```

#### Definindo o IP estático

1. Definir o IP como **192.168.100.16** e o nome do host como **home-assistant-vader** usando o comando **armbian-config**;
2. Reiniciar a TV Box.

#### Definindo o timezone

```bash
timedatectl
timedatectl list-timezones | grep Campo_Grande
timedatectl set-timezone America/Campo_Grande
```

### Instalando o Docker

- Instale o Docker com o comando **armbian-config**.

#### Ajustando funcionamento do cgroup

1. Editar o arquivo **/boot/boot.cmd**

2. Editar o seguinte trecho:

```
if test "${docker_optimizations}" = "on"; then
        setenv bootargs "${bootargs} cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory swapaccount=1 systemd.unified_cgroup_hierarchy=0";
fi
```

3. Executar o seguinte comando:

```bash
mkimage -C none -A arm -T script -d /boot/boot.cmd /boot/boot.scr
```

### Instalando Home Assistant Container

```bash
apt install apparmor jq wget curl udisks2 libglib2.0-bin network-manager dbus lsb-release systemd-journal-remote systemd-resolved -y
```

```bash
docker run -d --name homeassistant --privileged --restart=unless-stopped -e TZ=America/Campo_Grande -v /home/homeassistant:/config --network=host ghcr.io/home-assistant/home-assistant:stable
```

### Instalando o Samba

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
sudo service smbd restart
```

- Reinicie o sistema.
