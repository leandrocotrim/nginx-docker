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

![home.PNG](https://github.com/leandrocotrim/nginx-docker/home.PNG)

![page2.PNG](https://github.com/leandrocotrim/nginx-docker/page2.PNG)

![page3.PNG](https://github.com/leandrocotrim/nginx-docker/page3.PNG)

## Observações

Para rodar o `scp` no windows é necessário instalar o `Cygwin`, pode ser baixado em [https://cygwin.com/install.html](https://cygwin.com/install.html)


### Referencias
- devcontent.com.br
- docs.docker.com

