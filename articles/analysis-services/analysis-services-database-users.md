---
title: Gerenciar usuários e funções de banco de dados no Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar usuários e funções de banco de dados em um servidor do Analysis Services no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454196"
---
# <a name="manage-database-roles-and-users"></a>Gerenciar usuários e funções de banco de dados

No nível do modelo de banco de dados, todos os usuários devem pertencer a uma função. As funções definem os usuários com permissões específicas para o modelo de banco de dados. Qualquer usuário ou grupo de segurança adicionado a uma função deve ter uma conta em um locatário do Azure AD na mesma assinatura que o servidor. 

Como você define as funções é diferente dependendo da ferramenta usada, mas o efeito é o mesmo.

As permissões de função incluem:
*  **Administrador** – os usuários têm permissões completas para o banco de dados. Funções de banco de dados com permissões de administrador são diferentes dos administradores do servidor.
*  **Processar** – os usuários podem se conectar e executar operações de processo no banco de dados e analisar os dados do modelo de banco de dados.
*  **Leitura** – os usuários podem usar um aplicativo cliente para se conectar e analisar os dados do modelo de banco de dados.

Ao criar um projeto de modelo tabular, você cria funções e adiciona usuários ou grupos a essas funções usando o Role Manager no Visual Studio com projetos de Serviços de Análise. Quando implantado em um servidor, use SQL Server Management Studio (SSMS), [Serviços de Análise CmShell cmdlets](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)ou [Tabular Model Scripting Language](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) para adicionar ou remover funções e membros do usuário.

Ao adicionar um grupo `obj:groupid@tenantid`de **segurança,** use .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Para adicionar ou gerenciar funções e usuários no Visual Studio  
  
1.  No **Tabular Model Explorer**, clique com o botão direito do mouse **Funções**.  
  
2.  Em **Gerenciador de papéis,** clique em **Novo**.  
  
3.  Digite um nome para a função.  
  
     Por padrão, o nome da função padrão será numerado incrementalmente para cada nova função. É recomendável que você digite um nome que claramente Identifique o tipo de membro, por exemplo, Gerentes Financeiros ou Especialistas de Recursos Humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhum**|Os membros não podem ler ou modificar o esquema do modelo e não podem consultar dados.|  
    |**Ler**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema de modelo.|  
    |**Leitura e processo**|Os membros podem consultar dados (com base em filtros de nível de linha) e executar as operações Processar e Processar Tudo, mas não podem modificar o esquema de modelo.|  
    |**Processo**|Membros podem executar operações de Processar e Processar Tudo. Não é possível ler ou modificar o esquema do modelo e não pode consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema de modelo e consultar todos os dados.|   
  
5.  Se a função que você está criando tem a permissão Ler ou Ler e Processar, é possível adicionar filtros de linha usando uma fórmula DAX. Clique na guia **Filtros de Linha**, selecione uma tabela, clique no campo **Filtro DAX** e digite uma fórmula DAX.
  
6.  Clique **em Membros** > **Adicionar externos**.  
  
8.  Em **Adicionar Membro Externo**, insira usuários ou grupos no seu locatário do Azure AD pelo endereço de email. Depois que você clicar em OK e fechar o Gerenciador de Funções, as funções e membros da função aparecem no Gerenciador de Modelos Tabular. 
 
     ![Funções e usuários no Gerenciador de Modelos Tabular](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implante em seu servidor do Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerenciar funções e usuários no SSMS

Para adicionar funções e usuários a um modelo de banco de dados implantado, você deve estar conectado ao servidor como Administrador do servidor ou já estar em uma função de banco de dados com permissões de administrador.

1. Na Exporer de objetos, clique com o botão direito **do** > mouse**Em Novas Funções**.

2. Em **Criar Função**, insira um nome de função e uma descrição.

3. Selecione uma permissão.

   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controle total (Administrador)**|Os membros podem modificar o esquema de modelo, processar e consultar todos os dados.| 
   |**Processar Banco de Dados**|Membros podem executar operações de Processar e Processar Tudo. Não é possível modificar o esquema modelo e não é possível consultar dados.|  
   |**Ler**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema de modelo.|  
  
4. Clique em **Associação**, em seguida, insira um usuário ou grupo no seu locatário do Azure AD pelo endereço de email.

     ![Adicionar usuário](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se a função que você está criando tem a permissão Ler, é possível adicionar filtros de linha usando uma fórmula DAX. Clique em **Filtros de Linha**, selecione uma tabela e, em seguida, digite uma fórmula DAX no campo **Filtro DAX**. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e usuários usando um script TMSL

Você pode executar um script TMSL na janela XMLA no SSMS ou usando o PowerShell. Use o comando [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) e o objeto [Roles](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl).

**Exemplo de script TMSL**

Neste exemplo, um usuário externo B2B e um grupo são adicionados à função Analista com permissões de leitura para o banco de dados SalesBI. O usuário externo e o grupo devem estar no mesmo locatário do Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e usuários usando o PowerShell

O módulo [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral, que aceita um script ou consulta de TMSL (Linguagem de Script de Modelo Tabular). Os cmdlets a seguir são usados para gerenciar usuários e funções de banco de dados.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro de complemento](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicionar um membro a uma função de banco de dados.| 
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Executar um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Os filtros de linha definem quais linhas em uma tabela podem ser consultadas por membros de uma função específica. Os filtros de linha são definidos para cada tabela em um modelo usando fórmulas DAX.  
  
Os filtros de linha podem ser definidos somente para funções com as permissões Ler e Ler e Processar. Por padrão, se um filtro de linha não está definido para uma tabela específica, os membros podem consultar todas as linhas na tabela, a menos que a filtragem cruzada se aplique de outra tabela.
  
 Os filtros de linha exigem uma fórmula DAX, que deve ser avaliada como um valor TRUE/FALSE, para definir as linhas que podem ser consultadas por membros daquela função específica. Não é possível consultar linhas não incluídas na fórmula DAX. Por exemplo, a tabela Clientes com a seguinte expressão de filtros de linha, *=Clientes [País] = "EUA"*, os membros da função Vendas só podem ver clientes nos EUA.  
  
Os filtros de linha são aplicados às linhas especificadas e às linhas relacionadas. Quando uma tabela tiver várias relações, os filtros aplicam segurança para a relação que está ativa. Os filtros de linha são interseccionados com outros filtros de linha definidos para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|= Região [País] = "USA"|  
|ProductCategory|= ProductCategory [Name] = "Bicicletas"|  
|Transações|=Transactions[Year]=2016|  
  
 O efeito líquido é que os membros podem consultar as linhas de dados em que o cliente está nos EUA, a categoria de produto é bicicletas e o ano é 2016. Os usuários não podem consultar transações fora dos EUA, transações que não são bicicletas ou transações que não são em 2016 amenos que sejam um membro de outra função que concede essas permissões.
  
 Você pode usar o filtro, *=FALSE()*, para negar o acesso a todas as linhas de uma tabela inteira.

## <a name="next-steps"></a>Próximas etapas

  [Gerenciar administradores de servidores](analysis-services-server-admins.md)   
  [Gerenciar o Azure Analysis Services com PowerShell](analysis-services-powershell.md)  
  [Referência de TMSL (Linguagem de Scripts do Modelo Tabular)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

