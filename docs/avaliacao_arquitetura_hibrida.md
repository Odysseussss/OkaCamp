# 🏗️ Avaliação: Arquitetura Híbrida (Supabase + Fabric SQL)

A arquitetura descrita em `Integração_md` é uma abordagem de **Segregação de Responsabilidades (CQRS-like)**, onde a escrita das regras de campanha ocorre em um banco de transação pequeno (Supabase) e a leitura dos metadados organizacionais acontece diretamente no Data Warehouse corporativo (Fabric).

## Concordo que é a melhor solução?

👍 **Sim, concordo plenamente. É a abordagem mais pragmática e inteligente para este cenário.**

1. **Evita Sincronização Desnecessária (Zero-ETL para Metadados):** Fazer cópias diárias de tabelas de Vendas, Produtos e Clientes do ERP/Fabric para o Supabase seria um pesadelo de engenharia (jobs lentos, falhas de sync, dados defasados). Ler direto da "Fonte da Verdade" garante precisão.
2. **Economiza Capacidade (F2):** Como você mencionou, gravar no Fabric pode causar *throttling* se a volumetria de requisições web for alta. Usar o Supabase (Postgres) como master de CRUD de campanhas protege a infraestrutura de dados.
3. **Padrão de Integração Analítica Perfeito:** Ao garantir que o Supabase grave as chaves *exatamente* como estão no Lakehouse (`codigoproduto`, `modelocampanha`), o PowerBI (usando o Semantic Model) fará o JOIN nativo das regras (Supabase Connector) com as Vendas (Direct Lake) de forma transparente.

---

## 💥 Resumo do Impacto no Código Node.js / React

Esta decisão altera significativamente o roadmap técnico. O Node.js não será apenas um front-end servindo arquivos React, ele precisará atuar como um **BFF (Backend for Frontend)** completo conectado a dois bancos de dados diferentes simultaneamente.

### 1. Novo Backend NodeJS (BFF)
O portal precisará de uma camada de API (ex: Express ou rotas de API do próprio Vite/Next.js) para coordenar essas duas fontes de dados. É inseguro e imprático expor a connection string do Fabric SQL diretamente no navegador.

### 2. Gerenciamento de Duplo Driver (Prisma + MSSQL)
Como sugerido na especificação:
- **Supabase (Escrita):** Usaremos o **Prisma ORM** (`prisma/client`) configurado com a URL do PostgreSQL. Será reponsável pelos endpoints: `POST /api/campaigns`, `PUT /api/campaigns`, `DELETE /api/campaigns`.
- **Fabric (Leitura Rápida):** Instalação do pacote **`mssql`** (ou `tedious`). Será responsável pelos endpoints de preenchimento de dropdowns do front-end: `GET /api/rcas`, `GET /api/produtos`.

### 3. Impacto na Interface Frontend (UI do `App.tsx`)
1. **O Fim do Upload de Excel:** Os botões de upload de Produtos e Vendas somem completamente. A aplicação passa a ser 100% online.
2. **Dropdowns Assíncronos:** No cadastro de campanhas, a seleção de Categoria/RCAs e validação de Códigos de Produto farão chamadas aos novos endpoints `GET /api/rcas` e `GET /api/produtos` (que batem no Fabric) e terão que lidar com estados de `loading`.
3. **Validação Cross-Source:** Quando o usuário digitar o `codigoproduto` da campanha, a interface deverá consultar o Fabric para validar se o código existe antes de permitir "Salvar" no Supabase.

### 4. Modelo de Dados no Prisma (Supabase)
Precisaremos mapear as seguintes tabelas no Prisma Schema:
```prisma
model campanha_header {
  id              Int       @id @default(autoincrement())
  nome            String
  periodo_inicio  DateTime
  periodo_fim     DateTime
  periodicidade   String
  // relacoes
}
// Outros models: campanha_modelo_rca, campanha_regra_condicao, campanha_filtro_escopo
```
*Atenção aos tipos para bater com o PowerBI:* Onde o PowerBI espera `Int64.Type` (ex: `codigoproduto`), o banco PostgreSQL deve usar `BigInt`.

---

## 🎯 Próximo Passo Sugerido no Código
Para colocar isso de pé agora no codebase atual:
1. Devemos converter o projeto de um simples "Vite SPA React" para um projeto **Fullstack**, adicionando um backend estruturado (como rotas API se passarmos para um framework como Remix/Next, ou um servidor Express/Fastify acoplado).
2. Criar a estrutura do `Prisma` para construir as tabelas do Supabase.
3. Criar os conectores de API.
