---
title: Diretrizes para Coleções Confiáveis
description: Diretrizes e recomendações para o uso de coleções confiáveis de malha de serviço em um aplicativo de malha de serviço do Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398535"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Diretrizes e recomendações para Coleções Confiáveis no Azure Service Fabric
Esta seção fornece diretrizes para usar o Gerenciador de Estado Confiável e Coleções Confiáveis. A meta é ajudar os usuários a evitar armadilhas comuns.

As diretrizes são organizadas como recomendações simples prefixadas com *uma ação a ser realizada* e os termos *Considere*, *Evite* e *Não*.

* Não modifique um objeto de tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). As Coleções Confiáveis, como as Coleções Simultâneas, retornam uma referência aos objetos e não uma cópia.
* Faça uma cópia em profundidade do objeto de tipo personalizado retornado antes de modificá-lo. Como estruturas e tipos internos são passagem-por-valor, você não precisa fazer uma cópia em profundidade deles, a menos que eles contenham campos de tipo de referência ou propriedades que você pretende modificar.
* Não use `TimeSpan.MaxValue` para tempos limites. Tempos limite devem ser usados para detectar deadlocks.
* Não use uma transação depois que ela tiver sido confirmada, anulada ou descartada.
* Não use uma enumeração fora do escopo da transação que em foi criado.
* Não crie uma transação dentro `using` da declaração de outra transação porque ela pode causar impasses.
* Não crie estado `IReliableStateManager.GetOrAddAsync` confiável com e use o estado confiável na mesma transação. Isso resulta em uma Operação Operacional InválidaExcede.
* Certifique-se de que a implementação de `IComparable<TKey>` esteja correta. O sistema depende de `IComparable<TKey>` para mesclar os pontos de verificação.
* Use bloqueio de atualização durante a leitura de um item com uma intenção de atualizá-lo para impedir determinada classe de deadlocks.
* Considere manter o número de Coleções Confiáveis por partição inferior a 1000. Prefira Coleções Confiáveis com mais itens em vez de mais Coleções Confiáveis com menos itens.
* Considere manter seus itens (por exemplo, TKey + TValue para o Dicionário Confiável) abaixo de 80 KBytes: quanto menor, melhor. Isso reduz a quantidade de uso do Heap de Objetos Grandes, bem como os requisitos de disco e de E/S da rede. Muitas vezes, isso reduz a replicação de dados duplicados quando apenas uma pequena parte do valor está sendo atualizada. Uma maneira comum de fazer isso no Dicionário Confiável é dividir as linhas em várias linhas.
* Considere o uso da funcionalidade de backup e restauração para ter uma recuperação de desastre.
* Evite a mistura de operações de entidade única e operações de várias entidades (por exemplo, de `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido a níveis de isolamento diferentes.
* Trate InvalidOperationException. As transações do usuário podem ser anuladas pelo sistema por diversos motivos. Por exemplo, quando o Gerenciador de Estado Confiável muda sua função Primária ou quando uma transação de longa execução bloqueia o truncamento do log transacional. Nesses casos, o usuário pode receber InvalidOperationException, indicando que a transação já foi encerrada. Supondo que o encerramento da transação não foi solicitado pelo usuário, a melhor maneira de tratar essa exceção é descartar a transação, verificar se o token de cancelamento foi sinalizado (ou se a função da réplica foi alterada) e, caso não tenha, criar uma nova transação e tentar novamente.  

Eis aqui algumas coisas que se deve manter em mente:

* O tempo limite padrão é de 4 segundos para todas as APIs de Coleções Confiáveis. A maioria dos usuários deve usar o tempo limite padrão.
* O token de cancelamento padrão é `CancellationToken.None` em todas as APIs de Coleções Confiáveis.
* O parâmetro de tipo de chave (*TKey*) para um Dicionário Confiável deve implementar corretamente `GetHashCode()` e `Equals()`. As chaves devem ser imutáveis.
* Para obter alta disponibilidade para as Coleções Confiáveis, cada serviço deve ter pelo menos um destino e uma réplica mínima com tamanho definido como 3.
* As operações de leitura no secundário podem ler versões que não são confirmadas por quorum.
  Isso significa que uma versão dos dados que é lida por meio de um único secundário pode ter um progresso falso.
  As leituras do Primário são sempre estáveis: elas nunca podem ter um progresso falso.
* Segurança/privacidade dos dados persistidos pelo seu aplicativo em uma coleção confiável é a sua decisão e estão sujeitas às proteções fornecidas pelo seu gerenciamento de armazenamento; OU SEJA Criptografia de disco do sistema operacional pode ser usada para proteger seus dados em repouso.  

## <a name="volatile-reliable-collections"></a>Coleções confiáveis voláteis
Ao decidir usar coleções confiáveis voláteis, considere o seguinte:

* ```ReliableDictionary```tem suporte volátil
* ```ReliableQueue```tem suporte volátil
* ```ReliableConcurrentQueue```não tem suporte volátil
* Serviços persistidos NÃO PODEM ser voláteis. Mudar ```HasPersistedState``` a ```false``` bandeira para requer a recriação de todo o serviço do zero
* Serviços voláteis NÃO PODEM ser feitos persistindo. Mudar ```HasPersistedState``` a ```true``` bandeira para requer a recriação de todo o serviço do zero
* ```HasPersistedState```é uma configuração de nível de serviço. Isso significa que **todas as** coleções serão perpersistidas ou voláteis. Você não pode misturar coleções voláteis e persistentes
* A perda de quórum de uma partição volátil resulta em perda completa de dados
* Backup e restauração NÃO estão disponíveis para serviços voláteis

## <a name="next-steps"></a>Próximas etapas
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e Bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gerenciando dados
  * [Backup e Restauração](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* Outras pessoas
  * [Início Rápido dos Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
