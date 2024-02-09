# Atividade-de-Linux-AWS-COMPASS.UOL

## Parte 1 - O desafio da atividade: 

###  1 - Requisitos AWS:
Gerar uma chave pública para acesso ao ambiente;    
Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);    
Gerar 1 elastic IP e anexar à instância EC2;  
Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).  

###  2 - Requisitos no linux:
Configurar o NFS entregue;  
Criar um diretorio dentro do filesystem do NFS com seu nome;  
Subir um apache no servidor - o apache deve estar online e rodando;  
Criar um script que valide se o serviço esta online e envie o resultado da  
validação para o seu diretorio no nfs;  
O script deve conter - Data HORA + nome do serviço + Status + mensagem  
personalizada de ONLINE ou offline;  
O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o
serviço OFFLINE;  
Preparar a execução automatizada do script a cada 5 minutos.  
Fazer o versionamento da atividade;  
Fazer a documentação explicando o processo de instalação do Linux.  

## Resuloção do desafio: 

1 - Criar uma chave pública de acesso e após este passo, anexar a chave a uma nova instância EC2 que criou.  
2 - No console da AWS digite  "EC2" no campo de busca, e em "Pares de chaves".   
4 - Neste passo excolha um nome para a chave e finalize clicando em  "Criar par de chaves".  
5 - Um arquivo contendo a chave será gerado. Não perca este arquivo!!! Sua extesão é (xxx.pem)    
6 - Execute a instancia na opção "Executar instâncias".  
7 - Preste atenção neste ponto! As Tags são tudo!!! (Name, Project e CostCenter) Sem as TAGS seu projeto trava!   
8 - Como pedido no projeto, encontre a imagem Amazon Linux 2 AMI, SSD Volume Type.  
9 - Instancia pedida no projeto t3.small.
10 - Lembra da chave que gerou?! Você vai precisar dela agora!
12 - Utilize um SSD modelo gp2 com 16 GB dearmazenamento.
13 - Ao final clique  em "Executar instância" (caso tudo tenha dado certo) finalizamos a primeira parte!

## Anexar um  IP elástico à sua EC2.

1 - Acessar o console e escolher "EC2", opção "IPs elástico".  
2 - Opção "Alocar endereço IP elástico".  
3 - Utilize o ip alocado e "Associar endereço IP elástico".  
4 - Selecionar a instância EC2 criada anteriormente e clicar em "Associar".  
5 - Acessar a console, na pagina do serviço EC2, e clicar em "Segurança" > "Grupos de segurança" no menu lateral esquerdo.  

Selecione o grupo de segurança da instância EC2 criada anteriormente.  

Edite as regras de entrada "Editar regras de entrada".  

Configure as seguintes regras conforme a tabela:

Tipo	Protocolo	Intervalo de portas	Origem	Descrição

SSH	 TCP	22       MEU IP  0.0.0.0/0 SSH
UDP personalizado	UDP	111	0.0.0.0/0	RPC
UDP personalizado	UDP	2049	0.0.0.0/0	NFS
TCP personalizado	TCP	80	0.0.0.0/0	HTTP
TCP personalizado	TCP	443	0.0.0.0/0	HTTPS
TCP personalizado	TCP	111	0.0.0.0/0	RPC
TCP personalizado	TCP	2049	0.0.0.0/0	NFS

##Configurando o Sistema de Arquivos AWS EFS na Instância EC2.
Acesse o Console da AWS.

Navegue até o Serviço EFS.

Crie um Sistema de Arquivos EFS.

Configure as Opções:

Escolha a VPC (deve ser a mesma VPC da Instância EC2).
Defina as opções de segurança de acesso, liberando a porta 2049/TCP

Crie o Ponto de Montagem na Instância EC2:

Vá para a instância EC2.
Instale o pacote sudo yum install -y amazon-efs-utils para suporte NFS.
Crie um diretório local que servirá como ponto de montagem.
Obtenha as Informações de Montagem do EFS:

Volte ao console do EFS e obtenha as informações de DNS do ponto de montagem.
Monte o Sistema de Arquivos na Instância EC2:

Execute o comando de montagem na instância EC2 usando o cliente do NFS:
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 <DNS do EFS>:/ /<caminho local>

Configure a Montagem Automática:

Abra o arquivo /etc/fstab em um editor.
Para montar automaticamente um sistema de arquivos usando o NFS em vez do auxiliar de montagem do EFS, adicione a seguinte linha ao /etc/fstab arquivo.
file_system_id.efs.aws-region.amazonaws.com:/ mount_point nfs4 nfsvers=4.1,rsize=1048576wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0 

Substitua file_system_id pelo ID do sistema de arquivos que você está montando.

Substitua aws-region Região da AWS pela que está no sistema de arquivos, como. us-east-1
Substitua mount_point pelo ponto de montagem do sistema de arquivos.
Confirme se o sistema de arquivos EFS está montado corretamente usando o comando df -h.

Referência: Documentação do Amazon EFS

Configurar o Apache.
Executar o comando sudo yum update -y para atualizar o sistema.
Executar o comando sudo yum install httpd -y para instalar o apache.
Executar o comando sudo systemctl start httpd para iniciar o apache.
Executar o comando sudo systemctl enable httpd para habilitar o apache para iniciar automaticamente.
Executar o comando sudo systemctl status httpd para verificar o status do apache.
Configurações adicionais do apache podem ser feitas no arquivo /etc/httpd/conf/httpd.conf.
Parar o apache, executar o comando sudo systemctl stop httpd.
Exemplo de user data para instalação do apache na criação da ec2.


