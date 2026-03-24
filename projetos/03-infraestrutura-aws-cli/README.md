# 💻 Projeto 03: Provisionando Infraestrutura AWS via CLI (Linha de Comando)

<div align="center">

![AWS](https://img.shields.io/badge/AWS-CLI-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![VPC](https://img.shields.io/badge/AWS-VPC-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![EC2](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
![Status](https://img.shields.io/badge/Status-CONCLUÍDO-1a9c3e?style=for-the-badge)

</div>

---

## 🎯 Objetivo

Provisionar uma infraestrutura básica na AWS utilizando exclusivamente a linha de comando (**AWS CLI**), compreendendo a relação e dependência entre os recursos de rede e computação dentro da nuvem. 

O laboratório foi executado no ambiente sandbox do programa **AWS re/Start**, realizando a criação dos recursos manualmente para compreender cada etapa do processo de configuração de forma granular.

---

## 🛠️ Serviços Utilizados

| Serviço | Função |
|---|---|
| **Amazon VPC** | Rede virtual isolada na AWS |
| **Amazon EC2** | Instância de servidor virtual (Amazon Linux) |
| **Internet Gateway** | Conectividade entre a VPC e a internet |
| **Route Tables** | Controle de tráfego e roteamento da rede |
| **Security Groups** | Firewall virtual para permissão de acesso SSH |
| **AWS CLI** | Interface de linha de comando para provisionamento |
| **PowerShell** | Terminal para execução dos comandos e SSH |

---

## 🏗️ Arquitetura da Solução

A infraestrutura foi projetada utilizando uma **VPC dedicada** (não-default) com uma **subnet pública** conectada à internet por meio de um **Internet Gateway**. Uma instância EC2 com Amazon Linux foi implantada dentro desta subnet e protegida por um **Security Group** permitindo acesso SSH (porta 22). O acesso administrativo foi validado via terminal.

```text
Internet
    |
    | Porta 22 (SSH)
    ▼
┌──────────────────────────────┐
│       Internet Gateway       │
└──────────────────────────────┘
    |
    ▼
┌──────────────────────────────────────────────┐
│             VPC (10.0.0.0/16)                │
│                                              │
│  ┌────────────────────────────────────────┐  │
│  │       Subnet Pública (10.0.1.0/24)     │  │
│  │                                        │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │      Security Group (SSH)        │  │  │
│  │  └──────────────────────────────────┘  │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │   Amazon EC2 (Amazon Linux)      │  │  │
│  │  └──────────────────────────────────┘  │  │
│  └────────────────────────────────────────┘  │
│                                              │
│  Route Table → 0.0.0.0/0 → IGW               │
└──────────────────────────────────────────────┘

📋 Etapas de Implementação
Toda a infraestrutura foi provisionada utilizando AWS CLI no PowerShell, sem utilização do console da AWS:

Criação da VPC: Definição de uma rede isolada 10.0.0.0/16.

Subnet Pública: Criação de uma subnet específica para recursos com acesso externo.

Internet Gateway: Criação e associação do IGW à VPC para habilitar saída para internet.

Tabela de Rotas: Criação de uma Route Table personalizada.

Roteamento: Configuração da rota padrão (0.0.0.0/0) apontando para o Internet Gateway.

Associação de Rede: Vinculação da Route Table à Subnet pública.

Segurança: Criação de um Security Group e configuração da regra de entrada para SSH (Porta 22).

Launch EC2: Lançamento da instância associando VPC, Subnet e SG via IDs gerados na CLI.

⌨️ Comandos Utilizados
bash
# 1. Criar VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# 2. Criar subnet pública
aws ec2 create-subnet --vpc-id vpc-08cfaca6d74f943e2 --cidr-block 10.0.1.0/24

# 3. Criar e Associar Internet Gateway
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --internet-gateway-id igw-0a5deee9519b89ae8 --vpc-id vpc-08cfaca6d74f943e2

# 4. Criar tabela de roteamento e rota para internet
aws ec2 create-route-table --vpc-id vpc-08cfaca6d74f943e2
aws ec2 create-route --route-table-id rtb-04fb10c03f3271a4c --destination-cidr-block 0.0.0.0/0 --gateway-id igw-0a5deee9519b89ae8

# 5. Associar tabela de rotas à subnet
aws ec2 associate-route-table --route-table-id rtb-04fb10c03f3271a4c --subnet-id subnet-091fca42df094adb1

# 6. Criar Security Group e regra SSH
aws ec2 create-security-group --group-name meusegurancalab --description "acesso ssh" --vpc-id vpc-08cfaca6d74f943e2
aws ec2 authorize-security-group-ingress --group-id sg-051e70cb2f606cfd9 --protocol tcp --port 22 --cidr 0.0.0.0/0

# 7. Consultar informações e IP Público da instância
aws ec2 describe-instances --query "Reservations[*].Instances[*].{ID:InstanceId,Estado:State

🔑 Acesso e Validação
Após o provisionamento, a conectividade foi validada através de acesso remoto SSH:

Conexão via PowerShell: Utilizando OpenSSH nativo com chave .pem.

Conexão via PuTTY: Utilizando chave .ppk convertida.

A correta configuração das tabelas de rotas e do Security Group foi confirmada pelo sucesso no handshake SSH e login no sistema operacional Amazon Linux.

📸 Evidências
Screenshots disponíveis na pasta /evidencias.

💡 Aprendizado
Compreensão prática do provisionamento de infraestrutura utilizando AWS CLI.

Entendimento sobre dependência rigorosa entre recursos (ex.: não é possível deletar uma VPC com IGW associado).

Prática de filtragem de dados em JSON/Table utilizando o parâmetro --query.

Consolidação da habilidade de gerenciar acessos remotos seguros, essencial para administração de servidores em nuvem.

🔗 Portfólio
📁 Voltar ao Portfólio Principal

<div align="center">Marcelo Carrara · Aspiring Solutions Architect · Paraná, Brasil</div>

Código
