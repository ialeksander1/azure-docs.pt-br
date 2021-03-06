---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422358"
---
## <a name="speech-modes"></a>Modos de fala

**Interativo**
- Destinado a cenários de comando e controle.
- Tem um tempo de segmentação fora do valor de X.
- Ao final de uma declaração reconhecida, o serviço deixa de processar áudio a partir desse ID de solicitação e termina a curva. A conexão não está fechada.
- O limite máximo para reconhecimento é de 20 anos.
- Chamada típica de `RecognizeOnceAsync`carbono para invocar é .

**Conversa**
- Destinado a reconhecimentos mais longos.
- Tem um tempo de segmentação fora do valor de Y. (Y != X)
- Processará várias declarações completas sem terminar a curva.
- Vai acabar com a virada por muito silêncio.
- O carbono continuará com um novo ID de solicitação e reproduzindo áudio conforme necessário.
- O serviço se desligará à força após 10 minutos de reconhecimento de fala.
- O carbono se reconectará e reproduzirá áudio não reconhecido.
- Invocado em `StartContinuousRecognition`Carbono com .

**Ditado**
- Permite que os usuários especifiquem a pontuação falando-a.
- Invocado em Carbono `EnableDictation` especificando `SpeechConfig` no objeto, independentemente da chamada API que inicia o reconhecimento.
- O<sup>1º</sup> cluster `speech.fragment` de partidos retorna mensagens `speech.hypothesis` para resultados intermediários, as mensagens de retorno<sup>de</sup> terceiros.