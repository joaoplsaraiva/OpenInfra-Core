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
