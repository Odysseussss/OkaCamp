# Visão Geral do Projeto OkaCamp

## Propósito do Projeto
O **OkaCamp** tem o objetivo de automatizar o ciclo de vida de campanhas comerciais, permitindo o cadastro autônomo pelo time de vendas e apuração automatizada via Microsoft Fabric.

## Visão Geral da Solução
O OkaCamp é um portal web desenvolvido em Node.js que serve como interface operacional. Ele resolve o gargalo de criação manual de campanhas, permitindo que os dados de "input" (regras da campanha) sejam integrados diretamente ao "output" (análise de performance no Power BI) sem intervenção manual da equipe de dados.

## Público-Alvo
- Analistas de Vendas.
- Trade Marketing.
- Controladoria
## Benefícios
- **Agilidade**: Visualização rapida de campanhas logo após serem criadas.
- **Precisão**: Como as regras são definidas pelo trade, a precisão é garantida.
- **Transparência**: Visualização clara de quem atingiu as metas e o valor da premiação devida.
- **Autonomia**: O time de Trade pode criar e gerenciar campanhas sem depender da equipe de TI.
- **Otimização do tempo produtivo**: O time de dados não precisa mais criar campanhas manualmente, podendo focar em análises mais estratégicas.

## Arquitetura de Dados (Data Stack)
A solução utiliza uma abordagem híbrida dentro do OneLake para otimizar custo e performance:
Front-end/Back-end: Node.js (Portal Operacional).
Camada de Escrita (Operational Store): Fabric Warehouse (SKU F2).
Utilizado para armazenar tabelas de Cadastros, Metas e Configurações de Campanha via T-SQL.
Camada de Leitura (Analytical Store): Fabric Lakehouse.
Uso de Shortcuts (Atalhos) para espelhar tabelas de Vendas, Clientes e Produtos vindas do Azure Data Lake Store (ADLS).
Camada de Visualização: Power BI (Licença PPU).
Conexão via Direct Lake para garantir que, assim que uma campanha for cadastrada no portal, ela já esteja disponível para análise.

## Estrutura do Projeto: Etapas de Implementação 🛠️
Fase 1: Fundação no Fabric (Warehouse)
Criação do Warehouse dedicado na capacidade F2.
Modelagem das tabelas de transação (ex: tbl_campanha_header, tbl_campanha_metas).
Configuração de Service Principal no Azure para que o Node.js acesse o Warehouse com segurança.

Fase 2: Integração e Shortcuts
Criação de atalhos no Lakehouse para as tabelas mestras (Vendas/Clientes) já existentes.
Criação de Views SQL no Fabric para consolidar a lógica de negócio (ex: gatilhos de 90% de meta, apuração de positivação).
Nota: Manter a lógica em Views garante um back-end Node.js mais "leve" e centraliza as regras de negócio no banco.

Fase 3: Desenvolvimento do OkaCamp (Node.js)
Desenvolvimento das telas de formulário para o time comercial.
Implementação da conexão via driver TDS (SQL Server) para realizar o INSERT/UPDATE no Warehouse.
Consumo das Views de apuração para exibição de um "Dashboard Rápido" dentro do próprio portal.

Fase 4: Entrega no Power BI
Conexão do Modelo Semântico ao Lakehouse.
Desenvolvimento do relatório oficial de performance das campanhas usando o motor Vertipaq do Power BI.

## Gestão de Custos e Performance 💰
Isolamento de Carga: O portal OkaCamp consome apenas a SKU F2, garantindo que operações de escrita não concorram com os recursos da PPU utilizados pelos diretores e gestores no Power BI.
Suavização (Smoothing): As escritas no Warehouse são operações leves (baixo volume de linhas), o que mantém a utilização de CU (Capacity Units) dentro dos limites da F2 sem gerar throttling.






