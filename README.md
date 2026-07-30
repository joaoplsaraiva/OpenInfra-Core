# OpenInfra-Core 🚀

**OpenInfra-Core** é um projeto *open source* de **Infraestrutura como Código (IaC)** desenvolvido com **Ansible** e **Vagrant**. Seu objetivo é automatizar o provisionamento completo de um ambiente de rede corporativo em servidores Linux (Debian / Ubuntu Server), substituindo soluções proprietárias por componentes totalmente abertos e de alta performance.

---

## 📋 Serviços Incluídos

| Serviço | Tecnologia Utilizada | Descrição / Função |
| :--- | :--- | :--- |
| **Active Directory** | **Samba4** | Controlador de Domínio (AD DC) compatível com Kerberos, NTLM e LDAP. |
| **DNS** | **BIND9 (DLZ)** | Resolução de nomes integrada nativamente ao Samba4. |
| **DHCP** | **ISC Kea DHCP** | Atribuição dinâmica de IP com opções personalizadas de DNS e Gateway. |
| **NTP** | **Chrony** | Sincronização precisa de relógio (essencial para autenticação Kerberos). |
| **Root CA** | **Step-CA / Easy-RSA** | Infraestrutura de Chaves Públicas (PKI) interna para emissão de certificados. |
| **File Server** | **Samba (ACLs)** | Compartilhamento de arquivos integrado aos grupos de usuários do AD. |
| **Print Server** | **CUPS** | Servidor de impressão centralizado compatível com clientes Windows e Linux. |

---

## 📁 Estrutura do Repositório

```text
openinfra-core/
├── Vagrantfile               # Ambiente virtual de testes locais (VirtualBox / KVM)
├── ansible.cfg               # Configurações globais do Ansible
├── inventory.ini             # Arquivo de inventário dos servidores
├── site.yml                  # Playbook principal de execução
├── group_vars/
│   └── all.yml               # Variáveis globais do projeto (Domínio, Senhas, Subnets)
└── roles/
    ├── ntp/                  # Instalação e ajustes do Chrony
    ├── samba_ad/             # Provisionamento do Active Directory e DNS (BIND9)
    ├── dhcp/                 # Configuração do ISC Kea
    ├── pki/                  # Criação da Root CA e emissão de certificados
    ├── file_server/          # Criação de pastas compartilhadas e permissões (POSIX ACLs)
    └── print_server/         # Configuração de filas de impressão no CUPS
```

---

## ⚙️ Pré-requisitos

### Para Testes Locais (Laboratório)
* **Vagrant** (v2.3+)
* **VirtualBox** ou **KVM/QEMU**
* **Ansible** (v2.12+) instalado na máquina host

### Para Produção
* Servidor com **Ubuntu 24.04 LTS** ou **Debian 12** recém-instalado.
* Mínimo de **2 GB de RAM** e **2 vCPUs**.
* Endereço IP estático configurado no servidor target.
* Acesso SSH com privilégios de `root` ou `sudo`.

---

## 🚀 Como Usar

### 1. Clonar o Repositório
```bash
git clone https://github.com/seu-usuario/openinfra-core.git
cd openinfra-core
```

### 2. Configurar as Variáveis
Edite o arquivo `group_vars/all.yml` ajustando os parâmetros para o seu domínio e rede:

```yaml
# Configurações de Domínio
domain_name: "empresa.local"
realm_name: "EMPRESA.LOCAL"
domain_pass: "SenhaAdminSegura123!"

# Configurações de Rede
network_interface: "eth0"
network_subnet: "192.168.1.0/24"
dhcp_range_start: "192.168.1.100"
dhcp_range_end: "192.168.1.200"
dns_forwarder: "1.1.1.1"

# Configurações do File Server
share_path: "/dados/compartilhado"
```

### 3. Rodar em Ambiente de Testes (Vagrant)
Para levantar a VM de testes e aplicar o playbook automaticamente:

```bash
vagrant up
```

### 4. Rodar em Produção
Ajuste os IPs no arquivo `inventory.ini`:

```ini
[domain_controllers]
192.168.1.10 ansible_user=root
```

Execute o playbook principal via Ansible:

```bash
ansible-playbook -i inventory.ini site.yml
```

---

## 🛠️ Detalhes da Ordem de Provisionamento

Para garantir dependências e funcionamento correto, a automação executa as funções na seguinte ordem sequencial:

1. **NTP (`chrony`):** Garante a precisão do relógio do sistema antes do Samba ser provisionado.
2. **Active Directory & DNS (`samba_ad`):** Executa o `samba-tool domain provision` e integra o BIND9 DLZ.
3. **DHCP (`dhcp`):** Ativa o ISC Kea apontando o IP do servidor como DNS primário.
4. **Root CA (`pki`):** Cria a autoridade certificadora e disponibiliza o certificado `.crt` para distribuição.
5. **File Server (`file_server`):** Ajusta diretórios com ACLs do Linux vinculadas aos grupos de usuários do Samba.
6. **Print Server (`print_server`):** Inicializa o serviço CUPS e liberta a interface web administrativa.

---

## 🔍 Validação da Instalação

Após a conclusão da instalação, você pode validar o funcionamento dos serviços executando os comandos no servidor:

* **Verificar o Samba AD:**
  ```bash
  samba-tool domain level show
  smbclient -L localhost -U%
  ```

* **Testar Autenticação Kerberos:**
  ```bash
  kinit administrator@EMPRESA.LOCAL
  klist
  ```

* **Testar Resolução DNS:**
  ```bash
  host -t SRV _ldap._tcp.empresa.local
  ```

---

## 🤝 Contribuição

Contribuições são super bem-vindas! Se você deseja adicionar suporte a novas distribuições, novos módulos (como VPN ou Proxy) ou reportar um bug:

1. Faça um **Fork** do projeto.
2. Crie uma branch para sua funcionalidade (`git checkout -b feature/nova-funcionalidade`).
3. Faça o commit de suas alterações (`git commit -m 'Adiciona nova funcionalidade'`).
4. Envie para o seu repositório (`git push origin feature/nova-funcionalidade`).
5. Abra um **Pull Request**.

---

## 📜 Licença

Este projeto é distribuído sob a licença **MIT**. Veja o arquivo `LICENSE` para mais detalhes.
