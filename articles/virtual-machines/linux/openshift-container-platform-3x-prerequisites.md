---
title: Plataforma de contêineres OpenShift 3.11 nos pré-requisitos do Azure
description: Pré-requisitos para implantar a Plataforma de Contêineres OpenShift 3.11 no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 26b190515819378309c2b0705efdbc349ecccbe2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759514"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Pré-requisitos comuns para implantação da Plataforma de Contêineres OpenShift 3.11 no Azure

Este artigo descreve pré-requisitos comuns para implantar o OpenShift Container Platform ou OKD no Azure.

A instalação do OpenShift é feita por meio de guias estratégicos do Ansible. Ansible usa Secure Shell (SSH) para se conectar a todos os hosts de cluster para concluir as etapas de instalação.

Quando ansible faz a conexão SSH com os hosts remotos, ele não pode digitar uma senha. Por esse motivo, a chave privada não pode ter uma senha (senha) associada a ela ou a implantação falhará.

Como todas as máquinas virtuais (VMs) são implantadas por meio de modelos do Azure Resource Manager, a mesma Chave pública é usada para acessar todas as VMs. A chave privada correspondente deve estar na VM que executa todos os livros de jogo também. Para executar essa ação com segurança, um cofre de chaves Do Zure é usado para passar a chave privada para a VM.

Se houver uma necessidade de armazenamento persistente para contêineres, então Volumes Persistentes serão necessários. O OpenShift suporta discos rígidos virtuais (VHDs) do Azure para volumes persistentes, mas o Azure deve primeiro ser configurado como provedor de nuvem.

Nesse modelo, o OpenShift:

- Cria um objeto VHD em uma conta de armazenamento Azure ou em um disco gerenciado.
- Monta o VHD em uma VM e formata o volume.
- Montará o volume no Pod.

Para que isso funcione, o OpenShift precisa de permissões para executar as tarefas no Azure. Um diretor de serviço é usado para este fim. A Entidade de serviço é uma conta de segurança no Azure Active Directory que tem as permissões para recursos.

A Entidade de serviço deve ter acesso a Contas de armazenamento e máquinas virtuais que compõem o cluster. Se todos os recursos de cluster do OpenShift forem implantados em um único Grupo de recursos, a SP pode receber permissões para esse Grupo de recursos.

Este guia descreve como criar os artefatos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de nuvem do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 
Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/reference-index) e siga as instruções na tela ou clique em **Experimentar** para usar o Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve usar um grupo de recursos dedicado para hospedar o cofre-chave. Esse grupo é separado do grupo de recursos no qual os recursos de cluster OpenShift implantam.

O exemplo abaixo cria um grupo de recursos denominado *keyvaultrg* no local *eastus*:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave
Crie um KeyVault para armazenar as chaves de SSH para o cluster com o comando [az keyvault create](/cli/azure/keyvault). O nome do cofre chave deve ser globalmente único e deve ser habilitado para a implantação do modelo ou a implantação falhará com o erro "KeyVaultParameterReferenceSecretRetrieveFailed".

O exemplo abaixo cria um keyvault denominado *keyvault* no grupo de recursos *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH é necessária para proteger o acesso ao cluster de OpenShift. Crie um par de chaves SSH usando o comando `ssh-keygen` (no Linux ou Mac):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH não pode ter uma senha / frase secreta.

Para obter mais informações sobre as chaves SSH no Windows, confira [Como criar chaves SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows). Certifique-se de exportar a chave privada no formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Armazenar chave privada SSH no Azure Key Vault
A implantação de OpenShift usa a chave SSH criada para proteger o acesso ao mestre OpenShift. Para habilitar a implantação para recuperar a chave SSH com segurança, armazene a chave no Key Vault usando o comando a seguir:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 
O OpenShift se comunica com o Azure usando um nome de usuário e a senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. É melhor escopo das permissões do diretor do serviço para grupos de recursos específicos em vez de toda a assinatura.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp) e gere as credenciais necessárias para o OpenShift.

O exemplo a seguir cria um principal de serviço e atribui permissões de contribuinte a um grupo de recursos chamado *openshiftrg*.

Primeiro, crie o grupo de recursos chamado *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Criar uma entidade de serviço:

```azurecli
az group show --name openshiftrg --query id
```

Salvar a saída do comando e usar no lugar de $scope no próximo comando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Anote a propriedade appId e a senha retornada do comando:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Certifique-se de anotar a senha segura, pois não será possível recuperar essa senha novamente.

Para obter mais informações sobre entidades de serviço, confira [Criar entidade de serviço do Azure com a CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis apenas ao modelo de Gerenciador de Recursos

Os segredos precisarão ser criados para a chave privada SSH **(sshPrivateKey),** o segredo do cliente Azure AD **(aadClientSecret),** a senha de administrador do OpenShift **(openshiftPassword)** e a senha ou chave de ativação do Red Hat Subscription Manager **(rhsmPasswordOrActivationKey).**  Além disso, se forem usados certificados TLS/SSL personalizados, seis segredos adicionais precisarão ser criados - **roteamento,** **roteamento,** **arquivo de roteamento, arquivo de teclas de roteamento,** **mastercafile,** **mastercertfile**e **masterkeyfile**.  Esses parâmetros serão explicados com mais detalhes.

O modelo faz referência a nomes secretos específicos, então você **deve** usar os nomes em negrito listados acima (sensível ao caso).

### <a name="custom-certificates"></a>Certificados personalizados

Por padrão, o modelo implantará um cluster OpenShift usando certificados auto-assinados para o console web OpenShift e o domínio de roteamento. Se você quiser usar certificados TLS/SSL personalizados, defina 'roteamentoCertType' como 'personalizado' e 'masterCertType' como 'personalizado'.  Você precisará dos arquivos CA, Cert e Key no formato .pem para os certificados.  É possível usar certificados personalizados para um, mas não para o outro.

Você precisará armazenar esses arquivos em segredos do Key Vault.  Use o mesmo Cofre de Chaves que o usado para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes secretos, o modelo é codificado para usar nomes secretos específicos para cada um dos arquivos de certificado TLS/SSL.  Armazene os dados do certificado usando as informações da tabela a seguir.

| Nome Secreto      | Arquivo de certificado   |
|------------------|--------------------|
| mastercafile     | arquivo ca mestre     |
| mastercertfile   | arquivo cert mestre   |
| masterkeyfile    | arquivo de chave mestre    |
| roteamentoarquivo    | arquivo CA de roteamento    |
| routingcertfile  | roteamento de arquivo CERT  |
| routingkeyfile   | arquivo de chave de roteamento   |

Crie os segredos usando o Azure CLI. Veja abaixo um exemplo.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de Solução de nuvem do Azure.

Agora, implante um cluster de OpenShift:

- [Implantar a plataforma de contêiner do OpenShift](./openshift-container-platform-3x.md)
- [Implantar oferta de marketplace auto-gerenciada da plataforma de contêineres OpenShift](./openshift-container-platform-3x-marketplace-self-managed.md)
