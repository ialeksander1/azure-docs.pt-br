---
title: Acesse registros de consulta lenta - Azure CLI - Banco de dados Azure para MariaDB
description: Este artigo descreve como acessar os logs lentos no Banco de Dados Do Azure para MariaDB usando o utilitário de linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270529"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configure e acesse registros de consulta lenta usando o Azure CLI
Você pode baixar o banco de dados do Azure para logs de consulta lenta do MariaDB usando o Azure CLI, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- A [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar o registro em log
É possível configurar o servidor para acessar os logs de consulta lenta do MySQL executando as seguintes etapas:
1. Ative o registro de consulta lenta definindo o parâmetro **de log de consulta\_\_lenta** para ON.
2. Selecione onde fazer a saída dos logs para usar a **saída de log\_**. Para enviar logs para o armazenamento local e para os logs de diagnóstico do Monitor do Azure, selecione **Arquivo**. Para enviar logs somente para logs do Monitor do Azure, selecione **Nenhum**
3. Ajuste outros parâmetros como **long\_query\_time** e **log\_slow\_admin\_statements**.

Para saber como definir o valor desses parâmetros por meio da CLI do Azure, consulte [Como configurar parâmetros do servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longo para 10 segundos e desativa o registro em log da instrução de admin lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Listar logs para o servidor do Banco de Dados do Azure para MariaDB
Se **log_output** estiver configurado como "Arquivo", você poderá acessar logs diretamente do armazenamento local do servidor. Para listar os arquivos de log de consulta lenta disponíveis para o servidor, execute o comando [az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Você pode listar os arquivos de log para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup**. Em seguida, direcione-os para um arquivo de texto chamado **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Se **log_output** estiver configurado para "Arquivo", você poderá baixar arquivos de log individuais do seu servidor com o comando [az mariadb server-logs download.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download)

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup** para o ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [os logs de consulta lenta no Banco de Dados Do Azure para MariaDB](concepts-server-logs.md).
