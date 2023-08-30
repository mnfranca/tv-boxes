# TV Box MXIII 4K

## Especificações originais

- Modelo: MXIII 4K
- CPU: RK-3229-BOX V4

<img src="./motherboard-1.jpg" height="400">

<img src="./motherboard-2.jpg" height="400">

## Instalando o Linux

### Sites de referência

1. [Armbian](https://www.armbian.com/);
2. [Download](https://www.armbian.com/download/?device_support=Supported);
3. [RK322X](https://forum.armbian.com/topic/12656-csc-armbian-for-rk322x-tv-boxes/) (compatível com MXIII 4K).

### Preparando a instalação do Linux

1. Utilizar a imagem do utilitário [Multitool](https://www.dropbox.com/scl/fi/5hobx8t6v74uqrkcdd0mw/multitool.img.xz?rlkey=5iv2n239cdiqk03i8zbbifyi3&dl=0);
2. Utilizar a imagem do linux [Armbian_23.08.0-trunk_Rk322x-box_bookworm_current_6.1.39_minimal.img.xz](https://www.dropbox.com/s/9nszxtz1jrumsjf/Armbian_23.08.0-trunk_Rk322x-box_bookworm_current_6.1.39_minimal.img.xz?dl=0);
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

#### Atualizando o sistema

```bash
apt update
apt upgrade
```

#### Definindo o timezone

```bash
timedatectl list-timezones | grep Campo_Grande
timedatectl set-timezone America/Campo_Grande
timedatectl
```

#### Definindo o IP estático

1. Definir IP fixo usando o comando **armbian-config** (192.168.100.14);
2. Reiniciar a TV Box.