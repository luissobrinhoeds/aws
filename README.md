# AWS EC2 - Playbooks Ansible

Conjunto de playbooks [Ansible](https://www.ansible.com/) para gerenciar o ciclo de vida de instâncias [Amazon EC2](https://aws.amazon.com/ec2/) na AWS.

## 📋 Pré-requisitos

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) 2.9 ou superior
- [Python](https://www.python.org/) 3.x
- [Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) e [Botocore](https://botocore.amazonaws.com/v1/documentation/api/latest/index.html)
- Credenciais AWS configuradas (via variáveis de ambiente, `~/.aws/credentials` ou perfil IAM)

## 🔧 Instalação

Instale a coleção `amazon.aws` a partir do arquivo `requirements.yml`:

```bash
ansible-galaxy collection install -r requirements.yml
```

## 📁 Estrutura do Projeto

| Arquivo | Descrição |
|---|---|
| `requirements.yml` | Define as dependências de coleções Ansible |
| `create_instance.yml` | Cria uma nova instância EC2 |
| `list_instances.yml` | Lista todas as instâncias EC2 da região |
| `start_instance.yml` | Inicia uma instância EC2 existente |
| `stop_instance.yml` | Para uma instância EC2 existente |
| `terminate_instance.yml` | Destrói (termina) uma instância EC2 pelo nome |

## 🚀 Uso

### Criar instância EC2

Cria uma nova instância EC2. Valida os parâmetros obrigatórios e impede a criação de instâncias duplicadas (mesmo nome na mesma região).

```bash
ansible-playbook create_instance.yml \
  -e "region=us-east-1" \
  -e "name=meu-servidor" \
  -e "type=t2.micro" \
  -e "ami=ami-0abcdef1234567890" \
  -e "subnet=subnet-12345678" \
  -e "security_group=sg-12345678" \
  -e "key_pair=minha-chave"
```

**Parâmetros obrigatórios:**

| Parâmetro | Descrição |
|---|---|
| `region` | Região AWS (ex.: `us-east-1`) |
| `name` | Nome da instância (tag `Name`) |
| `type` | Tipo da instância (ex.: `t2.micro`) |
| `ami` | ID da AMI (ex.: `ami-0abcdef1234567890`) |
| `subnet` | ID da sub-rede (ex.: `subnet-12345678`) |
| `security_group` | ID do security group (ex.: `sg-12345678`) |
| `key_pair` | Nome do key pair (ex.: `minha-chave`) |

### Listar instâncias EC2

Lista todas as instâncias EC2 da região `us-east-1` (região fixa no playbook), exibindo ID, nome, estado, tipo, IPs e zona de disponibilidade.

```bash
ansible-playbook list_instances.yml
```

### Iniciar instância EC2

Inicia uma instância EC2 existente a partir do seu ID.

```bash
ansible-playbook start_instance.yml \
  -e "region=us-east-1" \
  -e "instance_id=i-1234567890abcdef0"
```

**Parâmetros obrigatórios:**

| Parâmetro | Descrição |
|---|---|
| `region` | Região AWS (ex.: `us-east-1`) |
| `instance_id` | ID da instância (ex.: `i-1234567890abcdef0`) |

### Parar instância EC2

Para uma instância EC2 existente a partir do seu ID.

```bash
ansible-playbook stop_instance.yml \
  -e "region=us-east-1" \
  -e "instance_id=i-1234567890abcdef0"
```

**Parâmetros obrigatórios:**

| Parâmetro | Descrição |
|---|---|
| `region` | Região AWS (ex.: `us-east-1`) |
| `instance_id` | ID da instância (ex.: `i-1234567890abcdef0`) |

### Destruir instância EC2

Destrói (termina) uma instância EC2 pelo nome (tag `Name`). Valida que existe exatamente uma instância com o nome informado antes de destruí-la.

```bash
ansible-playbook terminate_instance.yml \
  -e "region=us-east-1" \
  -e "name=meu-servidor"
```

**Parâmetros obrigatórios:**

| Parâmetro | Descrição |
|---|---|
| `region` | Região AWS (ex.: `us-east-1`) |
| `name` | Nome da instância (tag `Name`) |

## ⚠️ Considerações

- O playbook `create_instance.yml` **cancela a criação** se já existir uma instância com o mesmo nome na região informada.
- O playbook `terminate_instance.yml` **exige exatamente uma instância** com o nome informado. Se nenhuma ou mais de uma for encontrada, a execução é interrompida.
- O playbook `list_instances.yml` utiliza a região fixa `us-east-1`.
- Todos os playbooks aguardam (`wait: true`) a conclusão da operação antes de exibir o resultado final.
- A destruição de instâncias EC2 é uma operação **irreversível** e pode resultar em perda de dados.