## Descrição para o Atividade de Linux, AWS e servidores.✅


 O Desafio consiste em Criar 1 instância EC2 AWS com o sistema operacional Amazon Linux, Configurar o servidor NFS, Subir um apache no servidor - o apache deve estar online e rodando. Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs;O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline; O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE; Preparar a execução automatizada do script a cada 5 minutos.


## Para iniciar a instacia EC2 vamos ultizar os recursos:
- Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);
- Criar ou escolher uma VPC existente e associar a instancia.
- Gerar 1 elastic IP e anexar à instância EC2.
- Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP,2049/TCP/UDP, 80/TCP, 443/TCP)Para a liberaçao das portas, cria se um grupo de segurança de entrada e saída. Assim possibilitando o acesso da sua máquina virtual a recursos como o nfs.


 ## Para instalação e inicialização nfs:
#### Em algumas distribuição linux o nfs já vem pré instalado nesse caso apenas usamos os comandos:


 ```
 systemctl start nfs-server
 systemctl enable nfs-server

```
Caso precise instalar em sua máquina use o seguinte comando:

```
yum install -y nfs-utils

```
Rodando o comando abaixo iremos perceber que a porta 2049 está escutando é importante se  atentar a isso para o bom funcionamento da máquina e evitar problemas futuro com seu servidor.
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
systemctl start httpd
systemctl enable httpd
```
O Apache agora será iniciado automaticamente quando o servidor for inicializado.

Use o comando para ver o status e se ele foi instalado corretamente.

```
systemctl status httpd
```
![Captura de tela de 2023-11-29 18-58-35](https://github.com/AmandaCampoos/Desafio/assets/138727208/e22da8b1-9666-4036-baa6-c646a761bd5c)

#### Configuração do nfs 
o diretótio srv é geralmete ultilizado para serviço nesse caso vou escolher para montar um novo diretório para ser compartilhado.
com os comandos:
```
#cd /srv
#mkdir amanda_campos
#ls -l
```
vamos criar um arquivo index.html que será compartilhado

cat > index.html

Agora o arquivo /Vim/exports precisa ser alterado com os o diretórios,IPs e premissões necesssária para o compartilhamento:
```
vim /etc/exports
```
Dentro do arquivo coloque na ordem o diretório, ip que poderá se conectar ao servidor e a permissão."rw" é a permissão de leitura e gravação.
exemplo:
/srv/amanda_campos 3.239.34.149(rw)
:wq!(para sair e salvar)

digite o comando "exportfs -rav" para salvar e mostar as alterações.
neste caso teste o ip é da minha máquina cliente.

#### Configuração nfs na máquina cliente 
Siga as instruçoes de instalação de nfs e apache disponivél mais á cima. Com o nfs e htpptd startado siga em frente para que funcione corretamente a aplicação.
Optei por usar o ```mount```para montar o compartilhamento NFS. nesse exemplo abaixo o ip  do servidor que exporta o sistema de arquivos que você deseja compartilhar ,srv/amanda_campos é o sistema de arquivo ou diretório sendo exportado do servidor, ou seja, o diretório que você deseja montar. e o /var/www/html é o local do cliente onde /remote/export é montado.

```
mount -t nfs4 52.0.247.44:srv/amanda_campos /var/www/html

```

![Captura de tela de 2023-11-30 12-39-42](https://github.com/AmandaCampoos/Desafio/assets/138727208/cf5e3c84-6cc1-4851-b0f4-d684443be45f)


com o comando 

```
cat /var/www/html/index.html
```
Podemos observar o arquivo index.html 

![Captura de tela de 2023-11-29 19-35-09](https://github.com/AmandaCampoos/Desafio/assets/138727208/ca1221bc-75be-4c34-b4a9-11f172aebc36)


Assim se digitar o ip público no navegador você poderá encontrar seu arquivo index.html

![Captura de tela de 2023-11-30 20-27-58](https://github.com/AmandaCampoos/Desafio/assets/138727208/144a3825-9a02-40df-861c-9f88c1d37934)


#### Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs;
O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline;

Para criar o script escolha o diretório e dê um nome a ele, nesse exemplo vou chamar de datahora.sh

vim datahora.sh 

```
#!bin/bash #!bin/bash
 DATE=$(date '+%D-%M-%Y %H:%M:%S')
 SERVICO="httpd"
 STATUS=$(systemctl is-active $SERVICO)

  if [ "$STATUS" = "active" ];then
                STATUS="Online"
                MESSAGE="está online!"
                FILENAME="/srv/amanda_campos/online.txt"
  else
                STATUS="Offline"
                        MESSAGE="está offline."
                FILENAME="/srv/amanda_campos/offline.txt"
  fi


  echo "$DATE httpd $STATUS - $MESSAGE" >> "$FILENAME"
```
salve o script e execute os comandos:

```
[root@ip-10-0-19-137 amanda_campos]# chmod +x datahora.sh
[root@ip-10-0-19-137 amanda_campos]# . /srv/amanda_campos/datahora.sh

```
logo veremos nosso script rodando e funcionando. Para isso acontecer utilizamos também o serviço de "crontab" para preparar a execução automatizada do script a
cada 5 minutos.

com o comando ```crontab -e``` você consegue adicionar serviço de contagem de horas para execução automatica de script.

![Captura de tela de 2023-11-30 13-31-30](https://github.com/AmandaCampoos/Desafio/assets/138727208/f431ae4b-318d-49fc-94af-a3eaf8889ec1)



com o comando ```crontab -f```você consegue exibir todos os trabalhos no cron/crontab.


![Captura de tela de 2023-11-30 10-16-25](https://github.com/AmandaCampoos/Desafio/assets/138727208/7cd42b0c-35ea-4e9c-8da6-cea959f5b814)


Detalhes da configuração Crontab 

![Captura de tela de 2023-11-30 12-35-21](https://github.com/AmandaCampoos/Desafio/assets/138727208/4575dd35-4da1-4ca9-9e53-cedf798b0c31)


### Finalizamos esse projeto aqui ❤️ obrigada pela sua atenção.











