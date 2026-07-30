# OpenInfra-Core
Criação de um infraestrutura de empresa/corporativo com ferramentas opensource

1. Stack Tecnológica Open Source Recomendada
Active Directory (AD DC): Samba4 (Substitui o Windows Server AD).

DNS: BIND9 (Integrado diretamente ao Samba4 para gerenciar as zonas do AD).

DHCP: ISC Kea DHCP (Substituto moderno do antigo isc-dhcp-server).

File Server: Samba (Configurado com ACLs do Linux integrado ao AD).

Print Server: CUPS (Common UNIX Printing System).

Root CA: Easy-RSA ou Step-CA (Para infraestrutura de chaves públicas interna).

NTP: Chrony (Crítico: o Kerberos do AD falha se o relógio dessincronizar mais de 5 minutos).

Automação e Teste: Ansible (Deploy) e Vagrant (Para laboratório local).

2. Estrutura de Diretórios do Repositório
Vagrantfile (Ambiente virtual de testes).

inventory.ini (Arquivo com os IPs dos servidores onde a instalação ocorrerá).

deploy.yml (Playbook mestre que chama todas as tarefas).

roles/ (Módulos de instalação independentes):

roles/01_ntp/

roles/02_samba_ad/

roles/03_dhcp/

roles/04_root_ca/

roles/05_file_server/

roles/06_print_server/

3. Exemplo do Playbook Principal (deploy.yml)
YAML
---
- name: Instalacao Completa do OpenInfra-Core
  hosts: domain_controllers
  become: yes
  roles:
    - 01_ntp
    - 02_samba_ad
    - 03_dhcp
    - 04_root_ca
    - 05_file_server
    - 06_print_server
4. Lógica e Ordem Exata de Instalação (Passo a Passo Automático)
Ajustar Rede e NTP: Configurar IP estático, hostname (ex: dc01.empresa.local) e forçar a sincronização de tempo via Chrony.

Provisionar o Domínio (Samba4): Rodar o comando samba-tool domain provision, definindo o nome do domínio, realm e a senha do Administrador.

Configurar o DNS (BIND9): Ajustar o arquivo named.conf para incluir os arquivos de zona gerados pelo Samba.

Configurar DHCP (Kea): Definir o escopo de rede e garantir que os clientes recebam o IP do servidor Samba como seu servidor DNS primário.

Criar a Root CA: Inicializar o Easy-RSA, gerar a chave privada e o certificado raiz (CRT) para espalhar via GPO ou script para as máquinas da rede.

Ativar Serviços Secundários: Levantar os compartilhamentos no smb.conf e instalar as filas de impressão no CUPS, gerenciando as permissões baseadas nos grupos criados no Samba4.

Fontes Consultadas
Samba Wiki Oficial (Configuração do AD DC): wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller

Documentação do Ansible: docs.ansible.com

ISC Kea DHCP Documentação: kea.isc.org

Guia de Servidores Ubuntu (NTP e CUPS): [ubuntu.com/server/docs](https://ubuntu.com/server/docs)

Easy-RSA GitHub Repository: [github.com/OpenVPN/easy-rsa](https://github.com/OpenVPN/easy-rsa)
