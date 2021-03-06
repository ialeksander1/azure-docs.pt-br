---
title: ACOS no azure Cosmos DB linguagem de consulta
description: Saiba como o sistema SQL ACOS (arccosice) funciona no Azure Cosmos DB retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300954"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno).  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `ACOS` seguir retorna o de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
