---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: eur
ms.openlocfilehash: 05d94ddec3c4f5302eb83c430642190759a00561
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508934"
---
## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK for JavaScript </a> をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web ブラウザー </a>

## <a name="create-voice-signatures"></a>声紋を作成する

(事前登録されたユーザー プロファイルを使用して特定の参加者を識別しない場合は、この手順を省略できます。)

ユーザー プロファイルを登録する場合は、最初の手順で会話の参加者の声紋を作成して、一意の話者として識別できるようにします。 声紋を作成するための入力 `.wav` 音声ファイルは、16 ビット、16 kHz のサンプル レート、およびシングル チャンネル (モノラル) 形式である必要があります。 各オーディオ サンプルの推奨される長さは、30 秒間から 2 分間です。 オーディオ サンプルが短すぎると、話者を認識するときの精度が低下します。 一意の音声プロファイルを作成するため、`.wav` ファイルは **1 人の** 音声のサンプルである必要があります。

次の例は、JavaScript で [REST API を使用](https://aka.ms/cts/signaturegenservice)して声紋を作成する方法を示しています。 `subscriptionKey`、`region`、およびサンプル `.wav` ファイルのパスを実際の情報に置き換える必要があることに注意してください。

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

このスクリプトを実行すると、`voiceSignatureString` 変数に声紋文字列が返されます。 この関数を 2 回実行して、下記の `voiceSignatureStringUser1` および `voiceSignatureStringUser2` 変数への入力として使用する 2 つの文字列を作成します。

> [!NOTE]
> 声紋は、REST API を使用して **のみ** 作成できます。

## <a name="transcribe-conversations"></a>会話の文字起こし

次のサンプル コードは、2 人の話者の会話をリアルタイムで文字起こしする方法を示しています。 上の手順に従って各話者の声紋文字列が既に作成されていることを前提としています。 `subscriptionKey`、`region`、および文字起こしする音声のパス `filepath` を、実際の情報に置き換えます。

事前登録されたユーザー プロファイルを使用しない場合、speaker1、speaker2 などとして不明なユーザーの最初の認識を完了するまでに数秒かかります。

> [!NOTE]
> 署名の作成にアプリケーション全体で同じ `subscriptionKey` が使用されていることを確認してください。そうでないと、エラーが発生します。 

このサンプル コードは、次の処理を実行します。

* 文字起こしに使用するプッシュ ストリームを作成し、サンプル `.wav` ファイルをそれに書き込みます。
* `createConversationAsync()` を使用して `Conversation` を作成します。
* コンストラクターを使用して `ConversationTranscriber` を作成します。
* 参加者を会話に追加します。 上記の手順の出力として、文字列 `voiceSignatureStringUser1` と `voiceSignatureStringUser2` が得られます。
* イベントに登録し、文字起こしを開始します。
* 音声サンプルを提供せずに話者を区別したい場合は、[会話の文字起こしの概要](../../../conversation-transcription.md)に関するページにあるように、`DifferentiateGuestSpeakers` 機能を有効にしてください。 

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync(filepath));
    speechTranslationConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // en-us by default. Adding this code to specify other languages, like zh-cn.
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```
