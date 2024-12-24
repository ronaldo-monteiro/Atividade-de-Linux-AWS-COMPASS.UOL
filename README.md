# Atividade de Linux AWS - COMPASS.UOL

## Parte 1 - O desafio da atividade

### 1. Requisitos AWS:
- Gerar uma chave pública para acesso ao ambiente;
- Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);
- Gerar 1 Elastic IP e anexar à instância EC2;
- Liberar as portas de comunicação para acesso público: 
  - **22/TCP**, 
  - **111/TCP e UDP**, 
  - **2049/TCP/UDP**, 
  - **80/TCP**, 
  - **443/TCP**.

### 2. Requisitos no Linux:
- Configurar o NFS entregue;
- Criar um diretório dentro do filesystem do NFS com seu nome;
- Subir um Apache no servidor (deve estar online e rodando);
- Criar um script que:
  - Valide se o serviço está online;
  - Envie o resultado da validação para o diretório no NFS;
  - Inclua no log: **Data e Hora**, **nome do serviço**, **status** e **mensagem personalizada** (ONLINE ou OFFLINE);
  - Gere dois arquivos de saída: 
    - Um para o serviço **ONLINE**;
    - Um para o serviço **OFFLINE**;
- Configurar a execução automatizada do script a cada 5 minutos;
- Fazer o versionamento da atividade;
- Documentar o processo de instalação do Linux.

---

## Resolução do desafio

### Criando a chave pública e instância EC2
1. Criar uma chave pública de acesso e anexá-la à nova instância EC2.
2. No console da AWS, buscar por **EC2** e ir em **Pares de chaves**.
3. Escolher um nome para a chave e clicar em **Criar par de chaves**.
4. Um arquivo contendo a chave será gerado (**xxx.pem**). **Não perca este arquivo!**
5. Criar a instância usando a opção **Executar instâncias**.
6. Configurar as **Tags** (Name, Project e CostCenter) para evitar problemas futuros.
7. Selecionar a imagem **Amazon Linux 2 AMI, SSD Volume Type**.
8. Escolher a instância do tipo **t3.small**.
9. Utilizar um SSD modelo **gp2** com 16 GB de armazenamento.
10. Finalizar clicando em **Executar instância**.

---

### Anexando um Elastic IP à EC2
1. No console AWS, acessar **EC2 > IPs elásticos**.
2. Escolher **Alocar endereço IP elástico**.
3. Associar o endereço IP elástico à instância EC2 criada.
4. No console do serviço EC2, acessar **Segurança > Grupos de segurança**.
5. Configurar as seguintes regras de entrada no grupo de segurança:

| Tipo              | Protocolo | Intervalo de Portas | Origem  | Descrição |
|-------------------|-----------|---------------------|---------|-----------|
| SSH               | TCP       | 22                  | MEU IP  | Acesso SSH |
| UDP personalizado | UDP       | 111                 | 0.0.0.0/0 | RPC |
| UDP personalizado | UDP       | 2049                | 0.0.0.0/0 | NFS |
| TCP personalizado | TCP       | 80                  | 0.0.0.0/0 | HTTP |
| TCP personalizado | TCP       | 443                 | 0.0.0.0/0 | HTTPS |
| TCP personalizado | TCP       | 111                 | 0.0.0.0/0 | RPC |
| TCP personalizado | TCP       | 2049                | 0.0.0.0/0 | NFS |

---

### Configurando o Sistema de Arquivos AWS EFS
1. No console AWS, buscar pelo serviço **EFS**.
2. Criar um Sistema de Arquivos EFS.
3. Certificar-se de que a VPC seja a mesma da instância EC2.
4. Liberar a porta **2049/TCP** para acesso.

#### Criando o Ponto de Montagem na Instância EC2:
1. Instalar o pacote necessário:
   
   
   ```sudo yum install -y amazon-efs-utils```

2. Criar um diretório local para o ponto de montagem.
3. Obter as informações de DNS do ponto de montagem no console do EFS.
4. Montar o sistema de arquivos usando:
h

  ```sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 ://```

### Configurar a Montagem Automática:

1. Editar o arquivo /etc/fstab  e adicionar a seguinte linha:


  ```file_system_id.efs.aws-region.amazonaws.com:/ mount_point nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0```

2. Verificar a montagem usando:


  ```df -h```

### Configurando o servidor Apache

1. Atualizar o sistema:


  ```sudo yum update -y```

2. Instalar o Apache:


  ```sudo yum install httpd -y```

3. Iniciar o Apache:


  ```sudo systemctl start httpd```

4. Habilitar o Apache para iniciar automaticamente:


  ```sudo systemctl enable httpd```

5. Verificar o status:

  ```sudo systemctl status httpd```


### Configurando o script de validação

Criar o script usando o editor nano:
  
  ```nano script.sh```

Adicionar o conteúdo abaixo ao script:


  ```#!/bin/bash```

### Script que verifica o status do serviço httpd e salva o resultado em um arquivo.

```
   SERVICE_NAME="httpd"
   EFS_MOUNT_PATH="/var/efs/ronaldo"

   CURRENT_DATE=$(date "+%Y-%m-%d")
   CURRENT_TIME=$(date "+%H:%M:%S")

   if systemctl is-active --quiet $SERVICE_NAME; then
   STATUS="ONLINE"
   MESSAGE="O serviço $SERVICE_NAME está online."
   OUTPUT_FILE="$EFS_MOUNT_PATH/status_online.txt"
 else
   STATUS="OFFLINE"
   MESSAGE="O serviço $SERVICE_NAME está offline."
   OUTPUT_FILE="$EFS_MOUNT_PATH/status_offline.txt"
 if

echo "$CURRENT_DATE $CURRENT_TIME - $STATUS - $MESSAGE" > $OUTPUT_FILE

```

Tornar o script executável:

   ```chmod +x script.sh```

Executar o script:


   ```./script.sh```

Configurar a execução automática com Cron

1. Editar o cronjob:

   ```crontab -e```

Adicionar a seguinte linha:

```
  */5 * * * * /caminho/do/script/check_service.sh

```

Projeto Finalizado!





