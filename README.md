# Servidor NGINX com docker

> Criando arquivo docker-compose.yml
```yml
version: '3'

services:  
  nginx_service: # nome do serviço
    image: nginx:1.17.6-alpine # nome da imagem
    container_name: nginx_container # nome do container
    ports:
      - "8000:80" # porta espelhada para a 8000
```

```script
# Estou usando uma instancia EC2 Debian, já instalei docker e docker-compose
```

> Subindo o conteudo para a aws
```shell
  > cd deploy

  > scp -r -i *******.pem nginx admin@*******.compute.amazonaws.com:~
```

> Subindo o container
```shell
> docker-compose up
```

> Verificando o NGINX
```shell
> curl http://localhost:8000
```

## Observações

Para rodar o `scp` no windows é necessário instalar o `Cygwin`, pode ser baixado em [https://cygwin.com/install.html](https://cygwin.com/install.html)


### Referencias
- devcontent.com.br

