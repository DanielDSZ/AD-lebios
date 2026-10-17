\# Requisitos do Projeto AD-Lab



\*\*Versão:\*\* 0.1 | \*\*Status:\*\* rascunho | \*\*Última atualização:\*\* AAAA-MM-DD



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

(A preencher nas etapas seguintes: volumetria, dispositivos por filial,

sub-redes e links WAN, sistemas operacionais, sistemas de negócio.)



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

(Registrar aqui o que o lab não consegue simular.)



\## 8. Histórico de mudanças

| Data | Mudança |

|------|---------|

| AAAA-MM-DD | Criação do esqueleto |

