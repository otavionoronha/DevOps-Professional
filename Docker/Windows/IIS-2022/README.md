# Pré-Requisitos

 - Docker Instalado no Windows
 - Acesso a Internet
 - Baixar as informações no github para uma pasta

**Preparando o ambiente**

 - Instalação do Docker Windows
	 - Seguir o [LINK](https://github.com/otavionoronha/DevOps-Professional/wiki/Docker#instalando-o-docker-em-windows) da Wiki
- Download das pasta
	- [LINK](https://github.com/otavionoronha/DevOps-Professional/tree/6a299182937c087d21b89b2ea769d1490418d702/Docker/Windows/IIS-2022)

## Docker
Agora basta rodar o docker build e após rodar o docker run

    cd [diretorio onde está o arquivo baixado]
    docker build -t meu-iis:1.0 .
    docker run --name iis-2022 -d -p 8080:80 meu-iis:1.0
Caso queira em vez de copiar o site para dentro do container utilizar um volume permantente, está comentado dentro do DockerFile a opção, remova o comentário do **VOLUME** e comente o **COPY**

    # Defina o diretório de trabalho
    WORKDIR /inetpub/wwwroot
    COPY ./inetpub/wwwroot/ .
    
    # Crie uma pasta persistente
    #VOLUME C:\\inetpub\\wwwroot