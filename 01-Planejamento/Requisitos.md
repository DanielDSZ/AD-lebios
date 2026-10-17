\# Requisitos do Projeto AD-Lab



\*\*Versão:\*\* 0.3 | \*\*Status:\*\* rascunho | \*\*Última atualização:\*\* AAAA-MM-DD



\## 1. Contexto e objetivo

\- Cenário: empresa multinacional, 500+ funcionários, várias filiais e setores.

\- Objetivo de aprendizado: construir o desenho complexo de propósito.

\- Plataforma: Windows Server 2025 em VMs (VirtualBox).



\## 2. Requisitos de negócio

| ID | Requisito | Origem | Prioridade |

|----|-----------|--------|------------|

| R-01 | (PLACEHOLDER) Usuário autentica na própria filial mesmo com WAN fora | Operações | Alta |

| R-02 | (PLACEHOLDER) TI local de cada região administra seus usuários | RH/TI | Alta |



\## 3. Requisitos técnicos



\### 3.1 Volumetria estimada (PLACEHOLDER: valores de exemplo)



| Filial | Região | Usuários | Computadores | Servidores |

|--------|--------|----------|--------------|------------|

| AMER-1 (sede) | AMER | 220 | 242 | 10 |

| AMER-2 | AMER | 60 | 66 | 2 |

| EMEA-1 | EMEA | 120 | 132 | 5 |

| EMEA-2 | EMEA | 80 | 88 | 3 |

| APAC-1 | APAC | 70 | 77 | 3 |

| APAC-2 | APAC | 50 | 55 | 2 |

| \*\*Total\*\* | | \*\*600\*\* | \*\*660\*\* | \*\*25\*\* |



Faixa (horizonte 3-5 anos): usuários 550 / 600 / 750 (mín / esperado / máx).

Método: usuários = funcionários + contratados (premissa); computadores ≈ usuários × 1,1.

Confiança: baixa (cenário simulado).



\### 3.2 Itens de dimensionamento global



| Item | Estimativa | Faixa | Observação |

|------|------------|-------|------------|

| Aplicações com autenticação AD | 18 | 15-25 | Detalhar na etapa 1.1.7 |

| Filiais | 6 | 6-8 | 3 regiões, 2 filiais por região |

| OUs (preliminar) | 35 | 20-60 | Depende de D-01; refinar no desenho |

| Administradores (total) | 12 | 10-18 | Ver divisão abaixo |



\### 3.3 Administradores por categoria (PLACEHOLDER)



| Categoria | Qtde |

|-----------|------|

| Enterprise/Schema (nível floresta) | 2 |

| Administradores regionais | 3 |

| Help desk com delegação (reset de senha etc.) | 6 |

| Auditoria (somente leitura) | 1 |

| \*\*Total\*\* | \*\*12\*\* |



\### 3.4 Premissas desta seção

\- Todos os números são estimativas de cenário simulado.

\- Usuário inclui contratados; contas de serviço contadas à parte (a estimar).



\## 4. Restrições

\- Lab: 20 GB RAM, 4 CPUs, 1 TB de disco, no máximo 4 VMs simultâneas.

\- Limitações do VirtualBox para simular WAN/latência (ver seção 7).



\## 5. Premissas

\- (PLACEHOLDER) Cada filial tem link de pelo menos 10 Mbps.



\## 6. Decisões em aberto

| ID | Decisão | Depende de | Impacto |

|----|---------|------------|---------|

| D-01 | Domínios filhos vs. OUs com delegação | R-01, R-02 | Número de DCs, replicação |

| D-02 | Namespace (PLACEHOLDER: corp.example.com) | Domínio registrado/DNS | Nomes de domínios filhos |

| D-03 | Quantidade de sites e sub-redes | Levantamento de filiais | Topologia de replicação |



\## 7. Diferenças lab vs. produção

O VirtualBox consegue simular várias redes internas, mas latência e perda de pacotes de WAN real não são simuladas bem por padrão.



\## 8. Histórico de mudanças

| Data | Mudança |

|------|---------|

| AAAA-MM-DD | Criação do esqueleto |

