---
title: 'クイック スタート: 音声認識 (Javascript (ブラウザー)) - 音声サービス'
titleSuffix: Azure Cognitive Services
description: JavaScript から Speech Service SDK を使用してブラウザーで音声を認識する方法について説明します
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: f8b6f4d39ce4dc65d691ec6a0e3d0734abb19734
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087917"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-service-sdk"></a>クイック スタート: JavaScript から Speech Service SDK を使用してブラウザーで音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、音声をテキストに文字起こしする Web サイトを、Cognitive Services Speech SDK の JavaScript バインドを使用して作成する方法について説明します。
このアプリケーションは、Microsoft Cognitive Services Speech SDK ([ダウンロード バージョン 1.1.0](https://aka.ms/csspeech/jsbrowserpackage)) が基になっています。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 「[Speech サービスを無料で試す](get-started.md)」を参照してください。
* 機能するマイクを備えた PC または Mac。
* テキスト エディター。
* 現在のバージョンの Chrome または Microsoft Edge。
* 必要に応じて、PHP スクリプトのホストをサポートしている Web サーバー。

## <a name="create-a-new-website-folder"></a>新しい Web サイト フォルダーを作成する

新しい空のフォルダーを作成します。 Web サーバーでサンプルをホストする場合は、Web サーバーがフォルダーにアクセスできることを確認します。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript 用 Speech SDK をフォルダーに解凍する

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Speech SDK を [.zip パッケージ](https://aka.ms/csspeech/jsbrowserpackage)としてダウンロードし、新しく作成したフォルダーに解凍します。 これにより、`microsoft.cognitiveservices.speech.sdk.bundle.js` と `microsoft.cognitiveservices.speech.sdk.bundle.js.map` の 2 つのファイルが解凍されます。
後者のファイルはオプションであり、必要に応じて SDK コードのデバッグを支援するために使用します。

## <a name="create-an-indexhtml-page"></a>index.html ページを作成する

フォルダーに `index.html` という名前の新しいファイルを作成し、テキスト エディターでこのファイルを開きます。

1. 次の HTML スケルトンを作成します。

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. 次の UI コードを、ファイルの最初のコメントの下に追加します。

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Speech SDK への参照を追加します

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. UI コードで定義されている認識ボタン、認識結果、およびサブスクリプション関連フィールドに、ハンドラーを接続します。

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>トークン ソースを作成する (省略可能)

Web サーバーで Web ページをホストする場合は、デモ アプリケーションに対するトークン ソースを必要に応じて指定できます。
これにより、サブスクリプション キーはサーバーに常に存在するようになり、ユーザーは自分で承認コードを入力しなくても音声機能を使用できます。

1. `token.php` という名前で新しいファイルを作成します。 この例では、Web サーバーが PHP スクリプト言語をサポートするものと想定します。 次のコードを入力します。

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. `index.html` ファイルを編集し、次のコードをファイルに追加します。

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> 承認トークンは有効期間が限られています。
> この簡単な例では、承認トークンを自動更新する方法は示されていません。 ユーザーは、ページを読み込みなおすか、F5 キーを押すことにより、手動で更新できます。

## <a name="build-and-run-the-sample-locally"></a>サンプルをビルドしてローカルに実行する

アプリを起動するには、index.html ファイルをダブルクリックするか、または好みの Web ブラウザーで index.html を開きます。 簡単な GUI が表示されるので、サブスクリプション キーと[リージョン](regions.md)を入力し、マイクを使用して認識を開始することができます。

## <a name="build-and-run-the-sample-via-a-web-server"></a>Web サーバーからサンプルをビルドして実行する

アプリを起動するには、適当な Web ブラウザーを開き、フォルダーをホストしているパブリック URL にアクセスして、[リージョン](regions.md)を入力し、マイクを使用して認識を開始します。 構成した場合、トークン ソースからトークンが取得されます。

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/js-browser` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サンプルを入手する](speech-sdk.md#get-the-samples)
