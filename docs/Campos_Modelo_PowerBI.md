# Campos do Modelo PowerBI

## Tabela: Clientes
        {"id", type text},                              // UUID
        {"codigocliente", Int64.Type},                  // Código Cliente (integer, unique)
        {"cliente", type text},                         // Cliente (text)
        {"descricaocliente", type text},                // Descrição Cliente (text)
        {"cpf_cnpj", type text},                        // CPF/CNPJ (varchar 20)
        {"tipocliente", type text},                     // Tipo Cliente (varchar 2)
        {"codigoibge", Int64.Type},                     // Código IBGE (integer)
        {"codigocidade", Int64.Type},                   // Código Cidade (integer)
        {"cidade", type text},                          // Cidade (varchar 100)
        {"uf", type text},                              // UF (varchar 2)
        {"bairro", type text},                          // Bairro (varchar 100)
        {"cep", type text},                             // CEP (varchar 10)
        {"endereco", type text},                        // Endereço (text)
        {"ddd", type text},                             // DDD (varchar 10)
        {"nomefantasia", type text},                    // Nome Fantasia (varchar 100)
        {"classe", type text},                          // Classe (varchar 10)
        {"classificacao", type text},                   // Classificação (varchar 10)
        {"emailcliente", type text},                    // Email Cliente (varchar 150)
        {"codigoramoatividade", Int64.Type},            // Código Ramo Atividade (integer)
        {"ramoatividade", type text},                   // Ramo Atividade (text)
        {"descricaorede", type text},                   // Descrição Rede (text)
        {"praca", type text},                           // Praça (text)
        {"codigorota", Int64.Type},                     // Código Rota (integer)
        {"rota", type text},                            // Rota (text)
        {"codigorede", Int64.Type},                     // Código Rede (integer)
        {"regiao", type text},                          // Região (text)
        {"ufregiao", type text},                        // UF Região (varchar 2)
        {"codigoclienteprincipal", Int64.Type},         // Código Cliente Principal (integer)
        {"nomefantasiaclienteprincipal", type text},    // Nome Fantasia Cliente Principal (text)
        {"clienteprincipal", type text},                // Cliente Principal (text)
        {"descricaoclienteprincipal", type text},       // Descrição Cliente Principal (text)
        {"codigoregiao", Int64.Type},                   // Código Região (integer)
        {"regiaopreco", type text},                     // Região Preço (text)
        {"codfilialnf", type text},                     // Código Filial NF (varchar 10)
        {"status", type text},                          // Status (varchar 20)
        {"diasinativos", type text},                    // Dias Inativos (varchar 30)
        {"ddd_canal", type text},                       // DDD Canal (varchar 20)
        {"ddd_canal2", type text},                       // DDD Canal2 (varchar 20)
        {"microrregiao", type text},                    // Microrregião (varchar 120)
        {"mesorregiao", type text},                     // Mesorregião (varchar 120)
        {"regiao_okj", type text},                      // Região OKJ (varchar 100)
        {"latitude", type text},                        // Latitude (varchar 20)
        {"longitude", type text}                        // Longitude (varchar 20)

## Produtos
        {"id", type text},                              // UUID
        {"codigoproduto", Int64.Type},                  // Código Produto
        {"codigocategoria", Int64.Type},                // Código Categoria
        {"codigosubcategoria", Int64.Type},             // Código Subcategoria
        {"codigosecao", Int64.Type},                    // Código Seção
        {"codigodepartamento", Int64.Type},             // Código Departamento
        {"codigofabrica", type text},                   // Código Fábrica
        {"codigofornecedor", Int64.Type},               // Código Fornecedor
        {"codigoprodutoprincipal", Int64.Type},         // Código Produto Principal
        {"descricaoproduto", type text},                // Descrição Produto
        {"descricaocategoria", type text},              // Descrição Categoria
        {"descricaodepartamento", type text},           // Descrição Departamento
        {"descricaosecao", type text},                  // Descrição Seção
        {"descricaosubcategoria", type text},           // Descrição Subcategoria
        {"descricaoprodutoprincipal", type text},       // Descrição Produto Principal
        {"produto", type text},                         // Produto
        {"categoria", type text},                       // Categoria
        {"tipomedicamento", type text},                 // Tipo Medicamento
        {"subcategoria", type text},                    // Subcategoria
        {"secao", type text},                           // Seção
        {"departamento", type text},                    // Departamento
        {"embalagem", type text},                       // Embalagem
        {"embalagemmaster", type text},                 // Embalagem Master
        {"classe", type text},                          // Classe
        {"classevenda", type text},                     // Classe Venda
        {"subclasse", type text},                       // Subclasse
        {"codmarca", Int64.Type},                       // Código Marca
        {"marca", type text},                           // Marca
        {"importado", type text},                       // Importado
        {"linhaproduto", type text},                    // Linha Produto
        {"codlinhaprazo", Int64.Type},                  // Código Linha Prazo
        {"ean13", type text},                           // EAN13
        {"foralinha", type text},                       // Fora Linha
        {"situacao", type text},                        // Situação
        {"codigodistribuicao", Int64.Type},             // Código Distribuição
        {"distribuicao", type text},                    // Distribuição
        {"categoriacampanha", type text},               // Categoria Campanha
        {"categoriaokajima", type text},                // Categoria Okajima
        {"produtoprincipal", type text}                 // Produto Principal

## RCA
        {"codigorca", type number},                     // Código RCA
        {"codigosupervisor", type number},              // Código Supervisor
        {"codigogerente", type number},                 // Código Gerente
        {"rca", type text},                             // RCA
        {"descricaorca", type text},                    // Descrição RCA
        {"emailrca", type text},                        // Email RCA
        {"telefonerca", type text},                     // Telefone RCA
        {"supervisor", type text},                      // Supervisor
        {"descricaosupervisor", type text},             // Descrição Supervisor
        {"emailsupervisor", type text},                 // Email Supervisor
        {"telefonesupervisor", type text},              // Telefone Supervisor
        {"gerente", type text},                         // Gerente
        {"descricaogerente", type text},                // Descrição Gerente
        {"emailgerente", type text},                    // Email Gerente
        {"situacao", type text},                        // Situação
        {"tipo", type text},                            // Tipo
        {"modelocampanha", type text},                  // Modelo Campanha
        {"hierarquia", type text},                      // Hierarquia
        {"roteirizacao", type text},                    // Roteirização
        {"cpf", type text},                             // CPF
        {"areaatuacao", type text},                     // Área Atuação
        {"codigocentrocusto", type text},               // Código Centro de Custo
        {"id", type text},                              // UUID
        {"ordem_status_supervisor", type number}        // Coluna de Ordenação

## Cabeçalho Pedido

        {"id", type text},                              // UUID (PostgreSQL: uuid)
        {"numeropedido", Int64.Type},                   // Código Pedido (PostgreSQL: bigint NOT NULL)
        {"numeropedidorca", Int64.Type},                // Número Pedido RCA (PostgreSQL: bigint NULL)
        {"numerocarregamento", Int64.Type},             // Número Carregamento (PostgreSQL: integer NULL)
        {"numeronotafiscal", Int64.Type},               // Número Nota Fiscal (PostgreSQL: integer NULL)
        {"numerotransacaovenda", Int64.Type},           // Número Transação Venda (PostgreSQL: integer NULL)
        {"codfilial", type text},                       // Código Filial (PostgreSQL: character varying(2) NULL)
        {"datavenda", type date},                        // Data Venda (PostgreSQL: date NULL)
        {"dataprevisaofaturamento", type date},         // Data Previsão Faturamento (PostgreSQL: date NULL)
        {"datafaturamento", type date},                 // Data Faturamento (PostgreSQL: date NULL)
        {"datacancelamento", type date},                // Data Cancelamento (PostgreSQL: date NULL)
        {"dataentrega", type date},                      // Data Entrega (PostgreSQL: date NULL)
        {"hora", Int64.Type},                           // Hora (PostgreSQL: integer NULL)
        {"minuto", Int64.Type},                         // Minuto (PostgreSQL: integer NULL)
        {"situacao", type text},                        // Situação (PostgreSQL: character varying(30) NULL)
        {"condicaovendadescricao", type text},         // Condição Venda Descrição (PostgreSQL: text NULL)
        {"cancelado", type text},                        // Cancelado (PostgreSQL: character varying(1) NULL)
        {"tipopedido", type text},                       // Tipo Pedido (PostgreSQL: character varying(40) NULL)
        {"codigotipopedido", Int64.Type},               // Código Tipo Pedido (PostgreSQL: integer NULL)
        {"condicaovenda", Int64.Type},                   // Condição Venda (PostgreSQL: integer NULL)
        {"observacaopedido", type text},                 // Observação Pedido (PostgreSQL: text NULL)
        {"tipovenda_nf", type text},                    // Tipo Venda NF (PostgreSQL: character varying(10) NULL)
        {"tipovenda_nf_descricao", type text},         // Tipo Venda NF Descrição (PostgreSQL: text NULL)
        {"especie_nf", type text},                      // Espécie NF (PostgreSQL: character varying(10) NULL)
        {"serie_nf", type text},                        // Série NF (PostgreSQL: character varying(10) NULL)
        {"origempedido", type text},                    // Origem Pedido (PostgreSQL: character varying(50) NULL)
        {"anomes_venda", type text}                     // Ano Mês Venda (PostgreSQL: character varying(6) NULL)

