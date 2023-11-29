# Descrição para o Atividade de Linux, AWS e servidores.


### O Desafio consiste em Criar 1 instância EC2 AWS com o sistema operacional Amazon Linux, Configurar o servidor NFS, Subir um apache no servidor - o apache deve estar online e rodando. 
### Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs;
### O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline; O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE; Preparar a execução automatizada do script a cada 5 minutos.


# Para iniciar a instacia EC2 vamos ultizar os recursos:
- Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);
- Criar ou escolher uma VPC existente e associar a instancia.
- Gerar 1 elastic IP e anexar à instância EC2.
- Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP,2049/TCP/UDP, 80/TCP, 443/TCP)Para a liberaçao das portas, cria se um grupo de segurança de entrada e saída. Assim possibilitando o acesso da sua máquina virtual a recursos como o nfs.


 # Para instalação e inicialização nfs:
#### Em algumas distribuição linux o nfs já vem pré instalado nesse caso apenas usamos os comandos:


 ```
 systemctl start nfs-server
 systemctl enable nfs-server

```
#### Caso precise instalar em sua máquina use o seguinte comando:

```
yum install -y nfs-utils

```
Rodando o comando abaixo iremos perceber a porta 2049 está escutando.
```
ss -ln | grep 2049
```
```ruby
udp   UNCONN 0      0                                  0.0.0.0:2049             0.0.0.0:*           
udp   UNCONN 0      0                                     [::]:2049                [::]:*           
tcp   LISTEN 0      64                                 0.0.0.0:2049             0.0.0.0:*           
tcp   LISTEN 0      64                                    [::]:2049                [::]:*
```

##### Agora precisamos abrir uma excessão no firewall para os serviço nfs , para que a máquina cliente consiga se conectar com o meu servidor.

caso não esteja instalado como no meu caso na máquina Linux EC2
instale
```
yum -y instalar firewalld

```
Depois de as permissões necessarias com os comandos:
```

systemctl start firewalld.service
systemctl enable firewalld.service
firewall-cmd --add-service=nfs --permanent
firewall-cmd --reload

```
#### Instalar o Apache será necessário na máquina cliente e sevidor 
segue as instruçoes para configurar o apache em suas instâncias.
```
yum install httpd
```
Para baixar.
```
```
systemctl start httpd
systemctl enable httpd
```
O Apache agora será iniciado automaticamente quando o servidor for inicializado novamente.

Use o comando para ver o status e se ele foi instalado corretamente.
```
systemctl status httpd
```

#### Configuração do nfs 
o diretótio srv é geralmete ultilizado para serviço nesse caso vou escolher para montar um novo diretório para ser compartilhado.
com os comandos:
```
#cd /srv
#mkdir amanda_campos
#ls -l
```
vamos criar um arquivo index.html que será compartilhado
```
cat > index.html
<h1> Hello World!</h1>
Ctrl+d para salvar

Agora o arquivo /Vim/exports precisa ser alterado com os o diretórios,IPs e premissões necesssária para o compartilhamento:
```
vim /etc/exports
```
Dentro do arquivo coloque na ordem o diretório, ip que poderá se conectar ao servidor e a permissão."rw" é a permissão de leitura e gravação.
/srv/amanda_campos 3.239.34.149(rw)




