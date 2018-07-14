---
title: Azure 上で Custom Speech Service でカスタム音声エンドポイントを使用する | Microsoft Docs
description: Cognitive Services の Custom Speech Service でカスタム Speech-to-Text エンドポイントを使用する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373312"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>カスタム Speech-to-Text エンドポイントの使用
既定の Cognitive Services 音声エンドポイントと同様の方法で、Azure Custom Speech Service の Speech-to-Text エンドポイントに要求を送信できます。 これらのエンドポイントは、Speech API の既定のエンドポイントと機能的には同じです。 そのため、Speech API のクライアント ライブラリまたは REST API を介して使用できる同じ機能を、カスタム エンドポイントでも使用できます。

このサービスを使用して作成するエンドポイントは、さまざまな数の同時要求を処理できます。 ボリュームは、サブスクリプションに関連付けられている価格レベルによって変わります。 受信する要求が多すぎると、エラーが発生します。 無料レベルには要求数の毎月の上限があります。

このサービスは、データがリアルタイムで送信されることを前提としています。 より速く送信された場合、リアルタイムの音声の時間が経過するまで、要求は実行中と見なされます。

> [!NOTE]
> [新しい Web ソケット](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)はまだサポートしています。 カスタム音声エンドポイントと共に Web ソケットを使用する予定の場合は、このドキュメントの指示に従ってください。
>
> 新しい [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) のサポートが間もなく始まります。 HTTP を介してカスタム音声エンドポイントを呼び出す予定の場合は、このドキュメントの指示に従ってください。
>

## <a name="send-requests-by-using-the-speech-client-library"></a>音声クライアント ライブラリを使用して要求を送信する

音声クライアント ライブラリを使用してカスタム エンドポイントに要求を送信するには、認識クライアントを起動します。 [NuGet](http://nuget.org/) の Client Speech SDK を使用します。 *speech recognition* を検索し、使用しているプラットフォームの Microsoft の音声認識パッケージを選択します。 いくつかのサンプル コードが [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows) にあります。 Client Speech SDK には、次のメソッドを提供するファクトリ クラス **SpeechRecognitionServiceFactory** があります。

  *   ```CreateDataClient(...)```: データ認識クライアント。
  *   ```CreateDataClientWithIntent(...)```: 意図を含むデータ認識クライアント。
  *   ```CreateMicrophoneClient(...)```: マイク認識クライアント。
  *   ```CreateMicrophoneClientWithIntent(...)```: 意図を含むマイク認識クライアント。

詳細なドキュメントについては、[Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home) に関するページを参照してください。 Custom Speech Service エンドポイントは、同じ SDK をサポートしています。

データ認識クライアントは、ファイルや他のオーディオ ソースなど、データからの音声認識に適しています。 マイク認識クライアントは、マイクからの音声認識に適しています。 実際のシナリオに適した LUIS アプリを構築した場合、いずれのクライアントの意図を使用しても、[Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS) から構造化された意図の結果を返すことができます。

4 種類のクライアントはすべて 2 つの方法でインスタンス化できます。 最初の方法は、標準の Cognitive Services Speech API を使用します。 2 つ目の方法では、Custom Speech Service で作成したカスタム エンドポイントに対応する URL を指定できます。

たとえば、次のメソッドを使用してカスタム エンドポイントに要求を送信する **DataRecognitionClient** を作成できます。

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**your_subscriptionId** と **endpointURL** は、それぞれ **[展開情報]** ページのサブスクリプション キーと Web ソケット URL を参照しています。

**AuthenticationUri** は、認証サービスからトークンを受け取るために使用されます。 この URI は、次のサンプル コードに示すように、個別に設定する必要があります。

このサンプル コードは、クライアント SDK の使用方法を示しています。

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> SDK の **Create** メソッドを使用する場合、**Create** メソッドのオーバーロードのため、サブスクリプション ID を 2 回指定する必要があります。
>

Custom Speech Service は、短い形式と長い形式の認識用に 2 つの異なる URL を使用しています。 両方が **[デプロイ]** ページに表示されます。 使用する特定の形式に合わせて正しいエンドポイント URL を使用してください。

カスタム エンドポイントでさまざまな認識クライアントを呼び出す方法の詳細については、[SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) クラスを参照してください。 このページのドキュメントは、音響モデルの適応について触れていますが、このドキュメントは Custom Speech Service を使用して作成されたすべてのエンドポイントに適用されます。

## <a name="send-requests-by-using-the-speech-protocol"></a>Speech プロトコルを使用して要求を送信する

[Speech プロトコル](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)に対して表示されるエンドポイントは、オープン ソースの Web Socket Speech プロトコルのエンドポイントです。

現在、唯一の公式クライアント実装は [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) です。 リンク先で提供されているサンプルから始める場合は、コードを次のように変更してサンプルを再ビルドしてください。

1. _src\sdk\speech.browser\SpeechConnectionFactory.ts_ のホスト名 "wss://speech.platform.bing.com" を実際のデプロイの詳細ページに表示されているホスト名に置き換えます。 ここには完全な URI を挿入せずに、*wss* プロトコル スキームとホスト名だけのみ挿入します。 例: 

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. 必要に応じて、_samples\browser\Samples.html_ に _recognitionMode_ パラメーターを設定します。
    * _RecognitionMode.Interactive_ は最大 15 秒まで要求をサポートします。
    * _RecognitionMode.Conversation_ と _RecognitionMode.Dictation_ (Custom Speech Service ではどちらも同等) は、最大 10 分間のサポート要求をサポートします。

3. サンプルを使用する前に "gulp build" を使用してサンプルをビルドします。

> [!NOTE]
> このプロトコルに適した URI を使用していることを確認します。 必要なスキームは *wss* です (クライアント プロトコルのように *http* ではありません)。 

詳細については、[Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) のドキュメントを参照してください。

## <a name="send-requests-by-using-http"></a>HTTP を使用して要求を送信する

HTTP の POST を使用してカスタム エンドポイントに要求を送信する処理は、HTTP による要求を Cognitive Services Bing Speech API に送信する処理と似ています。 カスタム デプロイのアドレスを反映するように URL を変更します。

Cognitive Services Speech エンドポイントと、このサービスで作成されたカスタム エンドポイントの両方に対して、HTTP 経由で送信される要求にはいくつかの制限があります。 HTTP 要求は、認識処理中に部分的な結果を返すことができません。 さらに、要求時間は、オーディオ コンテンツの場合は 10 秒に制限され、全体では 14 秒に制限されています。

POST 要求を作成するには、Cognitive Services Speech API に使用する場合と同じプロセスに従います。

1. サブスクリプション ID を使用してアクセス トークンを取得します。 このトークンは認識エンドポイントにアクセスするために必要です。 このトークンは 10 分間再利用することができます。

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **subscriptionId** は、このデプロイに使用するサブスクリプション ID に設定する必要があります。 応答は、次の要求に必要なプレーンなトークンです。

2. もう一度 POST を使用してエンドポイントにオーディオを送信します。

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **トークン** は、以前の呼び出しで受け取ったアクセス トークンです。 **https_endpoint** は、**[展開情報]** ページに表示されるカスタム Speech-to-Text エンドポイントの完全なアドレスです。

HTTP POST パラメーターと応答の形式の詳細については、[Microsoft Cognitive Services Bing Speech HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation) に関するページを参照してください。

## <a name="send-requests-by-using-the-service-library"></a>Service ライブラリを使用して要求を送信する
Service ライブラリを使用すると、Microsoft Speech トランスクリプション クラウドを利用してリアルタイムで話し言葉をテキストに変換することができます。その結果、クライアント アプリがオーディオを送信し、同時に部分的な認識結果を非同期に受信できるようになります。 Service SDK の詳細については[こちら](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)を参照してください。

> [!NOTE]
> Service ライブラリを使用する場合は、**IAuthorizationProvider** の実装で承認プロバイダーの URI を https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken に変更する必要があります。

## <a name="next-steps"></a>次の手順
* [カスタム音響モデル](cognitive-services-custom-speech-create-acoustic-model.md)での精度を向上します。
* [カスタム言語モデル](cognitive-services-custom-speech-create-language-model.md)での精度を向上します。
