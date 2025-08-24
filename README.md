# Documentação: Exercício DIO Docker com Deploy AWS EC2

## Contexto do Projeto

Este projeto faz parte dos desafios práticos do Bootcamp WEX – End to End Engineering, uma iniciativa conjunta entre a WEX, empresa global de tecnologia financeira, e a plataforma de ensino DIO (Digital Innovation One). Apresenta a solução desenvolvida para o exercício de containerização Docker Compose: Executando uma aplicação HTML em um container apache HTTPD. A **implementação extra** inclui o deploy real em ambiente de produção utilizando Amazon Web Services (AWS) EC2, expandindo o escopo original com conhecimentos práticos em DevOps e Cloud Computing.

🔗 **Exercício Original: https://github.com/denilsonbonatti/docker-projeto1-dio**

### Proposta do Desafio

O exercício consiste em criar um arquivo YML com as definições de um **container Docker** executando servidor Apache (httpd), especificar o local onde os arquivos da aplicação estarão através de **mapeamento de volumes**, desenvolver uma aplicação web simples e versionar o projeto em repositório GitHub.

## Problema e Solução

### Desafio Enfrentado
- Exercício básico limitado ao ambiente local
- Implementação limitada ao ambiente de desenvolvimento local
- Ausência de deploy em infraestrutura de produção para validação prática

### Solução Implementada
Utilização de **Docker + AWS EC2 + Security Groups** como stack completa de produção, proporcionando não apenas o cumprimento dos requisitos, mas também:

1. **Deploy Real**: Aplicação acessível publicamente na internet
2. **Infraestrutura Cloud**: Servidor dedicado na Amazon Web Services
3. **Configuração de Segurança**: Firewall e controle de acesso adequados

## Arquitetura da Solução
<img width="3840" height="541" alt="Untitled diagram _ Mermaid Chart-2025-08-23-184554" src="https://github.com/user-attachments/assets/b446f6e6-5489-47fb-b1df-c4460d4a6e25" />

## Tecnologias Utilizadas

- **Docker & Docker Compose**: Containerização da aplicação
- **Apache HTTP Server 2.4**: Web server em container Linux
- **Amazon EC2**: Instância virtual na nuvem AWS
- **Amazon Linux 2023**: Sistema operacional base
- **AWS Security Groups**: Firewall e controle de acesso
- **SCP**: Transferência segura de arquivos
- **SSH**: Acesso remoto seguro à instância

## Execução - Método Local (Desenvolvimento)

A implementação inicial seguiu os requisitos básicos do exercício:

### Funcionalidades Demonstradas
- **Docker Compose**: Configuração declarativa do ambiente
- **Volume Mapping**: Sincronização entre código local e container
- **Port Mapping**: Exposição do serviço na porta 8080
- **Restart Policy**: Recuperação automática de falhas

### Estrutura do Projeto

```
docker-projeto1-dio/
├── compose.yml         
├── Website/           
│   └── index.html      
└── .git/            
```

### Configuração Docker Compose

```yaml
services:
  apache-server:
    image: httpd:2.4
    container_name: meu-servidor-app
    ports:
      - "8080:80"
    volumes:
      - ./Website:/usr/local/apache2/htdocs/
    restart: unless-stopped
    environment:
      - APACHE_RUN_USER=www-data
      - APACHE_RUN_GROUP=www-data
```

## Execução - Método AWS EC2 (Produção)

A implementação avançada incluiu deploy real na nuvem AWS:

### Vantagens do Deploy em Produção
- **Acesso Global**: Disponível 24/7 de qualquer lugar do mundo
- **Alta Disponibilidade**: Infraestrutura profissional da Amazon
- **Escalabilidade**: Recursos ajustáveis conforme demanda
- **Segurança**: Controle granular de acesso e firewall

### Configuração da Instância EC2

**Especificações Técnicas:**
- **AMI**: Amazon Linux 2023
- **Tipo**: t2.micro (elegível para Free Tier)
- **vCPUs**: 1 core
- **RAM**: 1 GB
- **Armazenamento**: 8 GB SSD
- **Região**: us-west-2 (Oregon)
- **IP Público**: 35.88.162.245

<br>
### Transferência de Arquivos via SCP

Demonstração do processo de transferência dos arquivos do projeto local para a instância EC2 usando protocolo SCP seguro:

```bash
scp -i ~/Desktop/my-key-aws.pem -r ~/desktop/docker-projeto1-dio ec2-user@35.88.162.245:/home/ec2-user/
```

### Instalação do Docker na EC2

Processo de configuração do ambiente Docker na instância Amazon Linux, incluindo instalação, inicialização e configuração de permissões:

```bash
# Atualização do sistema
sudo yum update -y

# Instalação do Docker
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user

# Instalação do Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

<br>

### Configuração de Security Groups

**Regras de Firewall AWS:**

| Porta | Protocolo | Origem    | Descrição         | ID Security Group     |
| ----- | --------- | --------- | ----------------- | --------------------- |
| 22    | TCP       | 0.0.0.0/0 | SSH Access        | sgr-00f75f26d1b3038f6 |
| 8080  | TCP       | 0.0.0.0/0 | Apache Web Server | sgr-0f77aa69def53073e |
| 80    | TCP       | 0.0.0.0/0 | HTTP Alternative  | sgr-0b65df8081eccba2e |

<br>

```bash
[ec2-user@ip-172-31-29-241 docker-projeto1-dio]$ docker-compose up -d
[+] Running 7/7
✔ apache-server Pulled                                    4.6s
✔ Container meu-servidor-app           Started            0.7s

[ec2-user@ip-172-31-29-241 docker-projeto1-dio]$ docker ps
CONTAINER ID   IMAGE       COMMAND              STATUS          PORTS
e26b43129285   httpd:2.4   "httpd-foreground"   Up 13 seconds   0.0.0.0:8080->80/tcp
```

## Diagrama de Fluxo da Solução

**Arquitetura Visual:** Fluxo completo desde o desenvolvimento local até a produção na AWS, mostrando todas as etapas de transferência, configuração e deploy.
<img width="3840" height="541" alt="Untitled diagram _ Mermaid Chart-2025-08-23-184554" src="https://github.com/user-attachments/assets/7507d043-3bc0-4d68-ad4e-fa5f6b35b468" />
```

## Resultados Alcançados

### Aplicação Final 
<img width="1673" height="311" alt="01" src="https://github.com/user-attachments/assets/1070601c-f17e-42a6-bc6e-ddfb15f53233" />

**URL de Produção Ativa:** http://35.88.162.245:8080

**Página HTML Renderizada:** A aplicação apresenta uma Landing Page de apresentação pessoal com design responsivo e informações técnico-profissionais organizadas.


### Comparação de Implementações

| Aspecto         | Exercício Básico  | Implementação AWS  |
| --------------- | ----------------- | ------------------ |
| Ambiente        | ☁️ Local apenas    | 🌐 Produção Cloud   |
| Acesso          | 🏠 localhost       | 🌍 Internet pública |
| Disponibilidade | ⏰ Sessão local    | ⚡ 24/7 continuous  |
| Infraestrutura  | 💻 Máquina pessoal | ☁️ AWS Enterprise   |
| Segurança       | 🔓 Sem firewall    | 🔒 Security Groups  |
| Escalabilidade  | ❌ Limitada        | ✅ Configurável     |

## Vantagens da Solução

### Benefícios Técnicos Demonstrados
- **Containerização**: Isolamento e portabilidade da aplicação
- **Cloud Computing**: Utilização de infraestrutura como serviço
- **DevOps**: Pipeline completo de desenvolvimento para produção
- **Segurança**: Configuração adequada de acesso e firewall
- **Monitoramento**: Logs e status de aplicação em tempo real

### Comandos de Monitoramento e Administração

```bash
# Verificar status da aplicação
docker ps

# Visualizar logs em tempo real
docker logs -f meu-servidor-app

# Teste de conectividade local
curl localhost:8080

# Gerenciamento do serviço
docker-compose restart
docker-compose down
docker-compose up -d
```

## Conclusão

A solução implementada demonstrou ser uma **evolução significativa** do exercício básico proposto. A combinação de Docker + AWS EC2 ofereceu:

- **Funcionalidade profissional** equivalente a ambientes corporativos
- **Experiência de desenvolvimento moderna** com ferramentas cloud
- **Conhecimento prático** em infraestrutura e deploy
- **Diferencial competitivo** para o mercado de trabalho

Esta abordagem foi desenvolvida como **implementação extra** ao exercício básico. A solução implementada além dos requisitos mínimos desenvolver competências práticas em DevOps e Cloud Computing. Uma experiência completa de desenvolvimento até produção.

## Especificações Técnicas Finais

- **Aplicação**: HTML responsivo com conteúdo educativo sobre Docker
- **Container**: Apache HTTP Server 2.4 em Linux
- **Cloud Provider**: Amazon Web Services (AWS)
- **Instância**: EC2 t2.micro (Free Tier elegível)
- **Sistema Operacional**: Amazon Linux 2023
- **Conexão**: SSH com chaves criptográficas
- **Transferência**: SCP (Secure Copy Protocol)
- **Monitoramento**: Docker logs e AWS CloudWatch
- **URL Produção**: http://35.88.162.245:8080
- **Status**: ✅ Online e Operacional 24/7

---

**Bootcamp WEX - End to End Engineering - [DIO](https://web.dio.me/)**  
Desenvolvido por [Natalia Moraes](https://www.linkedin.com/in/moraesnatalia/)
