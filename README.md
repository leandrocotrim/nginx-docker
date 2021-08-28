# Servidor NGINX com docker

> Criando arquivo docker-compose.yml
```yml
version: '3'

services:  
  nginx_service: # nome do serviço
    image: nginx:1.21.1-alpine # nome da imagem
    volumes:    
      - ./volume_html:/usr/share/nginx/html # copia a pasta para dentro do container
    container_name: nginx_container # nome do container
    ports:
      - "443:80" # porta espelhada para a 443 do host
```

```script
# Estou usando uma instancia EC2 Debian, já instalei docker e docker-compose na instancia
```

> Subindo o conteudo para a aws
```shell
  > cd deploy

  > scp -r -i *******.pem deploy admin@*******.compute.amazonaws.com:~
```

> Subindo o container no shell da EC2
```shell
> docker-compose up -d
```

> Verificando o NGINX
```shell
> curl localhost:443
```

> Acessando seu IPv4 público vc terá as seguintes telas

![home.PNG](https://github.com/leandrocotrim/nginx-docker/blob/main/home.PNG)            

![page2.PNG](https://github.com/leandrocotrim/nginx-docker/blob/main/page2.PNG)

![page3.PNG](https://github.com/leandrocotrim/nginx-docker/blob/main/page3.PNG)


# Proxy reverse e balance

> Adicione as linha abaixo no arquivo `docker-compose` para criar 2 serviços
```yml
  nginx_service2:
    image: nginx:1.21.1-alpine
    volumes:    
      - ./volume_service/index_service2.html:/usr/share/nginx/html/index.html # copia o arquivo para o container
    container_name: nginx_container2
    ports:
      - "80" # não espelha porta no host

  nginx_service3:
    image: nginx:1.21.1-alpine
    volumes:    
      - ./volume_service/index_service3.html:/usr/share/nginx/html/index.html # copia o arquivo para o container
    container_name: nginx_container3
    ports:
      - "80" # não espelha porta no host
```

> No terminal, podemos pegar o arquivo `default.conf`
```shell
deploy> sudo docker cp nginx_container:/etc/nginx/conf.d/default.conf .
deploy> ls
deploy> cat default.conf
```

> No terminal, podemos pegar o conteúdo do arquivo `default.conf`
```shell
deploy> sudo docker exec -it nginx_container apk add bash
deploy> sudo docker exec -it nginx_container bash

bash-5.1# cat /etc/nginx/conf.d/default.conf 
```

> Esse conteúdo está no arquivo `volume_service\default.conf` já modificado

```nginx
upstream services_balance {
    server nginx_container2;
    server nginx_container3;
}

server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    # location / {
    #     root   /usr/share/nginx/html;
    #     index  index.html index.htm;
    # }

    location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|zip|tgz|gz|rar|bz2|doc|xls|exe|pdf|ppt|txt|tar|mid|midi|wav|bmp|rtf|js|html)$ {
        root    /usr/share/nginx/html;        
        expires 30d;
    }

    location / {
        proxy_pass http://services_balance;        
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

```

> Adicionando a ultima linha no volume do nginx principal, para passar o arquivo de configuração
```yml
image: nginx:1.21.1-alpine
volumes:    
  # ...
  - ./volume_service/default.conf:/etc/nginx/conf.d/default.conf # copia arquivo de configuração para o container
```

> Alterando todos os `htmls` na pasta `volume_html`

```html
  <!-- Apagar -->
  <!-- <li><a href="/">Home</a></li> -->

  <!-- Adicionar -->
  <li><a href="/">Proxy reverso com balance</a></li>
  <li><a href="/index.html">Home</a></li>
```
Pronto, suba os arquivos para o EC2
- Acessando a página incial termos um novo link adicionado
- Ao Clicar nele será exibido uma mensagem "HTML Node - Service 2" ou "HTML Node - Service 3", ao atualizar fica variando de um serviço para o outro, caso não funcione, aperte `ctrl+shit+r`.

## Observações
- Os volumes encontram-se nas pastas `volume_html` e `volume_service`

- Para rodar o `scp` no windows é necessário instalar o `Cygwin`, pode ser baixado em [https://cygwin.com/install.html](https://cygwin.com/install.html)

## Referencias
- devcontent.com.br
- docs.docker.com
