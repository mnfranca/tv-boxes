# Instalando Ubuntu na TV Box H96 Pro+

## Preparação

- Gravar a imagem [Ubuntu_server_Tv_Box.img.xz](https://www.dropbox.com/s/945ixuwos6omxmt/Ubuntu_server_Tv_Box.img.xz?dl=0) no SDCard usando o aplicativo [balenaEtcher](https://www.dropbox.com/s/airlf91bq0633wb/balenaEtcher-Setup-1.7.9.zip?dl=0);
- Inicializar a TV Box com o SDCard inserido;
- Login inicial:
	- Usuário: root
	- Senha 1234
- Executar o seguinte comando:
  
```bash
./install.sh
```

## Definir IP estático:

```bash
sudo nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
address 192.168.100.5
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

## Atualizando Ubuntu da versão 16.04 para 18.04

```bash
sudo apt update 
sudo apt upgrade --fix-missing
sudo apt --with-new-pkgs upgrade
sudo apt install ubuntu-release-upgrader-core
```

Executar o código a seguir como root:

```bash
cd /etc/apt/sources.list.d/
for i in *.list; do
  mv $i ${i}.disabled
done
```

```bash
sudo apt clean
sudo apt autoclean
sudo do-release-upgrade
sudo reboot
lsb_release -a
```

## Atualizando Ubuntu da versão 18.04 para 20.04

```bash
sudo do-release-upgrade
sudo reboot
lsb_release -a
```

## Atualizando Ubuntu da versão 20.04 para 22.04

```bash
sudo do-release-upgrade
sudo reboot
lsb_release -a
```

### Caso o comando abaixo não puder ser executado com sucesso

Testar o acesso ao servidor Ubuntu:

```shell
ping us.archive.ubuntu.com
```

Caso o comando acima não obtenha sucesso, executar os seguintes comandos:
```shell
echo nameserver 8.8.8.8 | sudo tee /etc/resolv.conf
sudo apt install resolvconf
sudo systemctl start resolvconf.service
sudo systemctl enable resolvconf.service
sudo nano /etc/resolvconf/resolv.conf.d/head
nameserver 8.8.8.8
sudo systemctl restart resolvconf.service
```

### Caso o comando **sudo apt upgrade --fix-missing** não funcione

Executar os comandos:

```bash
cd /var/lib/dpkg/info
sudo rm usrmerge.*
sudo apt-get -f install
```