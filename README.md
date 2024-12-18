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
   ```bash
   sudo yum install -y amazon-efs-utils

