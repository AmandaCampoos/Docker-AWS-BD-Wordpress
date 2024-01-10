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


#### Para montar o diretório efs usando o nfs , usaremos o mount com o DNS do estará disponivél em anexar no seu EFS da aws.
vim    /etc/fstab/

![Captura de tela de 2024-01-10 08-51-54](https://github.com/AmandaCampoos/Docker/assets/138727208/17fb14d0-f9fe-409f-aa3e-6a2d8f21ad45)

```
mount -a
```
Para montar a partição na raiz do sistema , assim o sistema pode ser reiniciado ele continuara de forma estática.

# Docker 

### Docker Compose

Essa ferramenta é usada para unir dois ou mais contêines para subir uma aplicação.
O docker compose basicamente segue um arquivo de configuração , que especifica oque ele tem que fazer e inicia automaticamente as imagens que lhe foi especificado no documento, tornando mais simples na hora de usar o docker para subir aplicações.

###### Arquivos de configurações:

vamos criar um diretório efs dentro desse diretório vamos criar uma arquivo de configuração:

```
vim docker-compose.yml
```

Esse arquivo vai ter as configuraçoes de inicialização da aplicação com banco de dados e wordpress.
Ou seja mesmo que mão tivemos a imagem criada , com esse comando no docker composer ele fará todo o processo ele sobe toda infraestrutura que a gente definiu no arquivo com isso é possivel verificar o seu localhost e já estará na página do wordpress.

![Captura de tela de 2024-01-08 21-57-36](https://github.com/AmandaCampoos/Docker/assets/138727208/0ac3031c-a40c-4f4f-bea2-827bec7c279d)

use o comando a seguir para executar:
```
docker-compose up -d
```
logo podemos conferir os arquivos de configuração no nosso diretório criado.

![Captura de tela de 2024-01-08 21-58-24](https://github.com/AmandaCampoos/Docker/assets/138727208/e929942e-f3b4-421c-b0a2-a7a48fdaafa5)

# AWS 
Configurações na console da aws necessárias para essa atividade.
Primeiro vamos configurar grupos de segurança.
<br/> 
 Instacincias 

80 | HTTP | TCP
---| ----| --- 
TCP | SSH | 22
HTTP | TCP | 80	
NFS |	TCP |	2049

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
Nome: loadbalancer.
- vpc: a mesma usada nas instancias de toda a atividade.
- zonas disponivéis: us-east-1b (use1-az1) e us-east-1a (use1-az6).
- Tipo de endereço IP:IPv4.
- Receptores e regras : Criar um grupo de destino e alocar sua instância.
- Após a realizão da conecão com a instancia é possivél usar o DNS publíco para acessar através da rede a nossa aplicação.
  
![Captura de tela de 2024-01-10 09-03-04](https://github.com/AmandaCampoos/Docker/assets/138727208/e78ceee4-ec85-491e-b395-c9635699ae37)


###### Grupo de destino para o load balancer

- Escolher tipo: Instancia.
- VPC : Usada durante a atividade.
- Porta: 80
- Escolher a instancia de destino.

  ![Captura de tela de 2024-01-10 09-08-32](https://github.com/AmandaCampoos/Docker/assets/138727208/15842a80-7897-4012-99bd-94ca4c6a5a21)


### RDS

![Captura de tela de 2024-01-10 09-12-42](https://github.com/AmandaCampoos/Docker/assets/138727208/3c163b94-6e2f-4b36-b655-0ff7f35467d5)

Para a craição:
- Escolher o banco de dados.
- Dar um nome para o banco.
- Nome do usuário e senha.
- Escolher a vpc usada durante a tividade.
- O grupo de segurança será o mesmo da instãncia e um defult com a porta 3306 caso banco seja o Mysql.
- As configurações desse banco de dados foram usada no docker-composer para que ele pudesse se comunicar com o wordpress.
  
### Auto Scaling
- Escolher um nome
- Modelo de execuçãp: O arquivo de configuração da aplicação da isntancia.
- Escolher a VPC usada durante a atividade.
- Escolher zonas de disponibilidade e sub-redes.
  
![Captura de tela de 2024-01-10 09-28-41](https://github.com/AmandaCampoos/Docker/assets/138727208/7648235c-5e5d-41e2-991c-d5927aae5b69)

### Testando o DNS do load Balancer


![Captura de tela de 2024-01-10 09-45-10](https://github.com/AmandaCampoos/Docker/assets/138727208/cfa4d01b-d5ac-4b1c-bb59-dd28aa0ef022)

Cole no navegador

![Captura de tela de 2024-01-10 09-46-26](https://github.com/AmandaCampoos/Docker/assets/138727208/8d96fd54-7eb3-4626-aa2e-e83b74482b2e)



