# Instalando Debian na TV Box H96 Pro+

## Preparação

- Gravar a imagem [Armbian_23.02.0_amlogic_s912_bullseye_6.1.2_server_2022.12.31.img](https://www.dropbox.com/s/qkg1hjjs9zoam95/Armbian_23.02.0_amlogic_s912_bullseye_6.1.2_server_2022.12.31.img.gz?dl=0) em um **pen drive** usando o aplicativo [balenaEtcher](https://www.dropbox.com/s/airlf91bq0633wb/balenaEtcher-Setup-1.7.9.zip?dl=0).

## Procedimento de instalação

- Inicializar a TV Box com o **pen drive** inserido;
- Após inicialização, definir uma senha padrão para o usuário root;
- Utilizar a opção **1) bash** como interpretador de comando padrão;
- Ignorar criação de novo usuário com o comando **Ctrl-C**;

## Configurações iniciais

- Extender o espaço utilizado no **pen drive** através do comando:

```shell
armbian-tf
```

- Utilizar o comando **armbian-config** para definir as seguintes configurações:
  - IP estático;
  - Timezone.

## Observações finais

- Na TV Box testada não foi possível transferir o sistema operacional para sua memória interna. Portanto é necessário ter um pen drive dedicado para ser usado como o armazenamento do sistema operacional.

## Referências

[ophub/amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian)