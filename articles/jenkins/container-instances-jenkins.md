---
title: Jenkins construir em instância de contêiner
description: Saiba como configurar um servidor Jenkins para executar trabalhos de construção sob demanda em Instâncias de Contêineres do Azure
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617964"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Usar as Instâncias de Contêiner do Azure como um agente de build Jenkins

As instâncias de contêiner do Azure (ACI) fornecem um ambiente sob demanda, expansível e isolado para executar as cargas de trabalho em contêiner. Devido a esses atributos, ACI faz uma grande plataforma para executar os trabalhos internos de Jenkins em grande escala. Este artigo percorre a implantação e o uso de um servidor Jenkins pré-configurado com o ACI como um destino de compilação.

Para obter mais informações sobre as Instâncias de Contêiner do Azure, consulte [Sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Implantar um servidor Jenkins

1. No Portal do Azure, selecione **Criar um recurso** e pesquise **Jenkins**. Selecione a oferta Jenkins com um publicador da **Microsoft** e selecione **Criar**.

2. Insira as informações a seguir no formulário **Básico**, e em seguida, selecione **OK**.

   - **Nome** - insira um ome para a implantação do Jenkins.
   - **Nome de usuário**: insira um nome para o usuário administrador da máquina virtual Jenkins.
   - **Tipo de autenticação**: Nós recomendamos uma chave pública de SSH para autenticação. Se você selecionar essa opção, cole uma chave pública SSH a ser usada para fazer logon na máquina virtual do Jenkins.
   - **Assinatura**: selecione uma assinatura do Azure.
   - No **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.
   - **Local**: selecione um local para o servidor Jenkins.

   ![Configurações básicas da implantação do Portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. No **formulário Configurações adicionais,** preencha os seguintes itens:

   - **Tamanho** - Selecione a opção de tamanho apropriado para sua máquina virtual de Jenkins.
   - **Tipo de disco de VM** - Especifique **HDD** (unidade de disco rígido) ou **SSD** (unidade de estado sólido) para o servidor Jenkins.
   - **Rede virtual**: selecione a seta se desejar modificar as configurações padrão.
   - **Sub-redes**: Selecione sub-redes, verifique as informações e selecione **OK**.
   - **Endereço IP público**: selecione a seta para fornecer ao endereço IP público um nome personalizado, configure o SKU e defina o método de atribuição.
   - **Rótulo do nome de domínio** - Especifique o valor para a URL totalmente qualificada para a máquina virtual do Jenkins.
   - **Tipo de versão do Jenkins** - Selecione o tipo de versão desejada nas opções: **LTS**, **Construção semana**, ou **Azure Verified**.

   ![Configurações adicionais de implantação do portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Para integração da entidade de serviço, selecione **Auto(MSI)** para ter [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) e criar automaticamente uma identidade de autenticação para a instância do Jenkins. Selecione **Manual** para fornecer as próprias credenciais da entidade de serviço.

5. Agentes de nuvem configuram uma plataforma baseada em nuvem para trabalhos de build do Jenkins. Para o propósito deste documento, selecione **ACI**. Com o agente de nuvem ACI, cada trabalho de build do Jenkins é executado em uma instância de contêiner.

   ![Configurações de integração de nuvem de implantação do portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Após concluir as configurações de integração, clique em **OK** e, em seguida, em **OK** novamente no resumo de validação. Selecione **Criar** no resumo dos **Termos de uso**. O servidor Jenkins demora alguns minutos para implantar.

## <a name="configure-jenkins"></a>Configurar o Jenkins

1. No Portal do Azure, navegue até o Grupo de Recursos do Jenkins, selecione a máquina virtual Jenkins e anote o nome DNS.

   ![Nome DNS em detalhes sobre a máquina virtual de Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Navegue até o nome DNS da VM do Jenkins e copie a cadeia de caracteres SSH retornada.

   ![Instruções de logon do Jenkins com cadeias de caracteres SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Abra uma sessão de terminal no sistema de desenvolvimento e cole a cadeia de caracteres SSH da última etapa. Atualize `username` username para o nome de usuário especificado ao implantar o servidor Jenkins.

4. Depois que a sessão é conectada, execute o comando a seguir para recuperar a senha inicial do administrador:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Deixe a sessão SSH e o túnel em execução e navegue até `http://localhost:8080` em um navegador. Cole a senha de administrador inicial na caixa e, em seguida, selecione **Continuar**.

   ![Tela "Desbloquear Jenkins" com a caixa para a senha do administrador](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selecione **Instalar plug-ins sugeridos** para instalar todos os plug-ins recomendados do Jenkins.

   ![Tela "Personalizar Jenkins" com "Instalar o plug-ins sugeridos" selecionado](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Crie uma nova conta de usuário administrador. Essa conta é usada para fazer logon e trabalhar com a instância do Jenkins.

   ![Tela "Criar o primeiro usuário Admin", com as credenciais preenchidas em](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecione **Salvar e Concluir**, em seguida, selecione **Começar a usar o Jenkins** para concluir a configuração.

O Jenkins agora está configurado e pronto para compilar e implantar código. Para este exemplo, um aplicativo Java simples é usado para demonstrar uma compilação do Jenkins em Instâncias de Contêiner do Azure.

## <a name="create-a-build-job"></a>Criar um trabalho de build

Agora, um trabalho de build do Jenkins é criado para demonstrar builds dos Jenkins em uma instância de contêiner do Azure.

1. Selecione **Novo Item**, dê ao projeto de build um nome como **aci-demo**, selecione **Freestyle project** e selecione **OK**.

   ![Caixa para o nome do trabalho de compilação e a lista de tipos de projeto](./media/container-instances-jenkins/jenkins-new-job.png)

2. Em **Geral**, assegure-se de que **Restringir onde este projeto pode ser executado** está selecionado. Insira **linux** para a expressão de rótulo. Essa configuração assegura que esse trabalho de build execute na nuvem do ACI.

   ![Guia "Geral" com detalhes de configuração](./media/container-instances-jenkins/jenkins-job-01.png)

3. Sob **Build**, selecione **Adicionar etapa de compilação** e selecione **Executar Shell**. Insira `echo "aci-demo"` como o comando.

   ![Guia "Criar" com as seleções para a etapa de compilação](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecione **Salvar**.

## <a name="run-the-build-job"></a>Executar o trabalho de build

Para testar o trabalho de build e observar as Instâncias de Contêiner do Azure como a plataforma de build, inicie um build manualmente.

1. Selecione **Compilar Agora** para iniciar um trabalho de build. Demora alguns minutos para o trabalho iniciar. Você deverá ver um status semelhante à seguinte imagem:

   ![Informações de "Compilar histórico" com o status do trabalho](./media/container-instances-jenkins/jenkins-job-status.png)

2. Enquanto o trabalho estiver em execução, abra o Portal do Azure e analise o grupo de recursos do Jenkins. Você deverá ver que uma Instância de Contêiner foi criada. O trabalho do Jenkins é executado dentro desta instância.

   ![Instância de contêiner no grupo de recursos](./media/container-instances-jenkins/jenkins-aci.png)

3. Como o Jenkins executa mais trabalhos do que o número configurado de executores do Jenkins (padrão 2), várias instâncias de contêiner são criadas.

   ![Instâncias de contêiner recém-criadas](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Depois que todos os trabalhos de build forem concluídos, as instâncias de contêiner serão removidas.

   ![Grupo de recursos com instâncias de contêiner removido](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>O plug-in do Jenkins de solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [CI/CD no Serviço de Aplicativo do Azure](java-deploy-webapp-tutorial.md)