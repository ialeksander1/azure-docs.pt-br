---
title: Usando identidade para criar chaves de substituto
description: Recomendações e exemplos para usar a propriedade IDENTITY para criar chaves de substituto em tabelas no pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e681e8ad655c31d5078b56b8f1a49cfd7c664533
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742644"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Usando identidade para criar chaves de substituto no pool Synapse SQL

Recomendações e exemplos para usar a propriedade IDENTITY para criar chaves de substituto em tabelas no pool Synapse SQL.

## <a name="what-is-a-surrogate-key"></a>O que é uma chave de substituto

Uma chave substituta em uma tabela é uma coluna com um identificador exclusivo para cada linha. A chave não é gerada de dados da tabela. Os modeladores de dados gostam de criar chaves substitutas em suas tabelas quando criam modelos de data warehouse. Você pode usar a propriedade IDENTITY para atingir esse objetivo de forma simples e eficiente, sem afetar o desempenho de carga.  

## <a name="creating-a-table-with-an-identity-column"></a>Criando uma tabela com uma coluna IDENTITY

A propriedade IDENTITY foi projetada para dimensionar todas as distribuições no pool Synapse SQL sem afetar o desempenho da carga. Portanto, a implementação de IDENTITY é orientada para atingir esses objetivos.

Você pode definir uma tabela como tendo a propriedade IDENTITY quando você cria a tabela pela primeira vez usando uma sintaxe semelhante à instrução a seguir:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Você pode usar `INSERT..SELECT` para popular a tabela.

Este restante desta seção destaca as nuances da implementação para ajudá-lo a entendê-los mais detalhadamente.  

### <a name="allocation-of-values"></a>Alocação de valores

A propriedade IDENTITY não garante a ordem na qual os valores substitutos são alocados, o que reflete o comportamento do SQL Server e do Banco de Dados SQL do Azure. No entanto, na piscina Synapse SQL, a ausência de uma garantia é mais acentuada.

O exemplo a seguir é uma ilustração:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

No exemplo anterior, duas linhas foram descarregadas na distribuição 1. A primeira linha tem o valor substituto de 1 na coluna `C1`, e a segunda linha tem o valor substituto 61. Ambos os valores foram gerados pela propriedade IDENTITY. No entanto, a alocação dos valores não é contígua. Este comportamento ocorre por design.

### <a name="skewed-data"></a>Dados distorcidos

Os intervalos de valores para o tipo de dados são distribuídos igualmente pelas distribuições. Se uma tabela distribuída tiver dados distorcidos, então o intervalo de valores disponível para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados resultam em uma única distribuição, então efetivamente a tabela tem acesso a apenas um sexagésimo dos valores do tipo de dados. Por esse motivo, a propriedade de identidade é limitada somente aos tipos de dados `INT` e `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO

Quando uma coluna de IDENTITY existente é selecionada em uma nova tabela, a nova coluna herda a propriedade IDENTITY, a menos que uma das seguintes condições seja verdadeira:

- A instrução SELECT contém uma junção.
- Várias instruções SELECT são unidas usando UNION.
- A coluna IDENTITY é listada mais de uma vez na lista SELECT.
- A coluna IDENTITY é parte de uma expressão.

Se alguma dessas condições for verdadeira, a coluna será criada como NOT NULL em vez de herdar a propriedade IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) segue o mesmo comportamento do SQL Server que está documentado para SELECT..INTO. No entanto, você não pode especificar uma propriedade IDENTITY na definição de coluna da parte `CREATE TABLE` da instrução. Você também não pode usar a função IDENTITY na parte `SELECT` do CTAS. Para popular uma tabela, você precisa usar `CREATE TABLE` para definir a tabela, seguido de `INSERT..SELECT` para preenchê-la.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserir explicitamente os valores em uma coluna IDENTITY

O pool Synapse SQL suporta `SET IDENTITY_INSERT <your table> ON|OFF` sintaxe. Você pode usar essa sintaxe para inserir explicitamente os valores na coluna IDENTITY.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para determinadas linhas em suas dimensões. Um exemplo é de -1 ou a linha "membro desconhecido".

O próximo script mostra como adicionar essa linha explicitamente usando SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Carregando dados

A presença da propriedade IDENTITY tem algumas implicações no seu código de carregamento de dados. Esta seção destaca alguns padrões básicos para carregar dados em tabelas usando IDENTITY.

Para carregar dados em uma tabela e gerar uma chave substituta, usando IDENTITY, crie a tabela e, em seguida, use INSERT..SELECT ou INSERT..VALUES para executar o carregamento.

O exemplo a seguir destaca o padrão básico:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Não é possível usar `CREATE TABLE AS SELECT` atualmente ao carregar dados em uma tabela com uma coluna IDENTITY.
>

Para obter mais informações sobre o carregamento de dados, consulte [Projetando Extrato, Carga e Transformação (ELT) para pool Synapse SQL](design-elt-data-loading.md) e [Práticas recomendadas de carregamento](guidance-for-loading-data.md).

## <a name="system-views"></a>Exibições do sistema

Você pode usar a exibição de catálogo [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para identificar uma coluna que tem a propriedade IDENTITY.

Para ajudá-lo a entender melhor o esquema de banco de dados, este exemplo mostra como integrar sys.identity_column` com outras exibições de catálogo do sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitações

A propriedade IDENTITY não pode ser usada:

- Quando o tipo de dados da coluna não é INT ou BIGINT
- Quando a coluna é também a chave de distribuição
- Quando a tabela é uma tabela externa

As seguintes funções relacionadas não são suportadas no pool Synapse SQL:

- [IDENTIDADE()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Tarefas comuns

Esta seção fornece um código de exemplo que você pode usar para executar tarefas comuns ao trabalhar com colunas IDENTITY.

A coluna C1 é a IDENTITY em todas as tarefas a seguir.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Localizar o maior valor alocado para uma tabela

Use a função `MAX()` para determinar o valor mais alto alocado para uma tabela distribuída:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Localizar a semente e o incremento para a propriedade IDENTITY

Você pode usar as exibições de catálogo para descobrir os valores de configuração da semente e do incremento da identidade para uma tabela usando a consulta a seguir:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da tabela](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) IDENTITY (Propriedade)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
