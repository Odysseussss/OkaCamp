# 🚀 Migração para Backend Supabase (Fase de Protótipo)

A integração com o Supabase é um passo excelente que prepara o terreno para a arquitetura final (seja ela no Fabric ou não), pois força a aplicação a lidar com persistência assíncrona e um banco de dados real (PostgreSQL).

## 1. Impacto no Código Atual (`src/App.tsx`)

O impacto principal será na forma como os dados são salvos e carregados. A lógica de negócio (cálculos) e a UI mudam pouco nesta etapa, mas o gerenciamento de estado ganha complexidade assíncrona.

### O que deixará de existir:
- Operações de `localStorage.setItem` e `localStorage.getItem` para salvar campanhas e histórico.

### O que precisará ser criado/modificado:
1. **Novo Arquivo Cliente (`src/lib/supabase.ts`)**:
   - Criação da instância do cliente Supabase para ser usada em toda a aplicação.

2. **Refatoração de Inicialização de Estado (`useEffect`)**:
   - Atualmente, as campanhas e histórico são carregados sincronamente do `localStorage` na criação do estado (`useState`).
   - Com o Supabase, os estados iniciarão vazios (`[]`) e um `useEffect` fará o `fetch` assíncrono dos dados ao carregar a página. Necessidade de estados de "loading".

3. **Refatoração de Funções de Salvamento / Exclusão (CRUD)**:
   - Funções como `handleSaveCampaign` e `saveToHistory` passarão a ser `async` (assíncronas).
   - Ao invés de gravar no `localStorage`, farão requisições `insert()`, `update()` e `delete()` via API do Supabase.

4. **Instalação de Dependência**:
   - Será necessário executar `npm install @supabase/supabase-js`.

## 2. Tabelas Necessárias no Supabase

Para espelhar o estado atual, precisaremos criar as seguintes tabelas no banco de dados do seu projeto Supabase:

1. `campaigns` (Tabela principal de campanhas)
2. `campaign_categories` (Regras/categorias ligadas à campanha)
3. `apuracao_history` (Histórico de processamento de planilhas)

## 3. Chaves e Credenciais Necessárias

Para conectar o projeto Vite/React ao seu banco Supabase, você precisará me fornecer **duas** informações vitais que ficam no painel do Supabase (em Project Settings -> API).

Precisaremos criar um arquivo na raiz do projeto chamado `.env` (ou `.env.local`) com as seguintes variáveis:

```env
VITE_SUPABASE_URL=https://[ID-DO-SEU-PROJETO].supabase.co
VITE_SUPABASE_ANON_KEY=[SUA-CHAVE-PUBLICA-E-ANONIMA]
```

*Nota: Não se preocupe em me passar senhas do banco de dados (database password). Para o front-end interagindo com a API, precisamos apenas da URL e da chave `anon` (pública).*

---
**Próximos Passos recomendados caso queira iniciar:**
1. Me forneça a `URL` e a `ANON_KEY`.
2. Eu configuro o cliente e refatoro o monolito `App.tsx` isolando a lógica de banco de dados.
