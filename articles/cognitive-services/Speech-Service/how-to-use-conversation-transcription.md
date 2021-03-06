---
title: Transcrição da conversa em tempo real (Preview) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a transcrição de conversação em tempo real com o Speech SDK. Disponível para C++, C#e Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520903"
---
# <a name="real-time-conversation-transcription-preview"></a>Transcrição da conversa em tempo real (Pré-visualização)

A API **ConversationTranscriber** do Speech SDK permite transcrever reuniões e outras conversas com a capacidade de adicionar, `PullStream` remover `PushStream`e identificar vários participantes através do streaming de áudio para o serviço de fala usando ou . Este tópico exige que você saiba como usar o Speech-to-text com o Speech SDK (versão 1.8.0 ou posterior). Para obter mais informações, consulte [O que são serviços de fala](overview.md).

## <a name="limitations"></a>Limitações

- A API ConversationTranscriber é suportada para C++, C#e Java no Windows, Linux e Android.
- Atualmente disponível nas línguas "en-US" e "zh-CN" nas seguintes regiões: _centrale_ _eastasia_.
- Requer uma matriz circular de 7 microfones com um fluxo de referência de reprodução. A matriz do microfone deve atender à [nossa especificação.](https://aka.ms/sdsdk-microphone)
- O [Speech Devices SDK](speech-devices-sdk.md) fornece dispositivos adequados e um aplicativo de amostra demonstrando transcrição de conversação.

## <a name="optional-sample-code-resources"></a>Recursos opcionais de código de amostra

O Speech Device SDK fornece código de amostra em Java para captura de áudio em tempo real usando 8 canais.

- [Código de amostra do dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Código de amostra do Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do serviço speech. Você pode [obter uma assinatura de teste de discurso](https://azure.microsoft.com/try/cognitive-services/) se você não tiver um.

## <a name="create-voice-signatures"></a>Crie assinaturas de voz

O primeiro passo é criar assinaturas de voz para os participantes da conversa para uma identificação eficiente dos palestrantes.

### <a name="audio-input-requirements"></a>Requisitos de entrada de áudio

- O arquivo de onda de áudio de entrada para a criação de assinaturas de voz deve estar em amostras de 16 bits, taxa de amostra de 16 kHz e um formato de canal único (mono).
- O comprimento recomendado para cada amostra de áudio é entre trinta segundos e dois minutos.

### <a name="sample-code"></a>Código de exemplo

O exemplo a seguir mostra duas maneiras diferentes de criar assinatura de voz [usando a API REST](https://aka.ms/cts/signaturegenservice) em C#. Observe que você precisará substituir informações reais por "YourSubscriptionKey", seu nome de arquivo de `{region}` onda para "speakerVoice.wav", e sua região para e "YourServiceRegion"_(centralus_ ou _eastasia)._

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Transcreve conversas

O código de amostra a seguir demonstra como transcrever conversas em tempo real para três alto-falantes. Ele assume que você já criou assinaturas de voz para cada orador, como mostrado acima. Substitua as informações reais por "YourSubscriptionKey" e "YourServiceRegion" ao criar o objeto SpeechConfig.

Os destaques do código de amostra incluem:

- Criando `Conversation` um objeto `SpeechConfig` a partir do objeto usando um identificador de reunião gerado usando`Guid.NewGuid()`
- Criando `ConversationTranscriber` um objeto e `JoinConversationAsync()` junte-se à conversa com para iniciar a transcrição
- Registrando os eventos de interesse
- Adicionando ou removendo participantes para a conversa usando o objeto Conversação
- Streaming do áudio
- No Speech SDK versão 1.9.0 `int` `string` e em diante ambos os tipos de valor são suportados no campo de versão de assinatura de voz.

A transcrição e o identificador do alto-falante voltam nos eventos registrados.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transcrição de conversas assíncrona](how-to-async-conversation-transcription.md)
