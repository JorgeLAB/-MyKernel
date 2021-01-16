# Docker

[Docker Documentation][docker_documentation]

## O que é o container?

O container é um binário que tem tudo que sua aplicação necessita, o container não possui kernel. O kernel da máquina local que é compartilhado por esses containers. O container é a emulação de sua aplicação e não da máquina como um todo.

Em uma máquina física temos uma estrutura de poucas camada kernel -> SO -> Aplicação. Máquina Virtual : Server -> SO -> Máquina virtual -> Todo uma estrutura nova de sistema Operacional. Container: temos o servidor -> SO -> Engine  do Docker -> por fim a aplicação.

Vantagens:

- Portabilidade.
- Emular a aplicação e micro-serviços.

ch-root: foi o primeiro emulador de aplicação. 

## O que é o Docker?

2008: Houve o começo da empresa.

## Camadas e o Copy-on-Write

"Você tem um livro e quando você vai escrever o livro é gerado uma cópia, você não está editando a camada original mas a sua cópia."

## Docker Internals

Componentes do kernel linux que o docker faz uso:

- selinux
- cgroups: É responsável pela liberação de recursos de hardware para os containers.
- namespaces: principal responsável por separar o ambiente de cada container, evitando interferência um com o outro. ( isolamento de árvores de processo)
  - PID Namespace - Cada container tem sua própria árvore de identificação de processo.
  - net namespace - Isolamento de rede e responsável pela comunição.   ( ip -addr)
  - mnt namespace - Isolamento de file system
  - uts namespace - Isolamento do hostname.
  - user namespace - Isolamento de usuário, responsável pelo mapa de usuários. 
- netlink
- netfilter: Um modulo de kernel do **iptables** , quase toda parte de rede.
- apparmor
- capabilities

## Instalando o Docker

[Página oficial do Docker ](https://docs.docker.com/engine/install/)

[Docker Compose](https://docs.docker.com/engine/install/ubuntu/)

[ScreenCast OneBitCode](https://onebitcode.com/dominando-o-docker/)

- Docker só roda em processador 64

- Devemos ter um kernel mínimo de versão 3.8.   

  ```shell
  uname -r
  ```

- Seu kernel deve ter os módulo necessários.

## Como administrar o docker

- Primeiro vamos executar o docker, se a imagem não existir será feito o pull da imagem. Em seguida ele executará o container.

```shell
docker run hello-world
```

- Para visualizamos todos os containers em execução

  ```shell
  docker ps
  ```

- Mostrar todas as imagens presentes, **Imagem de forma grosseira é um container parado**

  ```shell
  docker images
  ```

- Mostrar todos os containers da minha máquina 

  ```shell
  docker ps -a
  ```

- Parâmetros fundamentais, **-ti** o **t** é para que se tenha um terminal e o **i** é para que tenha interatividade com o container.

  ```shell
  docker run -ti ubuntu /bin/bash 
  ```

  **/bin/bash** é para que caia em um bash.

  ```shell
  ip addr
  ```

- Se colocássemos a opção **-v** estaríamos rodando em **background**.

- Fazendo outro exemplo

  ```shell
  docker run -ti centos:7
  ```

- **Control + D** fechamos o shell e com consequência finalizamos o container. 

- Se você quiser sair do container sem matar o shell use **Control+P+Q**. Então volta para o host.

- Para voltar ao container faça o seguinte:

  ```shel
  ip addr
  ```

  ```
  docker attach <Container ID>
  ```

- **Voltou !!!!**

- Usar o create não deixa o container em execução apenas criar

  ```shell
  docker create ubuntu
  ```

- Para paramos o container:

  ```shell
  docker stop <Container ID>
  ```

- Se quisermos startar 

  ```shell
  docker start <Container ID>
  ```

- Se quisermos pausar esse container:

  ```shell
  docker pause <Container ID>
  ```

- Se quisermos descongelá-lo

  ```shell
  docker unpause <Container ID>
  ```

- Para saber o quanto este container está consumindo de recursos

  ```shell
  docker stats <Container ID>
  ```

- Quais os processos que rodando no container

  ```shell
  docker top <Container ID>
  ```

- Mostrar os logs do container

  ```shell
  docker logs <Container ID>
  ```

- Como remover um container pausado

  ```shell
  docker rm <Container ID>
  ```

- Agora se o container estiver rodando devemos fazer o seguinte, devemos forçar a remoção:

  ```shell
  docker rm -f <Container ID>
  ```

## Limitando CPU e Memória 

- Limite de utilização de memória, mostrará a quantidade de memória RAM do Host:

  ```shell
  free -m
  ```

- Mostrará todos os dados de recursos do container :

  ```shell
  ip addr
  ```

  ```shell
  docker inspect <Container ID>
  ```

- Vamos subir um novo container:

  ```shell
  docker run -ti --name teste debian
  ```

  **--name** é um parâmetro para se adicionar um nome ao container criado. 

- Agora vamos ver qual é a memória atribuída a este container:

  ```shell
  docker inspect <Container ID> | grep -i men
  ```

- A linha Memory tem um valor **0**.

- Vamos limitar a memória deste container:

  ```shell
  docker run -ti --memory 512m --name novo_teste debian 
  ```

- Agora vamos inspecionar o memory desse container agora:

  ```shell
  docker inspect <Container ID> | grep -i men
  ```

- Caso queiramos mudar a memória desse container já executado:

  ```shell
  docker ps
  ```

  ```shell
  docker update -m 256m <nome_do_container>
  ```

  ```shell
  docker inspect <Container ID> | grep -i men
  ```

### Agora vamos limitar a CPU

​	Se não limitarmos os recursos de um container, ele poderá usar todo o recurso do host.

​	Vamos criar 3 containers estipulando valores. 1024 , 512  e 512.

- Vamos criar o primeiro:

  ```shell
  docker run -ti --cpu-shares 1024 --name container1 debian
  ```

- Vamos ver se foi feita a criação e configuração. **CPUShares: 1024**

  ```
  docker inspect container1 | grep -i cpu
  ```

- Vamos criar o segundo container:

  ```shell
  docker run -ti --cpu-shares 512 --name container2 debian
  ```

- Vamos criar o terceiro container:

  ```shell
  docker run -ti --cpu-shares 512 --name container3 debian 
  ```

- Agora vamos verificar se eles estão presentes:

  ```shell
  docker ps 
  ```

- Se o container estiver em execução vamos usar o update:

  ```shell
  docker update --cpu-shares 512 container1
  ```

- Podemos verificar se todos ele agora possuem 512 de recurso de cpu:

  ```shell
  docker inspect container1 container2 container3 | grep -i cpu
  ```
  
- Podemos fazer melhor

  ```
  docker inspect --format='{{.RepoTags}}  {{.Config.Image}}' 3a5e53f63281
  ```

## Volumes no Docker

​	É uma forma de colocarmos um diretório de dentro do container fora do container. Ele está montado no container mas não faz parte. * Parecido com um compartilhamento **NFS**. Quando se utiliza um **volume** dentro de um container é basicamente o compartilhamento de um dir do seu host docker com seu container. **Quando vc fizer uma movimentação do seu container o volume não irá junto, o volume é apenas um ponto de montagem, ele persistirá (ficará no mesmo lugar) no host docker. **

- Vamos subir um container passando um volume:

  ```shell
  docker run -ti -v /volume ubuntu /bin/bash
  ```

  Vamos mostrar as partições

  ```shell
  df -h
  ```

- Vamos a este diretório em sua máquina

  ```shell
  cd /
  ```

  ```shell
  ls
  ```

- Podemos verificar que existirá o diretório **/volume**.

- Nossa!!! Montamos ele no nosso container, agora se modificá-lo também estaremos modificando em nosso host Docker

- O nosso volume nada mais é que um compartilhamento entre o nosso container e nosso host Docker. 

  ```shell
  docker ps
  ```

  ```shell
  docker inspect -f {{.Mounts}} <Container_ID>
  ```

- Vamos encontrar o diretório de onde se encontra o nosso volume no host Docker. Assim podemos pegar o caminho gigante que aparece.

  ```shell
  cd <Path>
  ```

- Dentro dele podemos criar qualquer coisa que se será criado no container também

### Vamos fazer um novo volume especificando o caminho

- Vamos criar um diretório:

  ```shell
  mkdir /root/primeiro_dockerfile
  ```

- Agora vamos criar o volume especificando o diretório

  ```shell
  docker run -ti -v /root/primeiro_dockerfile:/volume debian
  ```

- Vamos verificar se foi criado:

  ```shell
  df -h
  ```

- Vamos verificar se o caminho corresponde:

  ```shell
  cd /
  ```

  ```shell
  ls
  ```

  ```shell
  cd volume/
  ```

  ```shell
  touch Dockerfile
  ```

  Por fim criamos este arquivo e vamos sair do container:

  ```
  Control+P+Q
  ```

- Agora vamos no diretório que havíamos especificado na criação:

  ```shell
  cd /root/primeiro_dockerfile:/
  ```

-  Pronto !

### Criação do container DataOnly

​	É um container que nem precisa ser executado, dentro dele teremos volumes que serão compartilhados com outros containers

- Vamos  criar este container:

  ```shell
  docker create -v /data --name dbdados centos
  ```

- Container criado e o volume criado iremos compartilhar com outros containers.

  ```shell
  docker run -d -p 5432:5432 --name pgsql1 --volumes-from dbdados -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql
  ```

  

  ```shell
  docker run -d -p 5432:5432 --name pgsql2 --volumes-from dbdados -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql
  ```

## Este não foi do Jefferson mas estou colocando em destaque

- Este comando é capaz de apagar todas as imagens de uma só vez.

  ```shell
  docker rm $(docker ps -a -q)
  ```

# Intermezzo OneBitCode

​	Vamos dar um intervalo no Jefferson e chamar o Léo. Veja, vamos empregar o docker em uma situação que tenhamos de usar o **Rails**.

- Vamos fazer a criação de um container.

  ```shell
  docker run -it --rm --user "$(id -u):$(id -g)" -v "$PWD":/usr/src/app -w /usr/src/app rails rails new --skip-bundle my_awesome_app
  ```

- **--rm** : apague o container, rodando o comando uma única vez o comando **rails new** 

-  **--user "$(id -u):$(id -g)"**: Compartilhar usuário.

- **-v "$PWD":/urs/src/app -w /usr/src/app** : Criamos um novo volume e especificamos seu caminho no host Docker

- **rails**: o primeiro rails seria o nome do **IMAGEM** 

- Iremos criar um Dockerfile, nada mais é do que um meio que utilizamos para criar nossas próprias imagens. Em outras palavras, ele serve como a receita para construir um container, permitindo definir um ambiente personalizado e próprio para meu projeto pessoal ou empresarial.

  ```
  vim Dockerfile
  ```

- Logo após a criação iremos entrar no volume e inserir esses comando:

  ```dockerfile
  FROM ruby:2.3
   
  RUN mkdir -p /usr/src/app
  WORKDIR /usr/src/app
   
  RUN apt-get update &amp;&amp; apt-get install -y nodejs --no-install-recommends &amp;&amp; rm -rf /var/lib/apt/lists/*
  RUN apt-get update &amp;&amp; apt-get install -y sqlite3 --no-install-recommends &amp;&amp; rm -rf /var/lib/apt/lists/*
   
  COPY Gemfile /usr/src/app/
   
  RUN bundle install
   
  COPY . /usr/src/app
   
  EXPOSE 3000
  CMD puma -C config/puma.rb
  ```

  Explicando 

  - **FROM** : pegue a imagem ( nesse caso **ruby:2.3** )
  - **RUN** : Irá rodar um comando do shell do linux. ( Os próximos RUN são instalações)
  - **WORKDIR** : especifica o diretório onde estaremos instalando as dependências seguintes.
  - **COPY** : Pega um arquivo da pasta local ( host Docker ) e joga no **container**
  - **RUN bundle install** : instala as dependências do Gemfile
  -  **COPY . /usr/src/app** : copia tudo da pasta ( host Docker) subindo para o container.
  - **EXPOSE 3000**: Abrir a porta do container.
  - **CMD** : é o comando de saída.

- Agora iremos criar uma imagem através do **Dockerfile**. ( Construindo uma imagem personalizada )

  ```shell
  docker build -t my_awesome_app .
  ```

- Vamos testar se o server está rodando corretamente

  ```shell
  docker run -v "$PWD":/usr/src/app -p 3000:3000 my_awesome_app
  ```

- Tudo certo 

- Vamos remover os container criados

  ```shell
  docker rm <Container ID>
  ```

### Vamos Criar um outro exemplo <span style="color:red">Não está rodando</span>

- Primeiro iremos cria um projeto rails

  ```shell
  docker run -it --rm --user "$(id -u):$(id -g)" -v "$PWD":/usr/src/app -w /usr/src/app rails rails new --skip-bundle my_awesome_app2 --database=postgresql
  ```

  - Vamos analisar novamente o que está sendo feito:
  - **docker run** : Estamos rodando o docker.
  - **-it**: parâmetro de interatividade com o console.
  - **-v "$PWD":/usr/src/app -w /usr/src/app**: Estamos compartilhando nosso projeto com o container que será rodado.

- Crie um arquivo Dockerfile e configure uma nova imagem para este projeto.

  ```dockerfile
  FROM ruby:2.3
   
  RUN mkdir -p /usr/src/app
  WORKDIR /usr/src/app
   
  RUN apt-get update && apt-get install -y nodejs --no-install-recommends && rm -rf /var/lib/apt/lists/*
  RUN apt-get update && apt-get install -y postgresql-client --no-install-recommends && rm -rf /var/lib/apt/lists/*
   
  COPY Gemfile /usr/src/app/
   
  RUN bundle install
   
  COPY . /usr/src/app
   
  EXPOSE 3000
  CMD puma -C config/puma.rb
  ```

- Vamos fazer o build da imagem ( Construir nossa imagem personalizada ), dentro da pasta do projeto:

  ```shell
  docker build -t my_custom_image_2 .
  ```

- pIremos criar um network para que um container possa acessar o outro. ( uma rede internar do docker que irá conectar nossos containers).

  ```shell
  docker network create my_awesome_network
  ```

- Vamos subir o container com o Postgresql dentro:

  ```shell
  docker run --name db -d --network=my_awesome_network postgres
  ```

  - **--name** : estamos definindo um nome para o container
  - **-d**: daemon, dizendo para rodar em backend.
  - **--network** : especificando nosso container network.
  - **postgres** : acrecentando uma imagem postgres.

- Vamos configurar o **config/database.yml** do nosso projeto para que possamos conectar ao banco.

  ```yml
  default: 
    adapter: postgresql
    encoding: unicode
    pool:<%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>;
    # Apenas acrescentamos o nosso container como Host
    host: db
    # E colocamos o usuário default do postgreSQL
    user: postgres
   
  development:
  ```

- Vamos criar a base de dados

  ```shell
  docker run -v "$PWD":/usr/src/app --network=my_awesome_network my_custom_image_2 rake db:create
  ```

- Vamos

  ```shell
  docker run -v "$PWD":/usr/src/app -p 3000:3000 --network=my_awesome_network my_custom_image_2
  ```

### Usando o Docker Compose para organizar nossos containers

1 - No mesmo projeto que trabalhamos na Parte 3 (O mesmo que está dando errado ) vamos criar um arquivo chamado “docker-compose.yml” e ele vai gerenciar nossos containers para gente. Crie o arquivo e coloque o seguinte conteúdo dentro dele:

```yml
version: '2'
 
services:
  db:
    image: 'postgres:9.5'
    volumes:
      - 'postgres:/var/lib/postgresql/data'
 
  website:
    depends_on:
      - 'db'
    build: .
    ports:
      - '3000:3000'
    volumes:
      - '.:/usr/src/app'
 
volumes:
  postgres:
```

2 - Para fazer o Build de todos os nossos containers basta rodar (dentro do projeto):

```shell
docker-compose build
```

3 - Agora como nós criamos um <span style="color:red">novo container para o PostgreSQL</span> nós precisamos criar nosso banco de dados de novo, porém agora <span style="color:red">vamos usar os comandos do docker-compose </span> para fazer isso de maneira fácil. No console rode:

```shell
docker-compose run web rake db:create
```

4 - Para subir o container rode

```shell
docker-compose up
```

5 - Agando todos os container 

```shell
docker rm $(docker ps -a -q)
```

6 - Apagar todas as imagens docker 

```shell
docker rmi $(docker images -q)
```

### Alguns comandos úteis

​	1 - Criar um container e roda um comando dentro:

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

​	2 - Visualiza os container rodando:

```shell
docker ps
```

​	3 - Visualizando todos os containers:

```shell
docker ps -a 
```

​	4 - Parando um container:

```shell
docker stop id_do_container
```

​	5 - Parando todos os containers que estiverem rodando:

```shell
docker stop $(docker ps -a -q)
```

​	6 - Dando um start em um container parado:

```shell
docker start id_do_container
```

​	7 - Apagando um container:

```powershell
docker rm id_do_container
```

​	8 - Apagando todos os containers:

```shell
docker rm $(docker ps -a -q)
```

​	9 - Executando um comando dentro de um container que está sendo executado:

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

​	10 - Vendo as imagens que você tem instalado:

```shell
docker images
```

​	11 - Apagando uma imagem

```shell
docker rm id_da_imagem
```

​	12 - Fazendo o build das imagens usando o docker compose:

```shell
docker-compose build
```

​	13 - Criando e subindo os containers via Docker Compose:

```shell
docker-compose up
```

​	14 - Parando os containers via docker compose:

```shell
docker-compose stop 
```

​	15 - Executando um comando dentro do container via docker compose:

```shell
docker-compose run nome_do_servico o_comando
```

​	16 - Vendo sua configuração no docker compose 

```shell
docker-compose config
```

​	17 - Vendo os logs dos containers via Docker compose:

```shell
docker-compose logs
```

​	18 - Matando os containers via Docker Compose:

```shell
docker-compose kill
```

​	19 - Escalando seus containers ( Aumentando a quantidade de container para o mesmo service ) via Docker Compose.

```shell
docker-compose scale [SERVICE=NUM...]
```

​	20 - Parando e removendo containers usando o Docker Compose:

```shell
docker-compose down
```

​	21 - Startando os containers:

```shell
docker-compose start
```

[docker_documentation]: https://docs.docker.com/get-started/overview/

