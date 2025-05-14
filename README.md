# Criando-VM-na-Azure
Criação e configuração de VM na Azure, documentados para fins de consulta futura e como desafio proposto pela DIO no bootcamp "XP inc cloud com inteligencia artificial".

# Provisionamento de Máquina Virtual Linux Server na Azure Utilizando o Nível Gratuito (Free Tier)

**Data de Criação do Documento:** 13 de maio de 2025

## Sumário -

Este documento descreve o procedimento técnico para o provisionamento e configuração inicial de uma Máquina Virtual (VM) baseada em Linux Server na plataforma Microsoft Azure, com foco exclusivo na utilização de recursos elegíveis ao "Free Tier". O objetivo é fornecer um guia detalhado e reprodutível, adequado para fins acadêmicos, experimentação de serviços IaaS (Infrastructure as a Service) e como material de referência para projetos universitários, como o desafio proposto pela DIO no bootcamp "XP Inc. Cloud com Inteligência Artificial".

## 1. Pré-requisitos -

- **Conta Azure com Nível Gratuito (Free Tier) Ativo:** Necessária para acesso aos serviços e benefícios gratuitos. Detalhes em [azure.microsoft.com/free](https://azure.microsoft.com/free).
- **Familiaridade com o Portal Azure:** Conhecimento básico da interface de gerenciamento.
- **Cliente SSH:**
    - Para Linux/macOS: OpenSSH (geralmente nativo).
    - Para Windows: OpenSSH (disponível no PowerShell/CMD moderno) ou PuTTY.
- **Conceitos Básicos de Redes e Sistemas Operacionais Linux.**

## 2. Metodologia de Provisionamento da VM

O processo de provisionamento será conduzido integralmente através do Portal Azure.

### 2.1. Estabelecimento da Conta Azure

A criação de uma conta no Nível Gratuito da Azure é o passo inicial. Este processo envolve:

1. Navegar até [azure.microsoft.com](https://azure.microsoft.com/) e selecionar a opção de criação de conta gratuita.
2. Prover as informações solicitadas, incluindo um método de verificação de identidade (cartão de crédito, que não será cobrado dentro dos limites do nível gratuito).
3. Após a confirmação, realizar login no Portal Azure ([portal.azure.com](https://portal.azure.com/)).

### 2.2. Criação e Configuração da Máquina Virtual

No Portal Azure, o fluxo de criação da VM é iniciado pela busca do serviço "Máquinas Virtuais" e subsequente seleção de "Criar" > "Máquina virtual do Azure".

### 2.2.1. Configurações Fundamentais (Aba "Básico")

- **Assinatura:** Deve ser selecionada a assinatura associada ao Nível Gratuito.
- **Grupo de Recursos:**
    - É imperativo criar um novo grupo de recursos para encapsular todos os artefatos da VM, facilitando o gerenciamento e a eventual desmobilização.
    - **Ação:** Clicar em "Criar novo".
    - **Nomenclatura Sugerida:** `rg-linux-freetier-lab` (ou similar, seguindo uma convenção de nomenclatura).
- **Detalhes da Instância:**
    - **Nome da máquina virtual:** Um identificador único para a VM (e.g., `vm-ubuntu-server-01`).
    - **Região:** Selecionar uma região que ofereça explicitamente os serviços gratuitos desejados (e.g., `East US`, `West Europe`). A disponibilidade de instâncias `B1s` no nível gratuito deve ser confirmada para a região escolhida. A região `Brazil South` pode ser uma opção, sujeita à disponibilidade dos SKUs gratuitos.
    - **Opções de disponibilidade:** Para fins de aprendizado e uso do nível gratuito, a opção `Nenhuma redundância de infraestrutura necessária` é adequada.
    - **Tipo de segurança:** Manter o padrão `Standard`.
    - **Imagem:** Selecionar uma distribuição Linux elegível para o nível gratuito.
        - **Recomendação:** `Ubuntu Server 20.04 LTS - x64 Gen2` ou `Ubuntu Server 22.04 LTS - x64 Gen2`. A versão `Ubuntu Server 24.04 LTS - x64 Gen2` também pode ser uma opção, verificar a elegibilidade no momento da seleção do tamanho.
        - **Arquitetura:** `x64`.
    - **Tamanho da VM:** **Ponto Crítico para o Nível Gratuito.**
        - **Ação:** Clicar em "Ver todos os tamanhos".
        - **Seleção:** Filtrar e selecionar o SKU `B1s` (1 vCPU, 1 GiB RAM), que é comumente incluído na oferta de 12 meses do nível gratuito. A elegibilidade é geralmente indicada no portal.
- **Conta de Administrador:**
    - **Tipo de autenticação:**
        - **Chave pública SSH (Recomendado):**
            - **Nome de usuário:** Definir um nome de usuário (e.g., `adminuser`).
            - **Origem da chave pública SSH:** `Gerar novo par de chaves`.
            - **Nome do par de chaves:** Atribuir um nome (e.g., `vm-ubuntu-server-01-key`). **O download da chave privada (.pem) será solicitado ao final do processo de criação e é crucial para o acesso.**
        - **Senha (Alternativa):**
            - Definir **Nome de usuário** e uma **Senha** robusta, aderente às políticas de complexidade do Azure.
- **Regras de Porta de Entrada:**
    - **Portas de entrada públicas:** `Permitir portas selecionadas`.
    - **Selecionar portas de entrada:** Habilitar `SSH (22)`.

### 2.2.2. Configuração de Discos (Aba "Discos")

- **Tipo de disco do SO:**
    - Para manter a gratuidade, selecionar `SSD Standard` (ou HDD Standard, se for uma opção mais econômica e disponível no nível gratuito para a região). A oferta gratuita geralmente inclui uma cota de armazenamento SSD Standard (e.g., 2 x 64 GiB P6).
- **Gerenciamento de chaves:** Manter `Chave gerenciada pela plataforma`.
- **Discos de Dados:** Para esta configuração básica, discos de dados adicionais não são inicialmente necessários. Caso sejam, observar os limites do nível gratuito para tipo e tamanho.

### 2.2.3. Configuração de Rede (Aba "Rede")

- **Rede Virtual, Sub-rede, IP Público, NSG da NIC:**
    - O Azure propõe a criação de novos recursos com configurações padrão, geralmente adequadas.
    - **IP Público:** Um novo IP público (SKU Básico, Dinâmico) será criado, o que se enquadra no nível gratuito. IPs públicos estáticos ou de SKU Standard podem incorrer em custos.
    - **NSG (Network Security Group) da NIC:** Selecionar `Básico`. A regra para permitir tráfego na porta `TCP/22` (SSH), configurada anteriormente, será automaticamente adicionada.
- **Balanceamento de carga:** Selecionar `Nenhum`.

### 2.2.4. Configurações de Gerenciamento (Aba "Gerenciamento")

- **Microsoft Defender para Nuvem:** Avaliar a necessidade. O nível gratuito do Defender pode oferecer funcionalidades básicas de postura de segurança.
- **Diagnóstico de inicialização:** `Habilitar com conta de armazenamento gerenciado (recomendado)`. Os custos de armazenamento para logs de diagnóstico são geralmente marginais para VMs de laboratório.
- **Desligamento automático:** Configurar se desejado para otimização de custos, definindo um horário para desligamento e um fuso horário.

### 2.2.5. Configurações Avançadas e Marcas (Abas "Avançado" e "Marcas")

- **Avançado:** Nenhuma configuração adicional é tipicamente requerida para um provisionamento básico.
- **Marcas (Tags):** Recomenda-se o uso para organização e rastreamento de custos, mesmo em ambientes de laboratório.
    - Exemplos: `Projeto:BootcampDIO`, `Ambiente:LabFreeTier`, `Owner:SeuNome`.

### 2.2.6. Revisão e Criação

1. Navegar para a aba **"Revisar + criar"**.
2. O Azure realizará uma validação final das configurações. Corrigir quaisquer erros apontados.
3. Analisar o resumo, confirmando o tamanho da VM (`B1s`), tipo de disco e a estimativa de custo (deve ser R$0,00 ou valor muito próximo, indicando o uso do nível gratuito).
4. Clicar em **"Criar"**.
5. **Atenção (Caso tenha optado por chave SSH gerada):** Um prompt para **baixar a chave privada (.pem)** será exibido. Realize o download imediatamente e armazene-a em um local seguro. Esta chave é insubstituível e essencial para o acesso SSH.

## 3. Acesso à Máquina Virtual Pós-Provisionamento

Após a conclusão da implantação:

1. Navegue até o recurso da VM no Portal Azure.
2. Na seção "Visão geral", localize e copie o **"Endereço IP público"**.
3. **Conexão via SSH:**
    - **Utilizando chave SSH:**
    Substitua `/path/to/your-private-key.pem` pelo caminho do arquivo da chave privada, `<username>` pelo nome de usuário configurado, e `<public_ip_address>` pelo IP obtido.
        
        ```bash
        ssh -i /path/to/your-private-key.pem <username>@<public_ip_address>
        
        ```
        
        - **Permissões da Chave Privada (Linux/macOS):** `chmod 400 /path/to/your-private-key.pem`
        - **Permissões da Chave Privada (Windows - usando OpenSSH via PowerShell/CMD):**
            
            ```powershell
            icacls.exe <path_to_your_private_key.pem> /reset
            icacls.exe <path_to_your_private_key.pem> /grant:r "$($env:USERNAME):(R)"
            icacls.exe <path_to_your_private_key.pem> /inheritance:r
            
            ```
            
    - **Utilizando senha:**
    Será solicitada a senha definida durante a criação da VM.
        
        ```bash
        ssh <username>@<public_ip_address>
        
        ```
        
4. Na primeira conexão, confirme a autenticidade do host digitando `yes`.

## 4. Gerenciamento de Recursos e Otimização de Custos (Nível Gratuito)

- **Parada (Desalocada) da VM:** Para evitar o consumo desnecessário das horas de computação gratuitas, quando a VM não estiver em uso, utilize a opção "Parar" no portal Azure. Isso desaloca os recursos de computação. O armazenamento em disco associado permanece e pode consumir parte da cota gratuita de armazenamento.
- **Monitoramento de Consumo:** Utilize o serviço "Gerenciamento de Custos + Cobrança" no Portal Azure para acompanhar o uso dos serviços e evitar exceder os limites do nível gratuito.
- **Desmobilização de Recursos:** Ao concluir o uso, exclua o Grupo de Recursos criado. Esta ação removerá todos os recursos contidos (VM, disco, IP público, NSG, etc.), prevenindo quaisquer cobranças futuras.

## 5. Conclusão

Este documento detalhou o processo de provisionamento de uma VM Linux Server na Azure, aderindo estritamente aos recursos do Nível Gratuito. A metodologia apresentada permite a criação de um ambiente de laboratório funcional para desenvolvimento, testes e aprendizado em computação em nuvem, sem incorrer em custos iniciais, desde que os limites da oferta gratuita sejam respeitados.

---
