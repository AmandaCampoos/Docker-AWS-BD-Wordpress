# Docker
Documentação para inicializar o Docker.





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

<h1> Executando nosso primeiro Contêiner </h1>

 
