---
title: Gerenciar versões - LUIS
titleSuffix: Azure Cognitive Services
description: As versões permitem que você crie e publique modelos diferentes. É uma boa prática clonar o modelo ativo atual para uma versão diferente do aplicativo antes de fazer alterações no modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220892"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Usar versões para editar e testar sem afetar aplicativos de preparo ou produção

As versões permitem que você crie e publique modelos diferentes. É uma boa prática clonar o modelo ativo atual para uma [versão](luis-concept-version.md) diferente do aplicativo antes de fazer alterações no modelo. 

Para trabalhar com versões, abra o aplicativo selecionando o nome dele na página **Meus Aplicativos** e, em seguida, selecione **Gerenciar** na barra superior e selecione **Versões**. 

A lista de versões mostra quais versões são publicadas, onde são publicadas e qual versão está ativa no momento. 

> [!div class="mx-imgBorder"]
> [![Gerenciar a seção, página de versões](./media/luis-how-to-manage-versions/versions-import.png "Gerenciar a seção, página de versões")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar uma versão

1. Selecione a versão que você deseja clonar e, em seguida, selecione **Clonar** na barra de ferramentas. 

2. Na caixa de diálogo **Clonar versão**, digite um nome para a nova versão, como "0.2".

   ![Caixa de diálogo Clonar Versão](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > A ID da versão pode consistir apenas em caracteres, dígitos ou '.' e não pode ter mais de 10 caracteres.
 
   Uma nova versão com o nome especificado é criada e definida como a versão ativa.

## <a name="set-active-version"></a>Definir versão ativa

Selecione uma versão da lista e selecione **Ativar** na barra de ferramentas. 

> [!div class="mx-imgBorder"]
> [![Gerenciar seção, página de versões, fazer uma ação de versão](./media/luis-how-to-manage-versions/versions-other.png "Gerenciar seção, página de versões, fazer uma ação de versão")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importa versão

Você pode `.json` importar `.lu` uma ou uma versão do seu aplicativo.

1. Selecione **Importar** na barra de ferramentas e selecione o formato. 

2. Na janela pop-up **Importar nova versão**, insira o nome da nova versão com dez caracteres. Você só precisa definir um ID de versão se a versão no arquivo já existir no aplicativo.

    ![Gerenciar seção, página de versões, importar nova versão](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Após importar uma versão, a nova versão se tornará a versão ativa.

### <a name="import-errors"></a>Erros de importação

* Erros do tokenizador: Se você tiver um **erro de tokenizador** ao importar, você está tentando importar uma versão que usa um [tokenizer](luis-language-support.md#custom-tokenizer-versions) diferente do que o aplicativo usa atualmente. Para corrigir isso, consulte [Migrando entre versões de tokenizer](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Outras ações

* Para **excluir** uma versão, selecione-a na lista e selecione **Excluir** na barra de ferramentas. Selecione **Ok**. 
* Para **renomear** uma versão, selecione-a na lista e selecione **Renomear** na barra de ferramentas. Insira o novo nome e selecione **Concluído**. 
* Para **exporta** uma versão, selecione-a na lista e selecione **Exportar aplicativo** na barra de ferramentas. Escolha JSON para exportar para backup, escolha **Exportar para contêiner** para usar este aplicativo em um contêiner [LUIS](luis-container-howto.md).  

