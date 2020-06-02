---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 244bd6c270b5845039c1a5cc4d1322cb928a5aa3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806565"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。 index.js ファイルを作成して、次のコードを追加します。

`subscriptionKey`、`servcieRegion`、および `filename` の値を入力してください。

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pullaudiooutputstream"></a>PullAudioOutputStream にファイルを読み込む

NodeJS の場合、Speech SDK ではファイル アクセスをネイティブに直接サポートしていないので、ファイルを開いて、`PullAudioOutputStream` を使用して書き込みます。


```JavaScript
// create the push stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and push it to the push stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`SpeechSynthesizer` オブジェクトを初期化するには、サブスクリプション キーとサブスクリプション リージョンを使用する構成を作成する必要があります。 このコードを次に挿入します。

> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>オーディオ構成を作成する

ここで、使用する `PullAudioOutputStream` を指し示す `AudioConfig` オブジェクトを作成する必要があります。 Speech 構成のすぐ下にこのコードを挿入します。

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pullStream);
```

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer を初期化する

ここで、前に作成した `SpeechConfig` オブジェクトと `AudioConfig` オブジェクトを使用して `SpeechSynthesizer` オブジェクトを作成しましょう。

```JavaScript
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>語句を認識し、結果を表示する

`SpeechSynthesizer` オブジェクトから、`speakTextAsync()` メソッドを呼び出します。 このメソッドを使用すると、音声合成用にテキストを送信していることを Speech サービスに知らせることができます。

加えて、返された結果またはエラーをコンソールに書き込み、最後にシンセサイザーを閉じます。
```JavaScript
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);
```
## <a name="check-your-code"></a>コードを確認する
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the pull stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and write it to the pull stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
 
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamOutput(pullStream);
  
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);

}());
```
## <a name="run-the-sample-locally"></a>ローカルでサンプルを実行する

NodeJs を使用してコードを実行する
```bash
node index.js
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]