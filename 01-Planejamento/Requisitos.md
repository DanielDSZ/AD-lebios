# Requisitos do Projeto AD-Lab

**Versão:** 7.1 
**Status:** Rascunho para validação  
**Última atualização:** 2026-10-22  
**Plataforma-alvo:** Windows Server 2025  
**Ambiente de laboratório:** VirtualBox

> [!IMPORTANT]
> Este documento consolida o levantamento inicial para o projeto de Active Directory. Os endereços, nomes de cidades, responsáveis e contatos apresentados como exemplos devem ser substituídos pelos dados reais antes de qualquer implantação. Volumetria, links WAN, latências e requisitos de negócio ainda precisam de validação dos responsáveis.

| 2026-10-22 | 6.1 | Ajustes de consistência: distribuição por filial, usuários e dispositivos simultaneamente ativos, redação da D-01, nova D-10, nota sobre dependência em cadeia dos links L4 e L5. |
| 2026-10-22 | 6.0 | Reestruturação do documento em 14 seções, com fusos horários e referências. |
| 2026-10-22 | 5.0 | Consolidado o inventário de 660 estações/clientes e 25 servidores; documentadas as seis filiais, fusos horários, sub-redes, conectividade WAN, validações e decisões em aberto. |
| Anterior | 4.0 | Esqueleto inicial do levantamento; continha volumetria ilustrativa e campos placeholder. |

## Sumário

- [1. Contexto e objetivo](#1-contexto-e-objetivo)
- [2. Requisitos de negócio](#2-requisitos-de-negócio)
- [3. Inventário e volumetria](#3-inventário-e-volumetria)
- [4. Filiais e fusos horários](#4-filiais-e-fusos-horários)
- [5. Endereçamento IP](#5-endereçamento-ip)
- [6. Conectividade WAN](#6-conectividade-wan)
- [7. Dimensionamento global](#7-dimensionamento-global)
- [8. Administradores e delegação](#8-administradores-e-delegação)
- [9. Restrições do laboratório](#9-restrições-do-laboratório)
- [10. Premissas e pontos de validação](#10-premissas-e-pontos-de-validação)
- [11. Decisões em aberto](#11-decisões-em-aberto)
- [12. Diferenças entre laboratório e produção](#12-diferenças-entre-laboratório-e-produção)
- [13. Histórico de alterações](#13-histórico-de-alterações)
- [14. Referências de fuso horário](#14-referências-de-fuso-horário)

## 1. Contexto e objetivo

- **Cenário:** empresa multinacional com várias filiais e setores.
- **Objetivo:** levantar e documentar os requisitos para desenhar e implementar um ambiente Active Directory Domain Services (AD DS), usando boas práticas e reduzindo retrabalho.
- **Sistema operacional dos servidores de diretório:** Windows Server 2025.
- **Ambiente de estudo:** máquinas virtuais no VirtualBox.
- **Abrangência geográfica informada:** Brasil, Argentina, Uruguai e Chile.
- **Escopo inicial:** identidade, autenticação, estrutura lógica do AD, sites e sub-redes, delegação administrativa, DNS, replicação e resiliência a falhas de WAN.

O laboratório é uma simulação de arquitetura. As quantidades de equipamentos não significam, por si só, que todos os equipamentos serão executados simultaneamente no VirtualBox.

## 2. Requisitos de negócio

| ID | Requisito | Origem/parte interessada | Prioridade | Status |
|---|---|---|---|---|
| R-01 | Permitir autenticação na filial local quando o link WAN estiver indisponível, dentro dos limites técnicos do AD e do cache de credenciais aplicável. | Operações | Alta | A validar |
| R-02 | Permitir que a equipe local/regional administre os objetos autorizados sem conceder privilégios globais desnecessários. | RH/TI | Alta | A validar |
| R-03 | Garantir resolução DNS e localização adequada dos controladores de domínio para clientes em cada site. | Infraestrutura | Alta | Proposto |
| R-04 | Definir política de horário, sincronização e registro de eventos coerente entre sites e sistemas. | Infraestrutura/Segurança | Alta | Proposto |
| R-05 | Prever crescimento de usuários, dispositivos, aplicações integradas e filiais. | Negócio/TI | Média | A validar |

## 3. Inventário e volumetria

### 3.1 Estações de trabalho e clientes

| Sistema operacional/tipo | Quantidade | Situação de suporte informada | Risco relativo para integração com AD | Ação prevista |
|---|---:|---|---|---|
| Windows 11 Pro/Enterprise | 620 | Em suporte | Baixo | Ingressar no domínio, conforme edição, política e compatibilidade |
| macOS | 25 | Em suporte | Médio — requer método e políticas específicas | Validar método de integração e gestão |
| Linux (estações) | 10 | Em suporte | Médio | Avaliar integração com Kerberos/SSSD conforme distribuição |
| Thin clients e outros | 5 | Variável | Variável | Avaliar individualmente |
| **Total de estações/clientes** | **660** |  |  |  |

### 3.2 Servidores

| Sistema operacional/tipo | Quantidade | Situação de suporte informada | Risco relativo para integração com AD | Ação prevista |
|---|---:|---|---|---|
| Windows Server 2025 | 14 | Em suporte | Baixo | Integrar conforme função; inclui a previsão de futuros controladores de domínio |
| Linux (servidores) | 11 | Em suporte | Médio | Avaliar Kerberos/LDAP e requisitos de cada aplicação |
| **Total de servidores** | **25** |  |  |  |

### 3.3 Resumo consolidado

| Categoria | Quantidade |
|---|---:|
| Estações/clientes | 660 |
| Servidores | 25 |
| **Total de equipamentos inventariados** | **685** |

**Conferência aritmética:** 620 + 25 + 10 + 5 = **660 estações/clientes**.  
**Conferência de servidores:** 14 + 11 = **25 servidores**.  
**Total consolidado:** 660 + 25 = **685 equipamentos**.

> **Importante:** a quantidade de usuários não foi informada neste novo levantamento. Não se deve assumir que existem 660 usuários: a quantidade de dispositivos e a quantidade de pessoas são métricas diferentes. Levantar também usuários humanos, contas de serviço, contas administrativas, contas externas e aplicações que usarão o AD.

### 3.4 Premissas de inventário

- As quantidades representam o levantamento informado e devem ser confirmadas com o inventário corporativo.
- “Em suporte” foi mantido conforme informado; o ciclo de vida de cada versão/edição deve ser validado separadamente antes da implantação.
- A possibilidade de ingressar um dispositivo no domínio depende do sistema operacional, edição, configuração, ferramenta de gestão e requisito da aplicação.
- Linux pode usar Kerberos/LDAP/SSSD conforme a distribuição e a arquitetura; isso não significa que todo sistema Linux deva ser integrado da mesma forma.
- Thin clients e equipamentos especiais precisam ser avaliados individualmente.

### 3.5 Distribuição por filial

| Filial | Usuários humanos | Estações/clientes | Servidores | Observação |
|---|---:|---:|---:|---|
| BIOS-BH (Sede) | A levantar | A levantar | A levantar | |
| BIOS-SP | A levantar | A levantar | A levantar | |
| BIOS-RJ | A levantar | A levantar | A levantar | |
| BIOS-BSAS | A levantar | A levantar | A levantar | |
| BIOS-MVD | A levantar | A levantar | A levantar | |
| BIOS-RM | A levantar | A levantar | A levantar | |
| **Total** | **A levantar** | **660** | **25** | |

Regras de conferência:
- A soma de estações deve fechar com a seção 3.1 (660) e a de servidores com a 3.2 (25).
- Estações e servidores devem caber nas sub-redes da seção 5 (ver 5.2).
- Usuários humanos não devem ser deduzidos da quantidade de equipamentos.

### 3.6 Usuários e dispositivos simultaneamente ativos

Definições:
- **Usuário ativo:** sessão aberta no horário de pico da filial.
- **Dispositivo ativo:** estação em uso + servidores ligados 24 horas.
- Fórmulas: usuários ativos = usuários × presença no pico; dispositivos ativos = usuários ativos × dispositivos por usuário ativo + servidores.

| Filial | Padrão de trabalho | Presença no pico | Dispositivos por usuário ativo | Usuários ativos no pico | Dispositivos ativos no pico |
|---|---|---:|---:|---:|---:|
| BIOS-BH (Sede) | A levantar | A levantar | A levantar | A calcular | A calcular |
| BIOS-SP | A levantar | A levantar | A levantar | A calcular | A calcular |
| BIOS-RJ | A levantar | A levantar | A levantar | A calcular | A calcular |
| BIOS-BSAS | A levantar | A levantar | A levantar | A calcular | A calcular |
| BIOS-MVD | A levantar | A levantar | A levantar | A calcular | A calcular |
| BIOS-RM | A levantar | A levantar | A levantar | A calcular | A calcular |

Observações:
- Cinco das seis filiais estão em UTC−03:00 e o Chile varia sazonalmente entre UTC−03:00 e UTC−04:00 (seção 4.2). Os picos de logon tendem a **coincidir**, então a soma dos picos locais é uma estimativa conservadora do pico global.
- Registrar a origem de cada percentual (medição, estimativa, política de turnos).

### 3.7 Sistemas e serviços que autenticam no AD (PLACEHOLDER)
Ver planilha `Sistemas-AD.xlsx`.

Resumo: 14 itens catalogados em 5 categorias (arquivos, impressão, aplicações internas, VPN, serviços de rede).
Pontos de atenção:
- Itens com método ainda não confirmado: S-05, S-06 (a validar com os donos).
- Itens que podem exigir extensão de schema: nenhum identificado até agora (a validar).
- Dependência entre filiais: S-04 e S-05 rodam na sede e atendem todas as filiais pela WAN.



## 4. Filiais e fusos horários

### 4.1 Cadastro inicial de filiais

| ID | Filial | Região/UF informada | País | Endereço informado | Responsável local | Contato |
|---|---|---|---|---|---|---|
| F1 | BIOS-BH (Sede) | MG | Brasil | Rua Exemplo 100, Cidade-A | Coordenador de TI | Cargo/e-mail genérico |
| F2 | BIOS-SP | SP | Brasil | Av. Exemplo 200, Cidade-B | Técnico de TI local | Genérico |
| F3 | BIOS-RJ | RJ | Brasil | Exemplo Street 10, Cidade-C | Técnico de TI local | Genérico |
| F4 | BIOS-BSAS | BSAS | Argentina | Exemplo Platz 5, Cidade-D | Técnico de TI local | Genérico |
| F5 | BIOS-MVD | MVD | Uruguai | Exemplo Road 30, Cidade-E | Técnico de TI local | Genérico |
| F6 | BIOS-RM | RM | Chile | Exemplo Ave 40, Cidade-F | Técnico de TI local | Genérico |

> Os endereços e nomes de cidades são exemplos fornecidos para o levantamento, não endereços confirmados. Para validar o fuso de cada filial, confirmar a cidade e a região efetivas, principalmente no Chile.

### 4.2 Fusos horários de referência

| Filial | Localidade de referência | Fuso de referência | Situação em 22/10/2026 | Observações |
|---|---|---|---|---|
| BIOS-BH | Minas Gerais, Brasil | `America/Sao_Paulo` — UTC−03:00 | UTC−03:00 | Minas Gerais segue o horário de Brasília; sem horário de verão em vigor na data desta revisão. |
| BIOS-SP | São Paulo, Brasil | `America/Sao_Paulo` — UTC−03:00 | UTC−03:00 | Mesmo horário de BH e RJ. |
| BIOS-RJ | Rio de Janeiro, Brasil | `America/Sao_Paulo` — UTC−03:00 | UTC−03:00 | Mesmo horário de BH e SP. |
| BIOS-BSAS | Buenos Aires, Argentina | `America/Argentina/Buenos_Aires` — UTC−03:00 | UTC−03:00 | Confirmar a cidade real da filial. |
| BIOS-MVD | Montevidéu, Uruguai | `America/Montevideo` — UTC−03:00 | UTC−03:00 | Confirmar a cidade real da filial. |
| BIOS-RM | Santiago, Chile (referência provisória) | `America/Santiago` | UTC−03:00 | O Chile continental aplica mudança sazonal: a regra publicada para 2026–2029 prevê UTC−03:00 no período iniciado em setembro e UTC−04:00 fora desse período. Validar a cidade/região efetiva. |

**Conclusão do levantamento de fusos:** na data desta revisão, as seis localidades de referência estão em UTC−03:00. Isso **não** significa que permanecerão sempre no mesmo horário: Santiago, no Chile continental, tem mudança sazonal prevista. Não configure um deslocamento UTC fixo manualmente para representar um fuso geográfico.

### 4.3 Requisitos de horário para o AD

- Usar fusos nomeados do sistema operacional, em vez de configurar manualmente um deslocamento UTC fixo.
- Definir e documentar a hierarquia de sincronização de horário do domínio. Em um domínio AD, o serviço Windows Time e a hierarquia do domínio precisam ser planejados; os controladores de domínio não devem ser configurados de forma independente e inconsistente.
- Definir uma fonte de tempo confiável para o controlador que detenha a função PDC Emulator do domínio raiz da floresta e documentar a fonte externa aprovada.
- Registrar horários de eventos de forma consistente e considerar o UTC para correlação de logs entre países.
- Revalidar regras de fuso horário após mudanças legais, atualização do sistema operacional ou mudança da localização física da filial.

## 5. Endereçamento IP

As tabelas abaixo preservam os blocos e as sub-redes informados. A contagem de hosts utilizáveis considera IPv4 convencional, subtraindo endereço de rede e broadcast. Reservas de gateway, endereços estáticos, DHCP, interfaces redundantes e crescimento precisam ser considerados no planejamento final.

### 5.1 Blocos e sub-redes por filial

| Filial | Bloco reservado à filial | Sub-rede | Finalidade | Hosts IPv4 utilizáveis |
|---|---|---|---|---:|
| BIOS-BH (Sede) | `10.10.0.0/20` | `10.10.0.0/23` | Usuários/estações | 510 |
| BIOS-BH (Sede) | `10.10.0.0/20` | `10.10.2.0/24` | Servidores | 254 |
| BIOS-SP | `10.10.16.0/20` | `10.10.16.0/24` | Usuários/estações | 254 |
| BIOS-SP | `10.10.16.0/20` | `10.10.17.0/26` | Servidores | 62 |
| BIOS-RJ | `10.20.0.0/20` | `10.20.0.0/24` | Usuários/estações | 254 |
| BIOS-RJ | `10.20.0.0/20` | `10.20.1.0/26` | Servidores | 62 |
| BIOS-BSAS | `10.20.16.0/20` | `10.20.16.0/24` | Usuários/estações | 254 |
| BIOS-BSAS | `10.20.16.0/20` | `10.20.17.0/26` | Servidores | 62 |
| BIOS-MVD | `10.30.0.0/20` | `10.30.0.0/24` | Usuários/estações | 254 |
| BIOS-MVD | `10.30.0.0/20` | `10.30.1.0/26` | Servidores | 62 |
| BIOS-RM | `10.30.16.0/20` | `10.30.16.0/24` | Usuários/estações | 254 |
| BIOS-RM | `10.30.16.0/20` | `10.30.17.0/26` | Servidores | 62 |

### 5.2 Validação de endereçamento

- As máscaras e quantidades de hosts informadas estão aritmeticamente corretas: `/23` = 510; `/24` = 254; `/26` = 62 hosts IPv4 utilizáveis.
- As sub-redes listadas não se sobrepõem entre si e cabem nos respectivos blocos `/20` indicados.
- O bloco `/20` de cada filial é uma reserva agregada; não deve ser configurado como se fosse uma única LAN quando as sub-redes de usuários e servidores são distintas.
- As redes de usuários e servidores devem ser separadas por VLAN/sub-rede conforme a arquitetura de rede e as políticas de segurança.
- Reservar e documentar endereços de gateway, DHCP, DNS, controladores de domínio, infraestrutura, impressoras e outros serviços.
- Antes da implantação, comparar este plano com as rotas corporativas, VPNs, redes de terceiros e faixas já utilizadas. A ausência de sobreposição nesta tabela não garante ausência de conflito na rede real.
- Para configurar **Active Directory Sites and Services**, associar cada sub-rede ao site AD correto e documentar eventuais sub-redes adicionais.

## 6. Conectividade WAN

| Link | Origem | Destino | Tipo | Banda informada | Latência típica informada | SLA informado | Backup |
|---|---|---|---|---:|---:|---:|---|
| L1 | BIOS-BH (Sede) | BIOS-SP | MPLS | 200 Mbps | 20 ms | 99,9% | VPN internet |
| L2 | BIOS-BH (Sede) | BIOS-RJ | MPLS | 200 Mbps | 120 ms | 99,9% | VPN internet |
| L3 | BIOS-BH (Sede) | BIOS-MVD | MPLS | 200 Mbps | 230 ms | 99,5% | VPN internet |
| L4 | BIOS-RJ | BIOS-BSAS | VPN internet | 50 Mbps | 25 ms | 99,0% | Nenhum informado |
| L5 | BIOS-MVD | BIOS-RM | VPN internet | 20 Mbps | 40 ms | 98,5% | Nenhum informado |

**Observações para validação:**

- Os valores são os fornecidos no levantamento e ainda não foram confirmados por medição ou contrato.
- A latência de `120 ms` no link BIOS-BH–BIOS-RJ é um valor a confirmar, pois as localidades são ambas no Brasil; não foi alterado sem evidência.
- Validar se o SLA representa disponibilidade contratual do circuito, da VPN ou do serviço ponta a ponta.
- Confirmar capacidade de tráfego, perda de pacotes, jitter, MTU, roteamento, redundância e comportamento de failover.
- O desenho de replicação do AD deve considerar largura de banda, latência, disponibilidade, criticidade dos serviços e topologia real. Não decidir o número de controladores de domínio apenas com base na quantidade de equipamentos.
- Os links sem backup informado representam um ponto de atenção de disponibilidade e devem ser avaliados com o responsável pela WAN.
- **Dependência em cadeia:** BIOS-BSAS chega à sede por L4 via BIOS-RJ, e BIOS-RM por L5 via BIOS-MVD. L4 e L5 não têm backup informado e têm os menores SLAs (99,0% e 98,5%). Avaliar DC local, controlador somente leitura (RODC) ou link de contingência para essas duas filiais.
- A latência de 120 ms em L2 (BH–RJ) segue marcada como valor a confirmar.

## 7. Dimensionamento global

| Item | Estimativa atual | Observações |
|---|---:|---|
| Estações/clientes | 660 | 620 Windows, 25 macOS, 10 Linux e 5 thin clients/outros |
| Servidores | 25 | 14 Windows Server 2025 e 11 Linux |
| Equipamentos totais | 685 | Estações/clientes + servidores |
| Filiais | 6 | Cadastro inicial em quatro países |
| Aplicações com autenticação AD | A levantar | O valor anterior de 18 era placeholder e não foi confirmado |
| Usuários humanos | A levantar | Não inferir a partir da quantidade de equipamentos |
| Contas de serviço | A levantar | Inventariar separadamente, com responsáveis e finalidade |
| OUs | A definir | Estrutura depende do modelo administrativo e dos requisitos de delegação |
| Controladores de domínio | A definir | Definir após análise de disponibilidade, autenticação local, DNS e conectividade |
| Sites do AD | 6 como hipótese inicial | Validar com a topologia de rede e as sub-redes reais |

## 8. Administradores e delegação

A distribuição anterior de administradores era ilustrativa e precisa ser confirmada. Como referência inicial para o levantamento:

| Categoria | Quantidade preliminar | Observações |
|---|---:|---|
| Administração de floresta/esquema | 2 | Privilégio altamente restrito; contas de uso administrativo separadas das contas comuns |
| Administradores regionais | 3 | Escopo e regiões de responsabilidade a definir |
| Help desk com delegação | 6 | Apenas tarefas necessárias, como redefinição de senha e desbloqueio |
| Auditoria — somente leitura | 1 | Validar escopo, ferramenta e requisitos de auditoria |
| **Total preliminar** | **12** | Estimativa não aprovada |

Boas práticas a considerar no desenho:

- Aplicar o princípio do menor privilégio e delegar permissões por função e escopo.
- Evitar o uso cotidiano de contas com privilégios de floresta/domínio.
- Definir processo de concessão, revisão periódica e remoção de privilégios.
- Documentar contas de emergência (*break-glass*), seu controle e seu monitoramento, sem utilizá-las para tarefas rotineiras.
- Validar se a quantidade de administradores corresponde à operação real e à cobertura por fuso horário.

## 9. Restrições do laboratório

- Memória RAM disponível: 20 GB.
- CPUs disponíveis: 4.
- Armazenamento disponível: 1 TB.
- Limite operacional: no máximo 4 VMs simultâneas.
- Plataforma de virtualização: VirtualBox.
- A simulação de WAN, latência, perda de pacotes e indisponibilidade de links exige configuração e testes específicos; redes internas do VirtualBox, por si só, não reproduzem automaticamente as condições de uma WAN real.

## 10. Premissas e pontos de validação

1. **Inventário:** confirmar as quantidades e o estado de suporte com os responsáveis por cada filial.
2. **Usuários:** obter o total de funcionários, terceiros, contas administrativas, contas de serviço e usuários externos.
3. **Aplicações:** inventariar aplicações que usam LDAP, Kerberos, NTLM, SAML/OIDC via provedor de identidade ou outros métodos; não presumir que todas se integram diretamente ao AD DS.
4. **Filiais:** substituir endereços e localidades de exemplo pelos dados reais.
5. **Fusos horários:** confirmar as cidades exatas e revisar regras sazonais do Chile.
6. **Redes:** validar gateways, VLANs, DHCP, DNS, rotas, VPNs e possíveis redes sobrepostas fora deste documento.
7. **WAN:** confirmar latência, banda, SLA e existência de circuitos de contingência.
8. **Disponibilidade:** detalhar o que significa “autenticar com a WAN indisponível” para cada tipo de usuário, dispositivo e aplicação.
9. **Segurança:** definir requisitos de MFA para acessos administrativos, proteção de contas privilegiadas, auditoria, backup e recuperação.
10. **Crescimento:** estimar a evolução de usuários, dispositivos, filiais e aplicações para os próximos 3–5 anos.

## 11. Decisões em aberto

| ID | Decisão | Depende de | Impacto |
|---|---|---|---|
| D-01 | Domínio único com OUs e delegação **ou** domínios filhos regionais, dentro de uma única floresta (floresta única é decisão de projeto) | Requisitos administrativos, segurança e operação | Complexidade, administração, replicação e autonomia |
| D-02 | Namespace DNS/AD | Domínio público existente, estratégia de DNS e identidade | Compatibilidade, resolução de nomes e integrações |
| D-03 | Número e localização dos sites do AD | Topologia de rede e sub-redes confirmadas | Localização de DCs e replicação |
| D-04 | Quantidade e posicionamento dos controladores de domínio | Requisitos de disponibilidade, autenticação local e capacidade | Resiliência, custo e operação |
| D-05 | Modelo de DNS e encaminhamento entre filiais | Namespace e arquitetura de resolução | Autenticação e descoberta de serviços |
| D-06 | Fonte e hierarquia de horário | Política corporativa e requisitos de auditoria | Kerberos, logs e correlação de eventos |
| D-07 | Integração de macOS e Linux | Distribuições, versões, gestão e aplicações | Experiência de autenticação e suporte |
| D-08 | Backup e recuperação de AD DS | RPO/RTO, criticidade e infraestrutura disponível | Recuperação de desastre |
| D-09 | Estratégia de links redundantes | Contratos WAN e orçamento | Continuidade de autenticação e replicação |
| D-10 | Critério de regionalização dos domínios filhos (por país ou por grupo de países) | Autonomia administrativa, requisitos legais de dados, limite de 4 VMs do laboratório | Número mínimo de DCs (ver nota abaixo) |

> Não utilizar `corp.example.com` como namespace definitivo sem decisão formal. É um exemplo de documentação, não uma recomendação final para o ambiente real.
Nota sobre D-10 (mínimo de 1 DC por domínio, sem redundância):
- Raiz dedicada + domínios por país (4): 5 DCs, acima do limite de 4 VMs.
- Raiz dedicada + 2 grupos (por exemplo Brasil e Cone Sul): 3 DCs.
- Raiz = um dos domínios regionais + domínios por país: 4 DCs, ocupa todas as VMs e não sobra VM para cliente.
- Raiz = um dos domínios regionais + 2 grupos: 2 DCs.
Em produção, o mínimo seria 2 DCs por domínio; no laboratório, a redundância será simulada por rodízio de VMs ou por testes pontuais.

## 12. Diferenças entre laboratório e produção

- O laboratório possui recursos limitados e não comportará necessariamente a arquitetura de produção inteira ao mesmo tempo.
- Testes de falha devem ser planejados para validar autenticação, DNS, replicação, sincronização de horário e recuperação.
- Resultados obtidos em um laboratório virtual não substituem testes de carga, segurança, conectividade e recuperação no ambiente corporativo.
- Em produção, dimensionamento e posicionamento de DCs dependem de disponibilidade exigida, quantidade e distribuição de usuários, aplicações, links, segurança física e operação local.
- Mudanças em DNS, DHCP, rotas, firewall e horário devem seguir processo de mudança aprovado.

## 13. Histórico de alterações

| Data | Versão | Alteração |
|---|---|---|
| 2026-10-22 | 5.0 | Consolidado o inventário de 660 estações/clientes e 25 servidores; documentadas as seis filiais, fusos horários, sub-redes, conectividade WAN, validações e decisões em aberto. |
| Anterior | 4.0 | Esqueleto inicial do levantamento; continha volumetria ilustrativa e campos placeholder. |

## 14. Referências de fuso horário

- Observatório Nacional (Brasil) — [Fusos horários](https://www.gov.br/observatorio/pt-br/assuntos/areas-de-atuacao/tempo-e-frequencia/fusos-horarios).
- Directemar / autoridade marítima do Chile — [Cambio de hora en Chile Continental e Insular Occidental](https://www.directemar.cl/cambio-de-hora-en-chile-continental-e-insular-occidental-0).
- Biblioteca del Congreso Nacional de Chile — [Decreto 98 de 2026](https://www.bcn.cl/leychile/Navegar?idNorma=1225724&idParte=10600879&idVersion=2026-07-02).

**Nota:** os fusos e as regras sazonais podem ser alterados por legislação. Revalidar os dados antes da implantação e manter os sistemas operacionais atualizados com as regras de fuso horário.
