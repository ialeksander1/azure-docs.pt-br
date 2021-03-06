---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SuccessFactors | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292972"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SuccessFactors

Neste tutorial, você aprenderá a integrar o SuccessFactors ao Azure AD (Azure Active Directory). Quando integrar o SuccessFactors ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SuccessFactors.
* Permitir que os usuários sejam conectados automaticamente ao SuccessFactors com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do SuccessFactors habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SuccessFactors é compatível com o SSO iniciado por **SP**.
* Após configurar o SuccessFactors, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Adição do SuccessFactors da galeria

Para configurar a integração do SuccessFactors ao Azure AD, você precisará adicionar o SuccessFactors da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SuccessFactors** na caixa de pesquisa.
1. Selecione **SuccessFactors** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Configurar e testar o SSO do Azure AD para SuccessFactors

Configure e teste o SSO do Azure AD com o SuccessFactors usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SuccessFactors.

Para configurar e testar o SSO do Azure AD com o SuccessFactors, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do SuccessFactors](#configure-successfactors-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do SuccessFactors](#create-successfactors-test-user)** – para ter um equivalente de B. Fernandes no SuccessFactors vinculado à representação dela no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SuccessFactors**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do cliente do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) para obter esses valores.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SuccessFactors**, copie as URLs apropriadas com base em suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
    1. No campo **Nome**, insira `B.Simon`.  
    1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao SuccessFactors.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SuccessFactors**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-successfactors-sso"></a>Configurar o SSO do SuccessFactors

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **portal de administração do SuccessFactors** como administrador.

2. Visite **Segurança de Aplicativo** e nativo do **Recurso Logon Único**.

3. Coloque qualquer valor em **Redefinir Token** e clique em **Salvar Token** para habilitar SSO do SAML.

    ![Configurar o logon único no lado do aplicativo][11]

    > [!NOTE]
    > Esse valor é usado como chave liga/desliga. Se nenhum valor for salvo, o SSO do SAML será ATIVADO. Se um valor em branco for salvo, o SSO do SAML será DESATIVADO.

4. Nativo da captura de tela abaixo e execute as ações a seguir:

    ![Configurar o logon único no lado do aplicativo][12]
  
    a. Selecione o botão de opção **SSO do SAML v2**
  
    b. Defina o **Nome da Parte de Declaração SAML** (por exemplo, emissor SAML + nome da empresa).

    c. Na caixa de texto **Issuer URL**, cole o valor **Identificador do Microsoft Azure Active Directory** que você copiou do portal do Azure.

    d. Selecione **Asserção** como **Exigir Assinatura Obrigatória**.

    e. Selecione **Habilitado** como **Habilitar Sinalizador SAML**.

    f. Selecione **Não** como **Assinatura da Solicitação de Logon (Gerado por SF/SP/RP)** .

    g. Selecione **Perfil de Navegador/Postagem** como **Perfil SAML**.

    h. Selecione **Não** como **Impor Período de Certificado Válido**.

    i. Copie o conteúdo do arquivo do certificado baixado do portal do Azure e, então, cole-o na caixa de texto **Certificado de Verificação SAML**.

    > [!NOTE] 
    > O conteúdo do certificado deve ter começar marcas de certificado do certificado e de fim.

5. Navegue até SAML V2 e então execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo][13]

    a. Selecione **Sim** como **Logoff Global iniciado por SP de suporte**.

    b. Na caixa de texto **URL do Global Logout Service (destino LogoutRequest)** , cole o valor **URL de logout** que você copiou do portal do Azure.

    c. Selecione **Não** como **Exigir que sp criptografe todos os elementos NameID**.

    d. Selecione **não especificado** como **Formato de NameID**.

    e. Selecione **Sim** como **Habilitar logon iniciado por sp (AuthnRequest)** .

    f. Na caixa de texto **Enviar solicitação como emissor de toda a empresa**, cole o valor da **URL de Logon** copiada do portal do Azure.

6. Execute estas etapas se quiser fazer com os nomes de usuário de logon diferenciem maiúsculas de minúsculas.

    ![Configurar o logon único][29]

    a. Visite **Configurações da Empresa**(próximo à parte inferior).

    b. Marque a caixa de seleção ao lado de **Habilitar Nome de Usuário que Não Diferencia Maiúsculas de Minúsculas**.

    c. Clique em **Save** (Salvar).

    > [!NOTE]
    > Se você tentar habilitar essa opção, o sistema verifica se ele cria um nome de logon SAML duplicado. Por exemplo, se o cliente tiver os nomes de usuário Usuário1 e usuário1. Parar de diferenciar maiúsculas e minúsculas cria essas duplicatas. O sistema fornece a você uma mensagem de erro e não habilita o recurso. O cliente precisa alterar um dos nomes de usuário para que ele seja digitado diferente.

### <a name="create-successfactors-test-user"></a>Crie um usuário de teste do SuccessFactors

Para permitir que os usuários do Azure AD entrem no SuccessFactors, eles deverão ser provisionados no SuccessFactors. No caso do SuccessFactors, o provisionamento será uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SuccessFactors no Painel de Acesso, você deverá ser conectado automaticamente ao SuccessFactors, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o SuccessFactors com o Azure AD](https://aad.portal.azure.com)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o SuccessFactors com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
