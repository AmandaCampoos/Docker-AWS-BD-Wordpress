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

# AWS 
Configurações na console da aws necessárias para essa atividade.
Primeiro vamos configurar grupos de segurança.
<br/> 
 Instacincias 

80|HTTP| TCP
TCP| SSH | 22
HTTP|TCP| 80	
NFS |	TCP |	2049

RDB
  
3306	| MySQL/Aurora |	
 ----| --- 
ELB

80 |	HTTP	| TCP	 
---| ----| --- 
22	| SSH	 | TCP	
HTTP	|TCP |	80	
HTTPS	| TCP|	443	
ICMP	| Tudo

EFS
2209	| NFS	| TCP	S
---| ----| --- 

RDS

MYSQL/Aurora	|TCP|	3306
---| ----| --- 

### Load balancers
Nome: loadbalancer
- vpc:


# Docker

Ponto adicional para o trabalho utilizar
a instalação via script de Start Instance
(user_data.sh).

```
#!/bin/bash
yum update -y

yum install docker -y
#iniciar
systemctl start docker.service
#iniciar automaticamente
systemctl enable docker.service

#Instalação do docker-compose
curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
#permissao
chmod +x /usr/bin/docker-compose

#instalar o NFS e montar um sistema de arquivos EFS
yum install nfs-utils -y
systemctl start nfs-utils.service
systemctl enable nfs-utils.service
mkdir -p /efs
                                                                 
"user_data.sh" 22L, 671B 
```

Usamos o comando para dar permissão para o arquivo:
```
chmod +x user_data.sh
```
Após, exemplo de pesquisa para ver a versão:

![Captura de tela de 2023-12-28 12-08-31](https://github.com/AmandaCampoos/Docker/assets/138727208/bfd30e0d-8b7d-4647-82ef-83e1863b056c)

Para montar o diretório efs usando o nfs , usaremos o mount DNS do estará disponivél em anexar no seu EFS da aws.
```
mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport=fs-0a87eb8f6b5170cfe.efs.us-east-1.amazonaws.com:/ /efs
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

Arquivos de configurações:

vamos criar um diretório efs dentro desse diretório vamos criar uma arquivo de configuração
```
vim docker-compose.yml
```

esse arquivo vai ter as configuraçoes de inicialização da aplicação com banco de dados e wordpress.
é essa é nossa base

vamos salvar o arquivo usando WQ! para salvar
para executar 
```
docker-compose
```
ou seja mesmo que mão tivemos a imagem criada , com esse comando no docker composer ele fará todo o processo ele sobe toda infraestrutura que a gente definiu no arquivo com isso é possivel verificar o seu localhost e já estará na página do wordpress.
![Captura de tela de 2024-01-08 21-57-36](https://github.com/AmandaCampoos/Docker/assets/138727208/0ac3031c-a40c-4f4f-bea2-827bec7c279d)

use o comando a seguir para executar:
```
docker-compose up -d
```
logo podemos conferir os arquivos de configuração no nosso diretório criado.
![Captura de tela de 2024-01-08 21-58-24](https://github.com/AmandaCampoos/Docker/assets/138727208/e929942e-f3b4-421c-b0a2-a7a48fdaafa5)




Para olhar os programas rodadando na sua máquina
ls -l /run
Para visualizar os grupo existente e para poder adicionar usuarios no Docker sem precisar estar na conta root, Apenas com o comando sudo.
cat /etc/group

Adicionarando o usuario Amanda 
```
root@amanda-desktop:~# sudo gpasswd -a amanda docker
Adicionando usuário amanda ao grupo docker
docker attach (ID_do_conteiner)
