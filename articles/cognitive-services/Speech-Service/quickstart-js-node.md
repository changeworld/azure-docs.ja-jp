---
title: クイック スタート:Speech Service SDK を使用して Node.js の JavaScript で音声を認識する
titleSuffix: Azure Cognitive Services
description: Speech Service SDK を使用して Node.js の JavaScript で音声を認識する方法を学習する
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724435"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>クイック スタート:Speech Service SDK を使用して Node.js の JavaScript で音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、音声をテキストに文字起こしする Node.js プロジェクトを、Cognitive Services Speech SDK の JavaScript バインドを使用して作成する方法について説明します。
このアプリケーションは、Microsoft [Cognitive Services Speech SDK](https://aka.ms/csspeech/npmpackage) が基になっています。

## <a name="prerequisites"></a>前提条件

* Speech Service の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="create-a-new-project-folder"></a>新しいプロジェクト フォルダーを作成する

新しい空のフォルダーを作成し、新しい JavaScript と Node.js プロジェクトとして初期化します。

```sh
npm init -f
```

これにより、package.json ファイルが既定値で初期化されます。 後でこのファイルを編集する場合があります。

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript 用 Speech SDK をフォルダーにインストールする

`npm install microsoft-cognitiveservices-speech-sdk` を介して Node.js プロジェクトに Speech SDK を追加します。

これにより、npmjs から Speech SDK の最新バージョンと必要な前提条件がダウンロードされ、インストールされます。 SDK は、プロジェクト フォルダー内の `node_modules` ディレクトリにインストールされます。

## <a name="using-the-speech-sdk"></a>Speech SDK を使用する

フォルダーに `index.js` という名前の新しいファイルを作成し、テキスト エディターでこのファイルを開きます。

> [!NOTE]
> Node.js では、Speech SDK はマイクまたはファイルのデータの種類をサポートしていないことに注意してください。 どちらもブラウザーでのみサポートされています。 代わりに、`AudioInputStream.createPushStream()` または `AudioInputStream.createPullStream()` を通じて、Speech SDK への Stream インターフェイスを使用します。

この例では、`PushAudioInputStream` インターフェイスを使用します。

次の JavaScript コードを追加します。

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>コマンド ラインでサンプルを実行する

アプリを起動するには、`YourSubscriptionKey`、`YourServiceRegion`、および `YourAudioFile.wav` を構成に合わせて調整します。 その後、次のコマンドを呼び出すと、アプリを実行できます。

```sh
node index.js
```

指定したファイル名を使用して認識が開始され、出力がコンソールに表示されます。

以下に、サブスクリプション キーを更新し、`whatstheweatherlike.wav` ファイルを使用して `index.js` を実行した場合のサンプル出力を示します。

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>Visual Studio Code からサンプルを実行する

Visual Studio Code からもサンプルを実行できます。 クイック スタートをインストールし、開き、実行するには、以下の手順に従います。

1. Visual Studio Code を起動し、[フォルダーを開く] をクリックして、クイック スタートのフォルダーに移動します

   ![[フォルダーを開く] のスクリーンショット](media/sdk/qs-js-node-01-open_project.png)

1. Visual Studio Code でターミナルを開きます

   ![ターミナル ウィンドウのスクリーンショット](media/sdk/qs-js-node-02_open_terminal.png)

1. npm を実行して、依存関係をインストールします

   ![npm のインストールのスクリーンショット](media/sdk/qs-js-node-03-npm_install.png)

1. これで、`index.js` を開き、ブレークポイントを設定する準備が整いました

   ![16 行目にブレークポイントを設定した index.js のスクリーンショット](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. デバッグを開始するには、F5 キーを押すか、メニューで [デバッグ]、[デバッグの開始] の順に選択します

   ![デバッグ メニューのスクリーンショット](media/sdk/qs-js-node-05-start_debugging.png)

1. ブレークポイントがヒットしたら、呼び出し履歴と変数を検査することができます

   ![デバッガーのスクリーンショット](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 出力は、デバッグ コンソール ウィンドウに表示されます

   ![デバッグ コンソールのスクリーンショット](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Node.js のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
