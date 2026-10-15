\# Requisitos — Implantação de Active Directory (AD-Lab)

Versão 0.3 | Data: 14/10/2026 | Status: Rascunho



\## 1. Objetivo

Simular a implantação de AD DS para empresa fictícia com +500 usuários,

múltiplas filiais e setores, seguindo boas práticas Microsoft, em VirtualBox.



\## 2. Escopo

Dentro: AD DS, DNS integrado, DHCP, Sites e Serviços, OUs, GPOs base,

delegação, backup/restauração do AD, hardening básico.

Fora (por ora): Exchange, ADFS, PKI/AD CS, Entra Connect, VPN real entre filiais.



\## 3. Premissas do cenário simulado

\- Usuários: 500+ (alvo 700)

\- Contas de Serviço 25 (alvo 40)

\- Computadores: 650 (alvo 820)

\- Contas administrativas 20

\- Servidores 30

\- Aplicações integradas ao AD

\- Sub-redes 8

\- OUs 45

\- Administradores (pessoas) 20

\- Sedes: 1 matriz + 3 filiais

* Setores: RH, Financeiro, TI, Comercial, Operações, Jurídico, DP, Diretoria

\- Link entre filiais: simulado (rede interna do VirtualBox)

### 3.1 Simultaneidade por filial (v0.3)

| Filial | Usuários | Fator | Simultâneos | Disp. ativos | Pico logon (15 min) |

|--------|----------|-------|-------------|--------------|---------------------|

| Matriz | 303 | 85% | 258 | 284 | 155 |

| Filial A | 110 | 80% | 88 | 97 | 53 |

| Filial B | 82 | 75% | 62 | 68 | 37 |

| Filial C | 55 | 70% | 39 | 43 | 23 |

Hipóteses: dispositivos = simultâneos × 1,1; pico = 60% dos simultâneos em 15 min.

Horário de pico: 08:00–08:30. Turnos: a definir por filial.



\## 4. Requisitos funcionais

\- RF01 Autenticação centralizada (Kerberos/NTLM somente se necessário)

\- RF02 DNS integrado ao AD, zonas seguras

\- RF03 Um DC local (ou RODC) em cada site relevante

\- RF04 Estrutura de OUs por função, não por organograma político

\- RF05 Delegação de tarefas ao help desk sem Domain Admin

\- RF06 GPOs de baseline por tipo de objeto

\- RF07 Contas nomeadas (sem uso de Administrator no dia a dia)



\## 5. Requisitos não funcionais

\- Disponibilidade: ≥2 DCs na matriz; sem ponto único de falha de AD/DNS

\- Segurança: modelo de administração em camadas; LAPS; senhas/lockout definidas

\- Backup: System State dos DCs com teste de restauração documentado

\- Tempo: PDC Emulator sincronizado com fonte externa; demais por hierarquia

\- Auditoria: logs de eventos críticos habilitados



\## 6. Decisões de arquitetura (alvos, a validar na etapa de design)

\- Modelo: 1 floresta, 1 domínio

\- Nome DNS: subdomínio de domínio registrado: corp.lebios.com.br

&#x20; NetBIOS: EMPRESA. Não usar .local

\- Sistema: Windows Server 2025 (Standard), DCs em Server Core quando possível

\- Nível funcional: definir após conferir a doc (⚠️ ver seção de níveis)



\## 7. Restrições do laboratório

\- Host: RAM 4 GB, CPU 2 núcleos, disco 80 GB livre

* Licença: ISO de avaliação (prazo de ⚠️ 180 dias)

\- VirtualBox versão 7.2.20 ; VMs em modo EFI



\## 8. Inventário planejado de VMs (versão enxuta)

| VM | Função | vCPU | RAM | Disco | Site |

|----|--------|------|-----|-------|------|

| DC01 | DC + DNS (matriz) | 2 | 4 GB | 60 GB | Matriz |

| DC02 | DC + DNS (matriz) | 2 | 4 GB | 60 GB | Matriz |

| DC03 | DC ou RODC (filial) | 2 | 2–4 GB | 60 GB | Filial1 |

| SRV01 | DHCP/arquivos/membro | 2 | 4 GB | 60 GB | Matriz |

| CLI01 | Windows 11 cliente | 2 | 4 GB | 60 GB | Matriz |



\## 9. Plano de endereçamento (rascunho)

ID do site	Finalidade	CIDR	Gateway	Observações

MTR	Estações	10.10.0.0/23	10.10.0.1	358 estações + folga

MTR	Serdores	10.10.2.0/24	10.10.2.1	DCs com IP estático

FIL-A	Estações + Servidores	10.10.10.0/24	10.10.10.1	

FIL-B	Estações + Servidores	10.10.20.0/24	10.10.20.1	

FIL-C	Estações + Servidores	10.10.30.0/24	10.10.30.1	


\## 10. Convenção de nomes

Servidores SRV-FUNC-NN, estações PC-SETOR-NNN, contas de usuário nome.sobrenome,

contas administrativas adm.nome, grupos G-/DL- por função.



\## 11. Riscos e mitigações

(ex.: RAM do host insuficiente → usar Server Core e snapshots por fase)



\## 12. Critérios de aceite

(lista verificável: replicação sem erros, dcdiag limpo, join de cliente, GPO aplicada...)

