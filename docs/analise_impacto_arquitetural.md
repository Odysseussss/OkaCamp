# 📊 Análise de Impacto Arquitetural — OkaCamp

**Data:** 12/03/2026 | **Projeto:** OkaCamp v0.4 → Nova Visão (Overview.md)

---

## 1. Estado Atual do Codebase

### Diagnóstico Rápido

| Métrica | Situação Atual |
|---|---|
| Arquivo principal | [src/App.tsx](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx) — **1.330 linhas em um único arquivo** |
| Framework | React 19 + Vite + Tailwind CSS v4 + TypeScript |
| Banco de dados | `localStorage` (browser storage local, sem backend) |
| Entrada de dados | Upload manual de [.xlsx](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/data.xlsx) via SheetJS (CDN) |
| Lógica de negócio | Inline no componente React (dentro de `useMemo`) |
| Autenticação | ❌ Inexistente |
| API externa | ❌ Inexistente |
| Testes | ❌ Nenhum |

**Problemas técnicos identificados no código atual:**
- God Component Anti-Pattern: toda a aplicação vive em um único [App.tsx](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx). Estado, UI, lógica de cálculo e persistência estão misturados.
- Tipagem fraca: uso de `as any` em múltiplos pontos do motor de cálculo (ex: `_historyBefore`, `_vendasDepois` como campos fantasmas).
- Persistência frágil: `localStorage` é volátil, não compartilhável entre usuários e limitado a ~5MB — inviável como banco de dados para um sistema multi-usuário de distribuição.
- Sem integração real: as "campanhas cadastradas" ficam apenas no browser local. Nenhum outro sistema lê esses dados.

---

## 2. O Delta Arquitetural (O que muda com o Overview.md)

O documento de visão propõe uma transformação de **ferramenta offline de análise** para uma **plataforma integrada de Data Operations**. A magnitude do delta é enorme.

### Mapa de Delta por Camada

```
ANTES (atual)                          DEPOIS (novo Overview)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[ Browser ]                            [ Browser / Node.js Portal ]
    ↕ xlsx manual                           ↕ API REST / SDK
[ localStorage ]                       [ Fabric Warehouse F2 (SQL Server) ]
                                            ↕ OneLake Shortcuts
                                       [ ADLS Gen2 (Parquet) ]
                                            ↕ Direct Lake
                                       [ Power BI (PPU) ]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 3. Impacto Detalhado por Componente de Código

### 3.1 Camada de Interface (Node.js / React)

| Componente Atual | Impacto | Descrição |
|---|---|---|
| [App.tsx](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx) (monolito) | 🔴 **Refatoração total** | Precisa ser quebrado em módulos: `CampaignForm`, `CampaignList`, `ApuracaoDashboard`, `HistoryView`, etc. |
| [handleFileUpload()](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx#183-209) | 🔴 **Removido** | A entrada de dados deixa de ser upload de Excel. Os dados virão do Fabric Warehouse via API. |
| Motor de cálculo (`useMemo results`) | 🟡 **Migrado** | A lógica de Escalonada, Combo e Metas Simples migra para **SQL Views no Fabric** (T-SQL). O front-end apenas exibe resultados. |
| `localStorage` (campanhas e histórico) | 🔴 **Removido** | Substituído por escrita no Fabric Warehouse via API segura (Service Principal). |
| Interface de [CampaignCategory](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx#99-108) | 🟡 **Expandida** | A estrutura atual já captura `provider`, `saleType`, `quantity`, etc. O schema precisará ser alinhado com o DDL do Warehouse. |
| Autenticação | 🔴 **Criado do zero** | Será necessário autenticar usuários para controlar quem escreve no Warehouse (Service Principal como proxy). |

### 3.2 Camada de Dados

| Item | Impacto | Descrição |
|---|---|---|
| `localStorage` → Fabric Warehouse | 🔴 **Substituição completa** | Requer setup do Warehouse na SKU F2, criação das tabelas DDL (Campanhas, Categorias, Regras), e configuração de Service Principal com permissões de escrita. |
| Leitura de Vendas (Excel upload) → OneLake Shortcuts | 🔴 **Arquitetura nova** | Shortcuts apontando para as tabelas `.parquet` no ADLS Gen2. Não há código frontend para isso — é configuração de infraestrutura. |
| Supabase (fase de protótipo) | 🟢 **Adicionado temporariamente** | O Overview prevê uma fase intermediária com Supabase (Postgres) antes de migrar para Fabric. Isso significa adicionar um cliente Supabase JS ao projeto. |

### 3.3 Camada de Lógica de Negócio

| Item | Impacto | Descrição |
|---|---|---|
| Motor de cálculo (JS/`useMemo`) | 🟡 **Reescrito em T-SQL** | As mecânicas Escalonada, Combo e Bateu-Levou passam a ser **SQL Views e Stored Procedures no Fabric**. O Node.js fica "light" — apenas faz CRUD e apresenta snapshots. |
| Filtro "Não Positivados" | 🟡 **Migrado para SQL** | A lógica de `_historyBefore`/`targetDate` que hoje é implementada no `useMemo` será uma cláusula `WHERE` na View de apuração. |
| Lógica de Caixas e métricas decimais | 🔴 **Novo** | O Overview cita "métricas decimais (Caixas)" processadas via Spark. Isso requer um Notebook Spark no Fabric — não existe hoje. |

---

## 4. Veredicto: Continuar o Projeto ou Criar um Novo?

### 🎯 Recomendação: Migração Progressiva (não reescrever do zero)

#### Argumentos para MIGRAR (não reescrever):
- ✅ A stack (React + Vite + TypeScript) é compatível com a nova visão — Node.js como portal é exatamente o que já está sendo feito.
- ✅ As interfaces [Campaign](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx#109-116), [CampaignCategory](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx#99-108) e o fluxo de cadastro já estão implementados — são o núcleo do novo sistema.
- ✅ A UI e o design system (Tailwind, componentes) podem ser reaproveitados e expandidos.
- ✅ As mecânicas de cálculo (Escalonada, Combo) estão modeladas e testadas manualmente — servem como especificação para as SQL Views.

#### Argumentos contra REESCREVER do zero:
- ❌ Perda de ~300h de desenvolvimento de UI e lógica de mecânicas.
- ❌ O risco de introduzir novos bugs nas mecânicas de cálculo é alto em uma reescrita total.
- ❌ A visão do Overview é **incremental** (Fase 1 → Fase 4), não uma substituição abrupta. O código atual suporta as Fases 1 e 2.

#### O que NÃO tem como reaproveitar (e que precisa ser construído do zero):
- 🔴 Camada de API (comunicação com Fabric Warehouse / Supabase)
- 🔴 Autenticação / Autorização
- 🔴 SQL Views e DDL do Warehouse
- 🔴 Shortcuts e configuração do OneLake
- 🔴 Notebooks Spark para métricas de Caixas

---

## 5. Roadmap Técnico Recomendado

Alinhado com as Fases do Overview, mas com ações de código concretas:

### Fase 0 (Imediata) — Refatoração do Monolito
> **Condição necessária para qualquer integração.**

- [ ] Quebrar [App.tsx](file:///c:/odyspaste/Ambiente%20de%20Exposi%C3%A7%C3%A3o/OkaCamp/src/App.tsx) (1330 linhas) em componentes isolados:
  - `src/components/CampaignForm.tsx`
  - `src/components/ApuracaoEngine.tsx` (temporário, até migrar para SQL)
  - `src/components/HistoryView.tsx`
  - `src/layouts/Sidebar.tsx`
- [ ] Extrair lógica de negócio para `src/lib/calculations.ts`
- [ ] Criar `src/types/index.ts` com todas as interfaces centralizadas
- [ ] Corrigir os `as any` no motor de cálculo com tipagem explícita

**Esforço estimado:** 2–3 dias de desenvolvimento.

### Fase 1 (Protótipo com Supabase)
> Substitui o `localStorage` por um banco real antes de conectar ao Fabric.

- [ ] Instalar `@supabase/supabase-js`
- [ ] Criar serviço `src/lib/db.ts` para abstrair operações de CRUD
- [ ] Criar tabelas no Supabase: `campaigns`, `campaign_categories`, `apuracao_history`
- [ ] Migrar todas as chamadas a `localStorage.setItem/getItem` para o serviço `db.ts`

**Esforço estimado:** 3–5 dias de desenvolvimento.

### Fase 2 (Motor SQL no Fabric)
> As mecânicas saem do JavaScript e entram no T-SQL.

- [ ] Criar DDL do Warehouse: tabelas `Campanha`, `CampanhaCategoria`, `RegraEscalonada`, `RegraCombo`
- [ ] Criar SQL Views de apuração por mecânica
- [ ] Criar endpoint Node.js (ou Edge Function Supabase) para proxy de escrita no Fabric via Service Principal
- [ ] Substituir o `useMemo results` por uma chamada à API que retorna o snapshot calculado

**Esforço estimado:** 1–2 semanas (mais infra que código).

### Fase 3 (Portal Completo)
> Integração final e entrega analítica.

- [ ] Autenticação (Auth Supabase ou Microsoft Entra ID + MSAL)
- [ ] Dashboard de acompanhamento em tempo real (Snapshot de apuração)
- [ ] Publicação no Power BI via Direct Lake

---

## 6. Riscos Técnicos Identificados

| Risco | Severidade | Mitigação |
|---|---|---|
| Throttling na SKU F2 com escritas frequentes | 🟡 Médio | Projetar operações como batch e transações curtas (já previsto no Overview) |
| Latência do Fabric Warehouse em queries de apuração | 🟡 Médio | Usar Direct Lake apenas para BI; portal faz queries no Warehouse com cache de snapshot |
| Service Principal com excesso de permissões | 🔴 Alto | Princípio do menor privilégio: SP com permissão apenas de escrita nas tabelas de campanhas |
| Migração de dados do `localStorage` para banco real | 🟢 Baixo | Volume pequeno; pode ser feito com script de importação pontual |
| Complexidade do Spark para métricas de Caixas | 🟡 Médio | Priorizar T-SQL antes; Spark apenas se conversão de unidades for crítica |
