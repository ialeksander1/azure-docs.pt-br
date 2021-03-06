---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536414"
---
| Recurso | Compartilhamentos de arquivos padrão | Ações de arquivos premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | No mínimo; pagar como você vai | 100 GiB; Provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 100 TiB*, 5 TiB | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| IOPS máximo por ação | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivos | 5 | 5 |
| Throughput de destino para um único compartilhamento de arquivo | até 300 MiB/seg*, até 60 MiB/seg,  | Veja os valores de participação de arquivos premium e egressos|
| Saída máxima para um único compartilhamento de arquivos | Consulte o throughput de destino de compartilhamento de arquivos padrão | Até 6.204 MiB/s |
| A ingestão máxima para um único compartilhamento de arquivos | Consulte o throughput de destino de compartilhamento de arquivos padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |

\*O padrão nas ações de arquivo padrão é 5 TiB, veja [Ativar e criar grandes compartilhamentos de arquivos](../articles/storage/files/storage-files-how-to-create-large-file-share.md) para obter os detalhes sobre como aumentar a escala de compartilhamentos de arquivos padrão até 100 TiB.
