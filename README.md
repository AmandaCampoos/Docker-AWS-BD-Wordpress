# Docker

Documentação para inicializar o Docker
Para inciar vamos instalar o Docker na máquina e vou deixar alguns comandos essenciais no docker.
Para visualizar a instação e ver a versão em sua máquina.

```docker -v
   docker info
```
Para olhar os programas rodadando na sua máquina
 ls -l /run
 Para visualizar os grupo existente e para poder adicionar usuarios no Docker sem precisar estar na conta root, Apenas com o comando sudo.
cat /etc/group

Adicionarando o usuario Amanda 
```
root@amanda-desktop:~# sudo gpasswd -a amanda docker
Adicionando usuário amanda ao grupo docker
```
Para Incializar o container 
```
docker run -it ubuntu /bin/bash
```
com esse comando voce sera logado ao terminal da máquina criada e para sair 
e voltar ao seu terminal usado anteriormente ;

 exit

com esse comando voce tambem encerra o container ele não esta mais em execuçao.


<h1>Configurando o Docker para se comunicar na rede; </h1>
para isso precisamos dar stop no docker 

service docker stop

e com o ip da sua máquina inicializar dessa forma:
```
dockerd -H tcp://192.166.1.6:2375
```
para listar os conteiner execultados:

docker ps -a
sempre que quiser incializar com o conteiner criado
docker start (ID_do_conteiner)
para usar o terminal dele
docker attach (ID_do_conteiner)



Vamos instalar o docker em CentOs (será usado para fazer uma comunitação com a rede e outra VM).
```
yum install -y docker

```
com o docker já instalado e ativo 

digite o camando 
```
export DOCKER_HOST='tcp://192.166.1.6:2375'
```
agora o na máquina Centos é possivel acessar o host da máquina Ubuntu
ou sejá houve a comunicação pela rede.
Para analisarmos melhor 
```
docker info
```
Informaçoes sobre os container e detalhes do Docker configurado. é dessa forma que você configura para expor atravez da rede e para acessar atravéz da rede.
Nesse caso é apenas testes , medidas de segurança deve ser levadas em consideração em um ambiante de produção pois isso disponibiliza o docker em uma porta de rede e permite que qualquer pessoa se conecte a ele.

# Docker Compose


Para instalar o Docker Compose Ubuntu seguindo a documentação oficial.
curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
Para dar permissao de usuario
chmod +x /usr/local/bin/docker-compose
Arquivos de cinfigurações:
vamos criar um diretório mkdir docker-compose , dentro desse diretório vamos criar uma arquivo de configuração
vim docker-compose.yml
esse arquivo vai ter as configuraçoes de inicialização do nosso docker 
difinição de qual será exposta , banco de dados e wordpress, nesse caso será oque será inicializado no nosso conteiner.
é essa é nossa base
```
version: '3.4'
services:
  db:
    image: mysql:5.7.22
    command: mysqld --default_authentication_plugin=mysql_native_password
    ports:
      - "3308:3306"      
  wordpress:
    image: wordpress:latest
    ports:
      - 80:80
    depends_on:
      - db
```
agora vamos definir varáveis de ambientes, volumes
```
services:
  db:
    image: mysql:5.7.22
    command: mysqld --default_authentication_plugin=mysql_native_password
    environment:
      TZ: America/Sao_Paulo
      MYSQL_ROOT_PASSWORD: docker
      MYSQL_USER: docker
      MYSQL_PASSWORD: docker
      MYSQL_DATABASE: wordpress
    ports:
      - "3308:3306"
  wordpress:
    image: wordpress:latest
    ports:
      - 80:80
    volumes:
      - ./config/php.conf.uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
      - ./wp-app:/var/www/html
    environment:
      TZ: America/Sao_Paulo
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: docker
    depends_on:
      - db
```
o mesmo arquivo vamos modificar e acrescenatar as configuraçoes de rede:
```
version: '3.4'

services:
  db:
    image: mysql:5.7.22
    command: mysqld --default_authentication_plugin=mysql_native_password
    environment:
      TZ: America/Sao_Paulo
      MYSQL_ROOT_PASSWORD: docker
      MYSQL_USER: docker
      MYSQL_PASSWORD: docker
      MYSQL_DATABASE: wordpress
    ports:
      - "3308:3306"
    networks:
      - wordpress-network
  wordpress:
    image: wordpress:latest
    ports:
      - 80:80
    volumes:
      - ./config/php.conf.uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
      - ./wp-app:/var/www/html
    environment:
      TZ: America/Sao_Paulo
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: docker
    depends_on:
      - db
    networks:
      - wordpress-network
networks:
    wordpress-network:
      driver: bridge
```


 
