# Descrição para o Atividade de Linux, AWS e servidores.


O Desafio consiste em Criar 1 instância EC2 AWS com o sistema operacional Amazon Linux, Configurar o servidor NFS, Subir um apache no servidor - o apache deve
estar online e rodando. Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs;
O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline; O script deve gerar 2 arquivos de saida: 1
para o serviço online e 1 para o serviço OFFLINE; Preparar a execução automatizada do script a cada 5 minutos.


# Para iniciar a instacia EC2 vamos ultizar os recursos:
- Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);
- Criar ou escolher uma VPC existente e associar a instancia.
- Gerar 1 elastic IP e anexar à instância EC2.
- Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP,2049/TCP/UDP, 80/TCP, 443/TCP)Para a liberaçao das portas, cria se um grupo de segurança de entrada e saida. Assim possibilitando o acesso da sua máquina virtual a recursos como o nfs.


 # Para a configuração do linux:
 Em algumas distribuição linux o nfs já vem pré instalado nesse caso apenas usamos os comandos:
   systemctl start nfs-server rpcbind
 
