<h1>Atividade - AWS -Docker</h1>
Arquitetura do projeto:

![Captura de tela de 2023-12-27 20-20-09](https://github.com/AmandaCampoos/Docker/assets/138727208/5806f5d8-1f6a-465b-8aa1-2e1bb028e176)


1. instalação e configuração do DOCKER
ou CONTAINERD no host EC2;
Ponto adicional para o trabalho utilizar
a instalação via script de Start Instance
(user_data.sh)
2. Efetuar Deploy de uma aplicação
Wordpress com:
container de aplicação
RDS database Mysql
3. configuração da utilização do serviço
EFS AWS para estáticos do container
de aplicação Wordpress
4. configuração do serviço de Load
Balancer AWS para a aplicação
Wordpress.




# Docker
![awesome-compose](https://github.com/AmandaCampoos/Docker/assets/138727208/89fe560b-57bf-4ee0-ab2b-90aee7f8b23c)


Vamos instalar o docker 
```
yum install -y docker

```
comandos úteis no docker:
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
```
exit
```
com esse comando você tambem encerrar o container ele não esta mais em execução.

Sempre que quiser incializar com o conteiner criado.
```
docker start (ID_do_conteiner)
```
Para usar o terminal dele.
```
docker attach (ID_do_conteiner)
```

# Docker Compose

Essa ferramenta é usada para unir dois ou mais contêines para subir uma aplicação específica. 

Para instalar o Docker Compose Ubuntu seguindo a documentação oficial.
```
curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
Para dar permissao de usuario:
```
chmod +x /usr/local/bin/docker-compose
```
O docker compose basicamente segue um arquivo de configuração , que especificar oque ele tem que fazer e inicia automaticamente as imagens que lhe foi especificado no documento, tornando mais simples na hora de usar o docker para subir aplicações.

Arquivos de cinfigurações:

vamos criar um diretório mkdir docker-compose  dentro desse diretório vamos criar uma arquivo de configuração
```
vim docker-compose.yml
```
esse arquivo vai ter as configuraçoes de inicialização da aplicação com banco de dados e wordpress.
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
vamos salvar o arquivo usando WQ! para salvar
para executar 
```
docker-compose
```
ou seja mesmo que mão tivemos a imagem criada , com esse comando no docker composer ele fará todo o processo ele sobe toda infraestrutura que a gente definiu no arquivo com isso é possivel verificar o seu localhost e já estará na página do wordpress.



 
