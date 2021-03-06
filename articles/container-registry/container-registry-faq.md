---
title: Perguntas frequentes
description: Respostas para perguntas frequentes relacionadas ao serviço de Registro de Contêineres do Azure
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 7452b5dd3c952a13a28566914d2fe513689d4751
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618804"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas frequentes sobre o Registro de Contêineres do Azure

Este artigo aborda perguntas frequentes e questões conhecidas sobre o Registro de Contêineres do Azure.

## <a name="resource-management"></a>Gerenciamento de recursos

- [Posso criar um registro de contêiner do Azure usando um modelo de Gerenciador de recursos?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe a vulnerabilidade de segurança que busca imagens no ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como configurar o Kubernetes com o Registro de Contêineres Do Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como faço para obter credenciais de admin para um registro de contêineres?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como obter credenciais de admin em um modelo de Gerenciador de recursos?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A exclusão da replicação falha com o status proibido, embora a replicação seja excluída usando o Azure CLI ou o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [As regras do firewall são atualizadas com sucesso, mas não fazem efeito](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Posso criar um Registro de Contêiner do Azure usando um modelo de Gerenciador de recursos?

Sim. Aqui está [um modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) que você pode usar para criar um registro.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe a vulnerabilidade de segurança que busca imagens no ACR?

Sim. Consulte a documentação do [Azure Security Center,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como configurar o Kubernetes com o Registro de Contêineres Do Azure?

Consulte a documentação de [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e etapas para [Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como faço para obter credenciais de admin para um registro de contêineres?

> [!IMPORTANT]
> A conta de usuário do administração foi projetada para que um único usuário acesse o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Ver [visão geral de autenticação](container-registry-authentication.md).

Antes de obter credenciais de admin, certifique-se de que o usuário de admin do registro esteja habilitado.

Para obter credenciais usando o Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Usando o Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como obter credenciais de admin em um modelo de Gerenciador de recursos?

> [!IMPORTANT]
> A conta de usuário do administração foi projetada para que um único usuário acesse o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Ver [visão geral de autenticação](container-registry-authentication.md).

Antes de obter credenciais de admin, certifique-se de que o usuário de admin do registro esteja habilitado.

Para obter a primeira senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obter a segunda senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A exclusão da replicação falha com o status proibido, embora a replicação seja excluída usando o Azure CLI ou o Azure PowerShell

O erro é visto quando o usuário tem permissões em um registro, mas não tem permissões de nível reader na assinatura. Para resolver esse problema, atribua permissões do Leitor na assinatura ao usuário:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>As regras do firewall são atualizadas com sucesso, mas não fazem efeito

Leva algum tempo para propagar mudanças nas regras do firewall. Depois de alterar as configurações do firewall, aguarde alguns minutos antes de verificar essa alteração.


## <a name="registry-operations"></a>Operações de registro

- [Como acessar o Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Como excluir todos os manifestos que não são referenciados por qualquer tag em um repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Por que o uso da cota de registro não reduz após a exclusão de imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como validar alterações nas cotas de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como faço para autenticar com meu registro ao executar a CLI em um contêiner?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Como ativar o TLS 1.2?](#how-to-enable-tls-12)
- [O Registro de Contêineres do Azure suporta o Content Trust?](#does-azure-container-registry-support-content-trust)
- [Como concedo acesso para puxar ou empurrar imagens sem permissão para gerenciar o recurso de registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como habilitar quarentena automática de imagem para um registro?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Como faço para habilitar acesso de tração anônimo?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Como acessar o Docker Registry HTTP API V2?

O ACR suporta o Registro Docker HTTP API V2. As APIs podem ser `https://<your registry login server>/v2/`acessadas em . Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como excluir todos os manifestos que não são referenciados por qualquer tag em um repositório?

Se você está em bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: Você `-y` pode adicionar no comando excluir para pular a confirmação.

Para obter mais informações, consulte [Excluir imagens de contêiner no Registro de Contêineres do Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Por que o uso da cota de registro não reduz após a exclusão de imagens?

Essa situação pode acontecer se as camadas subjacentes ainda estiverem sendo referenciadas por outras imagens de contêiner. Se você excluir uma imagem sem referências, o uso do registro será atualizado em poucos minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como validar alterações nas cotas de armazenamento?

Crie uma imagem com uma camada de 1GB usando o seguinte arquivo docker. Isso garante que a imagem tenha uma camada que não seja compartilhada por nenhuma outra imagem no registro.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Construa e empurre a imagem para o seu registro usando o CLI docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Você deve ser capaz de ver que o uso de armazenamento aumentou no portal Azure, ou você pode consultar o uso usando o CLI.

```azurecli
az acr show-usage -n myregistry
```

Exclua a imagem usando o Azure CLI ou portal e verifique o uso atualizado em poucos minutos.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como faço para autenticar com meu registro ao executar a CLI em um contêiner?

Você precisa executar o contêiner Azure CLI montando o soquete Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

No recipiente, `docker`instale:

```bash
apk --update add docker
```

Em seguida, autentique com seu registro:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Como ativar o TLS 1.2?

Habilite o TLS 1.2 usando qualquer cliente docker recente (versão 18.03.0 ou superior). 

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, o Registro de Contêiner do Azure exigirá que todas as conexões seguras de servidores e aplicativos usem o TLS 1.2. O suporte para TLS 1.0 e 1.1 será desativado.

### <a name="does-azure-container-registry-support-content-trust"></a>O Registro de Contêineres do Azure suporta o Content Trust?

Sim, você pode usar imagens confiáveis no Registro de Contêineres do Azure, uma vez que o [Notário Docker](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser habilitado. Para obter detalhes, consulte [Content Trust no Registro de Contêineres do Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde está o arquivo da impressão digital localizado?

Em: `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`

* As chaves públicas e os certificados de todas `root.json`as funções (exceto funções de delegação) estão armazenados no .
* As chaves públicas e os certificados da função delegação são armazenados `targets.json` no `targets/releases` arquivo JSON de sua função principal (por exemplo, para a função).

Sugere-se verificar essas chaves públicas e certificados após a verificação geral do TUF feita pelo cliente Docker e Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como concedo acesso para puxar ou empurrar imagens sem permissão para gerenciar o recurso de registro?

O ACR suporta [funções personalizadas](container-registry-roles.md) que fornecem diferentes níveis de permissões. Especificamente, `AcrPull` `AcrPush` e as funções permitem que os usuários puxem e/ou empurrem imagens sem a permissão para gerenciar o recurso de registro no Azure.

* Portal Azure: Seu registro -> Access Control (IAM) -> Add (Selecionar `AcrPull` ou `AcrPush` para a função).
* Azure CLI: Encontre o ID de recurso do registro executando o seguinte comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Em seguida, você `AcrPull` `AcrPush` pode atribuir a ou função `AcrPull`a um usuário (o exemplo a seguir usa):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Ou, atribuir a função a um princípio de serviço identificado pelo seu ID de aplicação:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O cessionário é então capaz de autenticar e acessar imagens no registro.

* Para autenticar em um registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para listar repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Para puxar uma imagem:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Com o uso `AcrPull` apenas `AcrPush` da função ou função, o cessionário não tem permissão para gerenciar o recurso de registro no Azure. Por `az acr list` exemplo, `az acr show -n myRegistry` ou não mostrará o registro.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como habilitar quarentena automática de imagem para um registro?

A quarentena de imagens é atualmente um recurso de visualização do ACR. Você pode habilitar o modo de quarentena de um registro para que apenas as imagens que passaram com sucesso na varredura de segurança sejam visíveis para usuários normais. Para obter detalhes, consulte o [repo Do GitHub do ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Como faço para habilitar acesso de tração anônimo?

A configuração de um registro de contêiner do Azure para acesso de tração anônimo (público) é atualmente um recurso de visualização. Para habilitar o acesso público, https://aka.ms/acr/support/create-ticketabra um bilhete de suporte em . Para obter detalhes, consulte o Fórum de Comentários do [Azure](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries).


## <a name="diagnostics-and-health-checks"></a>Diagnósticos e exames de saúde

- [Verifique a saúde com`az acr check-health`](#check-health-with-az-acr-check-health)
- [o docker pull falha com erro: net/http: solicitação cancelada enquanto aguarda conexão (Client.Timeout excedido enquanto aguarda cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [o empurrão docker é bem sucedido, mas a atração de docker falha com erro: não autorizada: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`sucesso, mas comandos docker falham com erro: não autorizado: autenticação necessária](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Habilite e obtenha os logs de depuração do daemon docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Novas permissões de usuário podem não ser eficazes imediatamente após a atualização](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [As informações de autenticação não são fornecidas no formato correto em chamadas diretas da API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Por que o portal Azure não lista todos os meus repositórios ou tags?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Por que o portal Azure não consegue buscar repositórios ou tags?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Por que meu pedido de puxão ou empurrão falha com a operação proibida?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Como faço para coletar traços http no Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Verifique a saúde com`az acr check-health`

Para solucionar problemas de ambiente e registro comuns, consulte [Verificar a saúde de um registro de contêiner do Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>o docker pull falha com erro: net/http: solicitação cancelada enquanto aguarda conexão (Client.Timeout excedido enquanto aguarda cabeçalhos)

 - Se este erro for um problema transitório, então a tentativa será bem sucedida.
 - Se `docker pull` falhar continuamente, então pode haver um problema com o daemon Docker. O problema geralmente pode ser mitigado reiniciando o daemon Docker. 
 - Se você continuar a ver esse problema depois de reiniciar o daemon do Docker, então o problema pode ser alguns problemas de conectividade de rede com a máquina. Para verificar se a rede geral da máquina é saudável, execute o seguinte comando para testar a conectividade do ponto final. A `az acr` versão mínima que contém este comando de verificação de conectividade é 2.2.9. Atualize seu Cli do Azure se estiver usando uma versão mais antiga.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Você deve sempre ter um mecanismo de repetição em todas as operações de clientes Docker.

### <a name="docker-pull-is-slow"></a>Docker puxar é lento
Use [esta](http://www.azurespeed.com/Azure/Download) ferramenta para testar a velocidade de download da rede da sua máquina. Se a rede da máquina for lenta, considere usar o Azure VM na mesma região que o seu registro. Isso geralmente lhe dá velocidade de rede mais rápida.

### <a name="docker-push-is-slow"></a>O empurrão do Docker é lento
Use [esta](http://www.azurespeed.com/Azure/Upload) ferramenta para testar a velocidade de upload da rede da sua máquina. Se a rede da máquina for lenta, considere usar o Azure VM na mesma região que o seu registro. Isso geralmente lhe dá velocidade de rede mais rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>O empurrão do Docker é bem sucedido, mas o docker pull falha com o erro: não autorizado: autenticação necessária

Esse erro pode acontecer com a versão Red Hat `--signature-verification` do daemon Docker, onde é habilitado por padrão. Você pode verificar as opções do Daemon do Docker para Red Hat Enterprise Linux (RHEL) ou Fedora executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o Fedora 28 Server tem as seguintes opções de daemon de docker:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Com `--signature-verification=false` falta, `docker pull` falha com um erro semelhante a:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a `--signature-verification=false` opção ao arquivo de `/etc/sysconfig/docker`configuração do daemon do Docker . Por exemplo:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Reinicie o serviço de daemon do Docker executando o seguinte comando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Detalhes `--signature-verification` podem ser `man dockerd`encontrados executando .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>o login az acr é bem sucedido, mas o docker falha com erro: não autorizado: autenticação necessária

Certifique-se de usar uma URL de `docker push myregistry.azurecr.io/myimage:latest`servidor em minúsculas, por exemplo, mesmo que `myRegistry`o nome do recurso do registro seja maiúsculo ou caso misto, como .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Habilite e obtenha os registros de depuração do daemon Docker    

Comece `dockerd` com `debug` a opção. Primeiro, crie o arquivo de`/etc/docker/daemon.json`configuração do daemon do `debug` Docker ( ) se ele não existir e adicione a opção:

```json
{    
    "debug": true    
}
```

Então, reinicie o daemon. Por exemplo, com o Ubuntu 14.04:

```bash
sudo service docker restart
```

Os detalhes podem ser encontrados na documentação do [Docker.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * Os registros podem ser gerados em diferentes locais, dependendo do seu sistema. Por exemplo, para o Ubuntu 14.04, é `/var/log/upstart/docker.log`.    
Consulte [a documentação do Docker](https://docs.docker.com/engine/admin/#read-the-logs) para obter detalhes.    

 * Para Docker for Windows, os logs são gerados em %LOCALAPPDATA%/docker/. No entanto, pode não conter todas as informações de depuração ainda.    

   Para acessar o registro completo do daemon, você pode precisar de algumas etapas extras:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora você tem acesso a todos os `dockerd`arquivos da VM em execução . O registro `/var/log/docker.log`está em .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Novas permissões de usuário podem não ser eficazes imediatamente após a atualização

Quando você concede novas permissões (novas funções) a um diretor de serviço, a alteração pode não ter efeito imediato. Há dois motivos possíveis:

* Atraso na atribuição de atribuição do Diretório Ativo do Azure. Normalmente é rápido, mas pode levar minutos devido ao atraso na propagação.
* Atraso de permissão no servidor de token ACR. Isso pode levar até 10 minutos. Para atenuar, `docker logout` você pode e, em seguida, autenticar novamente com o mesmo usuário após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente, o ACR não suporta a exclusão de replicação doméstica pelos usuários. A solução alternativa é incluir a criação de replicação doméstica `"condition": false` no modelo, mas pular sua criação adicionando como mostrado abaixo:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>As informações de autenticação não são fornecidas no formato correto em chamadas diretas da API REST

Você pode `InvalidAuthenticationInfo` encontrar um erro, `curl` especialmente usando `-L` `--location` a ferramenta com a opção , (para seguir redirecionamentos).
Por exemplo, buscar a `curl` bolha `-L` usando com opção e autenticação básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

pode resultar na seguinte resposta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A causa principal `curl` é que algumas implementações seguem redirecionamentos com cabeçalhos da solicitação original.

Para resolver o problema, você precisa seguir redirecionamentos manualmente sem os cabeçalhos. Imprima os cabeçalhos de resposta com a `-D -` opção de `curl` e depois extraia: o `Location` cabeçalho:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Por que o portal Azure não lista todos os meus repositórios ou tags? 

Se você estiver usando o navegador Microsoft Edge/IE, você pode ver no máximo 100 repositórios ou tags. Se o seu registro tiver mais de 100 repositórios ou tags, recomendamos que você use o navegador Firefox ou Chrome para listá-los todos.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Por que o portal Azure não consegue buscar repositórios ou tags?

O navegador pode não ser capaz de enviar a solicitação de busca de repositórios ou tags para o servidor. Pode haver várias razões como:

* Falta de conectividade de rede
* Firewall
* Bloqueadores de anúncios
* Erros de DNS

Entre em contato com o administrador da rede ou verifique a configuração e a conectividade da rede. Tente `az acr check-health -n yourRegistry` executar usando seu Azure CLI para verificar se seu ambiente é capaz de se conectar ao Registro de Contêineres. Além disso, você também pode tentar uma sessão anônima ou privada no seu navegador para evitar qualquer cache ou cookies de navegador obsoletos.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Por que meu pedido de puxão ou empurrão falha com a operação proibida?

Aqui estão alguns cenários onde as operações talvez não permitidas:
* Os registros clássicos não são mais suportados. Atualize-se para um [SKUs](https://aka.ms/acr/skus) suportado usando [a atualização az acr](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) ou o portal Azure.
* A imagem ou repositório talvez bloqueado para que não possa ser excluído ou atualizado. Você pode usar o comando [az acr show repositório](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) para visualizar atributos atuais.
* Algumas operações são proibidas se a imagem estiver em quarentena. Saiba mais sobre [quarentena](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Como faço para coletar traços http no Windows?

#### <a name="prerequisites"></a>Pré-requisitos

- Habilite a descriptogração https no fiddler:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Habilite o Docker para usar um proxy através da ui Docker:<https://docs.docker.com/docker-for-windows/#proxies>
- Certifique-se de reverter quando estiver completo.  Docker não vai trabalhar com isso ativado e violinista não está funcionando.

#### <a name="windows-containers"></a>Contêineres do Windows

Configurar proxy Docker para 127.0.0.1:8888

#### <a name="linux-containers"></a>Contêineres do Linux

Encontre o ip do switch virtual do Docker vm:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure o proxy Docker para a saída do comando anterior e a porta 8888 (por exemplo, 10.0.75.1:8888)

## <a name="tasks"></a>Tarefas

- [Como faço para cancelar corridas em lote?](#how-do-i-batch-cancel-runs)
- [Como incluo a pasta .git no comando az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [As tarefas suportam o GitLab para gatilhos de origem?](#does-tasks-support-gitlab-for-source-triggers)
- [Que serviço de gerenciamento de repositório git suporta tarefas?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Como faço para cancelar corridas em lote?

Os comandos a seguir cancelam todas as tarefas em execução no registro especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como incluo a pasta .git no comando az acr build?

Se você passar uma pasta `az acr build` de `.git` origem local para o comando, a pasta será excluída do pacote carregado por padrão. Você pode `.dockerignore` criar um arquivo com a seguinte configuração. Ele diz ao comando para `.git` restaurar todos os arquivos no pacote carregado. 

`!.git/**`

Esta configuração também `az acr run` se aplica ao comando.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>As tarefas suportam o GitLab para gatilhos de origem?

No momento, não suportamos o GitLab para gatilhos de origem.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Que serviço de gerenciamento de repositório git suporta tarefas?

| Serviço Git | Contexto de origem | Construção manual | Construção automática através do gatilho de confirmação |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Sim | Sim |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Sim | Sim |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Sim | Não |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Sim | Não |

## <a name="run-error-message-troubleshooting"></a>Executar solução de problemas da mensagem de erro

| Mensagem de erro | Guia de Solução de Problemas |
|---|---|
|Nenhum acesso foi configurado para a VM, portanto, não foram encontradas assinaturas|Isso pode acontecer se `az login --identity` você estiver usando em sua Tarefa ACR. Este é um erro transitório e ocorre quando a atribuição de função de sua Identidade Gerenciada não se propagou. Esperando alguns segundos antes de tentar novamente funciona.|

## <a name="cicd-integration"></a>Integração CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Ações do GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-registry-intro.md) sobre o Registro de Contêineres do Azure.
