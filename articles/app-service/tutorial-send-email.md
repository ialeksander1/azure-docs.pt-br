---
title: 'Tutorial: enviar emails com os Aplicativos Lógicos'
description: Saiba como invocar processos empresariais em seu aplicativo do Serviço de Aplicativo. Envie emails, tweets e postagens do Facebook, adicione a listas de endereçamento e muito mais.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892565"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Tutorial: enviar emails e invocar outros processos empresariais no Serviço de Aplicativo

Neste tutorial, você aprenderá a integrar seu aplicativo do Serviço de Aplicativo aos seus processos empresariais. Isso é comum em cenários de aplicativos Web, como:

- Enviar emails de confirmação de transação
- Adicionar usuários grupos do Facebook
- Conectar-se a sistemas de terceiros, como SAP, SalesForce etc.
- Trocar mensagens B2B padrão

Neste tutorial, você envia emails com o Gmail de seu aplicativo do Serviço de Aplicativo usando os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md). Há outras maneiras de enviar emails de um aplicativo Web, por exemplo, usando a configuração de SMTP fornecida pela estrutura da linguagem. No entanto, os Aplicativos Lógicos trazem muito mais recursos para seu aplicativo do Serviço de Aplicativo sem adicionar complexidade ao código. Os Aplicativos Lógicos fornecem uma interface de configuração simples para as integrações empresariais mais populares, as quais seu aplicativo pode chamar a qualquer momento com uma solicitação HTTP.

## <a name="prerequisite"></a>Pré-requisito

Implante um aplicativo com a estrutura de linguagem de sua escolha no Serviço de Aplicativo. Para seguir um tutorial para implantar um aplicativo de exemplo, confira abaixo:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Tutorial: criar um aplicativo ASP.NET no Azure com Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Tutorial: criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Tutorial: Criar um aplicativo Node.js e MongoDB no Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Tutorial: Criar um aplicativo PHP e MySQL no Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Tutorial: Executar um aplicativo Web Python (Django) com o PostgreSQL no Serviço de Aplicativo do Azure](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Criar um aplicativo Ruby e Postgres no Serviço de Aplicativo do Azure no Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Criar o aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico vazio seguindo as instruções em [Criar o aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Quando vir o **Designer de Aplicativos Lógicos**, volte para este tutorial.
1. Na página inicial do Designer de Aplicativos Lógicos, selecione **Quando uma solicitação HTTP é recebida** em **Inicie com um gatilho comum**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. Na caixa de diálogo **Quando uma solicitação HTTP é recebida**, selecione **Use o conteúdo de amostra para gerar o esquema**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Copie o JSON de exemplo a seguir na caixa de texto e selecione **Concluído**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Agora, o esquema é gerado para os dados de solicitação que você deseja. Na prática, você pode simplesmente capturar os de solicitação reais que o código do aplicativo gera e permitir que o Azure gere o esquema JSON para você. 
1. Na parte superior do Designer de Aplicativos Lógicos, selecione **Salvar**. 

    Agora, você pode ver a URL de seu gatilho de solicitação HTTP. Selecione o ícone de cópia para copiar a chave para uso posterior.

    ![](./media/tutorial-send-email/http-request-url.png)

    Essa definição de solicitação HTTP é um gatilho para qualquer coisa que você queira fazer no aplicativo lógico, seja ele o Gmail ou qualquer outro. Posteriormente, você invocará essa URL em seu aplicativo do Serviço de Aplicativo. Para saber mais sobre o gatilho de solicitação, confira a [Referência de solicitação/resposta HTTP](../connectors/connectors-native-reqres.md).

1. Na parte inferior do designer, clique em **Nova etapa**, digite **Gmail** na caixa de pesquisa de ações e localize e selecione **Enviar email (V2)** .
    
    > [!TIP]
    > Você pode pesquisar outros tipos de integrações, como SendGrid, MailChimp, Office 365 e SalesForce. Para saber mais , confira a [Documentação dos Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/).
1. Na caixa de diálogo **Gmail**, selecione **Entrar** e entre na conta do Gmail da qual deseja enviar o email.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Depois de conectado, clique na caixa de texto **Para** e a caixa de diálogo de conteúdo dinâmico será aberta automaticamente.

1. Ao lado da ação **Quando uma solicitação HTTP é recebida**, selecione **Ver mais**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Agora, você deve ver as três propriedades de seus dados JSON de exemplo usados anteriormente. Nesta etapa, você usa essas propriedades da solicitação HTTP para construir um email.
1. Como você está selecionando o valor do campo **Para**, escolha **email**. Se quiser, desative a caixa de diálogo de conteúdo dinâmico clicando em **Adicionar conteúdo dinâmico**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Na lista suspensa **Adicionar novo parâmetro**, selecione **Assunto** e **Corpo**.

1. Clique na caixa de texto **Assunto** e, da mesma forma, escolha **tarefa**. Com o cursor ainda na caixa **Assunto**, digite *criado*. 

1. Clique em **Corpo** e, da mesma forma, escolha **conclusão**. Mova o cursor para a esquerda de **conclusão** e digite *Este item de trabalho deve ser concluído em*.

    > [!TIP]
    > Se quiser editar o conteúdo HTML diretamente no corpo do email, selecione **Modo de exibição de código** na parte superior da janela do Designer de Aplicativos Lógicos. Lembre-se apenas de preservar o código de conteúdo dinâmico (por exemplo, `@{triggerBody()?['due']}`)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Em seguida, adicione uma resposta HTTP assíncrona ao gatilho HTTP. Entre o gatilho HTTP e a ação do Gmail, clique no sinal **+** e selecione **Adicionar uma ramificação paralela**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Na caixa de pesquisa, pesquise por **resposta** e, em seguida, selecione a ação **Resposta**.

    ![](./media/tutorial-send-email/choose-response-action.png)

    Por padrão, a ação de resposta envia um HTTP 200. Ele é bom o suficiente para este tutorial. Para saber mais , confira a [Referência de solicitação/resposta HTTP](../connectors/connectors-native-reqres.md).

1. Na parte superior do Designer de Aplicativos Lógicos, selecione **Salvar** novamente. 

## <a name="add-http-request-code-to-app"></a>Adicionar código de solicitação HTTP ao aplicativo

Verifique se você copiou a URL do gatilho de solicitação HTTP anterior. Como ela contém informações confidenciais, é uma melhor prática não a colocar diretamente no código. Com o Serviço de Aplicativo, você pode fazer referência a ela como uma variável de ambiente, usando as configurações do aplicativo. 

No [Cloud Shell](https://shell.azure.com), crie a configuração do aplicativo com o seguinte comando (substitua *\<app-name>* , *\<resource-group-name>* e *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Em seu código, faça um HTTP post padrão para a URL usando qualquer linguagem de cliente HTTP disponível para sua estrutura de linguagem, com a seguinte configuração:

- O corpo da solicitação contém o mesmo formato JSON que você forneceu ao aplicativo lógico:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- A solicitação contém o título `Content-Type: application/json`. 
- Para otimizar o desempenho, envie a solicitação de maneira assíncrona, se possível.

Clique na guia da linguagem/estrutura preferencial abaixo para ver um exemplo.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

No ASP.NET, você pode enviar o HTTP post com a classe [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient). Por exemplo:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Se estiver testando esse código no aplicativo de exemplo do [Tutorial: Criar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md), você poderá usá-lo para enviar um email de confirmação em [Criar ação](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), depois que o item `Todo` for adicionado. Para usar o código assíncrono acima, converta Criar ação em assíncrono.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

No ASP.NET Core, você pode enviar o HTTP post com a classe [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient). Por exemplo:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Esse código é escrito para simplificar a demonstração. Na prática, não crie uma instância de um objeto `HttpClient` para cada solicitação. Siga as orientações em [Usar IHttpClientFactory para implementar solicitações HTTP resilientes] (https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient -http-requests).

Se estiver testando esse código no aplicativo de exemplo do [Tutorial: Criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](app-service-web-tutorial-dotnetcore-sqldb.md), você poderá usá-lo para enviar um email de confirmação em [Criar ação](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65), depois que o item `Todo` for adicionado.

# <a name="nodejs"></a>[Node.js](#tab/node)

No Node.js, você pode enviar o HTTP post facilmente com um pacote npm como o [axios](https://www.npmjs.com/package/axios). Por exemplo:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Se estiver testando esse código no aplicativo de exemplo do [Tutorial: Criar um aplicativo Node.js e MongoDB no Azure](app-service-web-tutorial-nodejs-mongodb-app.md), você poderá usá-lo para enviar um email de confirmação em [criar função](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27), pós [o artigo ser salvo com êxito](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

No PHP, você pode enviar o HTTP post facilmente com o [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Por exemplo:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Se estiver testando esse código no aplicativo de exemplo do [Tutorial: Criar um aplicativo Web PHP e MySQL no Azure](app-service-web-tutorial-php-mysql.md), você poderá usá-lo para enviar um email de confirmação na [função Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), logo antes da instrução return.

# <a name="python"></a>[Python](#tab/python)

No Python, você pode enviar o HTTP post facilmente com [solicitações](https://pypi.org/project/requests/). Por exemplo:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Se estiver testando esse código no aplicativo de exemplo do [Tutorial: Executar um aplicativo Web Python (Django) com PostgreSQL no Serviço de Aplicativo do Azure](containers/tutorial-python-postgresql-app.md), você poderá usá-lo para enviar um email de confirmação na [função Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), logo antes da instrução return.

# <a name="ruby"></a>[Ruby](#tab/ruby)

No Ruby, você pode enviar o HTTP post facilmente com [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Por exemplo:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Se estiver testando esse código no aplicativo de exemplo de [Criar um aplicativo Ruby e Postgres no Serviço de Aplicativo do Azure no Linux](containers/tutorial-ruby-postgres-app.md), você poderá usá-lo para enviar um email de confirmação na ação [criar](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38), [quando @task.save for bem-sucedido](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Mais recursos

[Tutorial: Hospedar uma API RESTful com CORS no Serviço de Aplicativo do Azure](app-service-web-tutorial-rest-api.md)  
[Referência de solicitação/resposta HTTP para os Aplicativos Lógicos](../connectors/connectors-native-reqres.md)  
[Início Rápido: Criar seu primeiro fluxo de trabalho usando os Aplicativos Lógicos do Azure – portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)