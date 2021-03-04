# DOCKER + LUMEN
Os pontos importantes adquirido no curso de Laravel/Lumen estão registrados no readme para eventuais consultas.

## DOCKER-COMPOSE -| comentários dos principais comandos
```
# Cria uma rede para comunicação com os container
networks:
	nome-da-rede:

# Usando a rede no container
nginx:
	networks:
		- nome-da-rede

# Método 1: Indicando a imagem do Nginx para criar o webserver. Será baixada direto do repositório do [Docker Hub](http://hub.docker.com)
nginx:
	image: nginx:stable-alpine
# Método 2: Indicando um arquivo do Dockerfile
nginx:
	build:
		context: .
		dockerfile: Dockerfile

# Indicando que o webserver Nginx dependência entre container. Atenção ao comando, ele foi depreciado nas versões superiores a 3.2 do docker-compose
nginx:
	depends_on:
		- php
		- mysql

# Expor portas para comunicação do host/container.
nginx:
	ports:
		- "8080:80"

# Montar o volume do host/container. Os arquivos do host serão refletidos no container.
nginx:
	volumes:
		- "./app:/var/www/html"

# Nomeando o container
nginx:
	container_name: nginx

# 
```

### Configuração do Nginx para processar arquivos PHP-FPM
> Esse arquivo deve substituir o arquivo original do nginx. Faça isso através do mapeamento do volume, incluindo: `./nginx/default.conf:/etc/nginx/conf.d/default.conf`
```
# default.conf
server {
    listen 80;
    index index.php index.html;
    server_name _;
    root /var/www/html/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```