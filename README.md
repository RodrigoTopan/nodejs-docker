## DOCKER
Primeiro passo -> analisar os requerimentos que nossa aplicação necessita

Mapear o que nossa aplicação tem que fazer
Passos
    1.Instalar node.js
    2.Instalar o typescript
    3.Instalar dependências
    4.Compilar o projeto
    5.Rodar o projeto em uma porta específica

### OBJETIVO DO DOCKER
-Pequenas aplicações como sistemas operacionais (com suas próprias dependências, seu próprio linux)
-Compartilhar informações entre containers, compartilhar rede e alguns casos recursos

-Precisamos de um arquivo de receita, que define quem ou como será executado ou instalado em nosso objeto
(quem segue os steps)->Dockerfile

-Após a criação do nosso Dockerfile, precisamos compilar nossa imagem
`docker build -t heroes-api .`

- `-t` -> nome da imagem
- `.` -> caminho de onde está o dockerfile

- Após construir nossa imagem, precisamos rodar de fato nossa app

- `docker run -p 3000:3000 heroes-api`
- `p`
- 1° parâmetro-> Porta que meu sistema vai visualizar
- 2° parâmetro-> Porta de dentro do container
-`e`
-Baixamos a imagem e colocamos para rodar o MongoDB
`docker run -p 27017:27017 -d --name mongodb mongo:4`
`d` - Roda em segundo plano

- Importante: Se você estiver com umagem com o memo vai dar aerro

- Para listar as imagens:
`docker image list`
-Para listar os contaieners:
`docker ps`
- Para listar os containers e os containers parados
`docker ps -a`

- Para listar os containers e os containers parados:
`docker ps -aq`

- Para para um container
`docker stop ID`

- Precisamos linkar nosso MongoDB à nossa aplicação
`--link`

- Passamos o MONGO_URL com o nome do nosso container, internamente ele vai converter essa string para o IP interno do MongoDB

    variavéis de ambiente do projeto. O que fica geralmente no .ENV

```shell
docker run -p 3000:4000 \
-e PORT=4000 \
-e MONGO_URL=mongodb \
--link mongodb:mongodb \
heroes-api
```

- Podemos entrar no nosso container e visualizar seu código
1° `docker ps` e pegar o ID
2° `docker exec -it ID /bin/sh`
    `exec -it` -> executa um comando específico e entra no container

## Subir nossa imagem para o Docker Hub
1º passo -> Criar conta no dockerhub.com
2º passo -> logar no terminal

`docker login`

3º passo -> compilar nossa imagem ou tag-a-la
1. Colocamos o nomde de usuário do docker hub, o nome do projeto
2. falamos a versão da nossa imagem
`docker build -t rodrigotopan/nomedaimagem:v1 .`

# Podemos tag-a-la para uma nova imagem
`docker tag existente novoNome`

#Criamos uma cópia da imagem com outro nom
#Não precisamos recompilar
`docker tag heroes-api rodrigotopan/heroes-api:v1`

#DEPLOY
`docker push rodrigotopan/heroes-api:v1`

#REMOVENDO IMAGEM LOCALMENTE
`docker rmi rodrigotopan/heroes-api:v1`
`docker rmi heroes-api`


#Não encontra local, baixa do dockerhub
```shell
docker run -p 3000:4000 \
-e PORT=4000 \
-e MONGO_URL=mongodb \
--link mongodb:mongodb \
rodrigotopan/heroes-api:v1
```

## ------------

Para evitar rodar comandos bash o tempo inteiro, temos uma forma automatizada de agregar serviços.
Agrupar o mongodb, apis, eventos, serviços, tudo em um lugar, para que ao digitar um unico comando, subir nossa infra completa
Este arquivo é chamado `docker-compose.yml`

- Após a criação e serviços no docker-compose
-`docker-compose up`-> para levantar todos os serviços
-`docker-compose up --build`-> para levantar e reconstruir as imagens
imagens
-`docker-compose up -d heroes-api` -> paa levantar apenas o heroes-api e rodar em segundo plano
-`docker-compose down` ->  para matar todos os serviços e volumes e networks

- Matar o mongodb existente
`docker stop id` 

- Para não perder as informações de dados de aplicações, criamos volumes. Informamos a pasta local que será espelhada para a pasta no container.

- Para começar a enxergar é necessário limpar as aplicações com
`docker-compose down` e `docker-compose up`

```yaml
    - volumes:
       ./db:/data/db
```

- Podemos também criar volumes virtuais usando a aba volumes do docker-compose

```yml
    volumes:
        mongo_data: {}
```

- Desta forma, criamos um disco virtual 

```yml
    - volumes: mongo_data:/data/db
```

- Para visualizar onde nosso volume foi criado
1.`docker volume ls`
2.`docker volume inspect ID` 


Para ganhar um painel bonitão, colocamos uma interface, para visualizar nossas instâncias do docker
`PORTAINER`

ON dockerhub:
https://hub.docker.com/r/rodrigotopan/heroes-api/


###RODANDO

###Com dockerhub

```shell
    docker run -p 27017:27017 -d --name mongodb mongo:4
```

```shell
docker run -p 3000:4000 \
-e PORT=4000 \
-e MONGO_URL=mongodb \
--link mongodb:mongodb \
rodrigotopan/heroes-api:v1
```

###Com docker compose
`docker-compose-up`
- ir para `localhost:9000` e visualizar os containers