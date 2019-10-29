---
title: クイック スタート:音声を認識する、Java (Android) - Speech Service
titleSuffix: Azure Cognitive Services
description: Android で Java と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675566"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>クイック スタート:Android で Speech SDK を使用して Java で音声を認識する

[音声合成](quickstart-text-to-speech-java-android.md)と[音声優先仮想アシスタント](quickstart-virtual-assistant-java-android.md)のクイックスタートも利用できます。

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Azure Cognitive Services Speech SDK を使用して音声をテキストに文字起こしする Android 向け Java アプリケーションを開発する方法について説明します。

このアプリケーションは、Speech SDK Maven パッケージと Android Studio 3.3 が基になっています。 現在 Speech SDK は、32 ビットまたは 64 ビットの ARM プロセッサおよび Intel x86 または x64 互換のプロセッサを搭載した Android デバイスと互換性があります。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳細については、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-and-configure-a-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>ユーザー インターフェイスを作成する

ここでは、アプリケーションの基本的なユーザー インターフェイスを作成します。 メイン アクティビティのレイアウト `activity_main.xml` を編集します。 初期状態のレイアウトには、アプリケーションの名前が表示されるタイトル バーと、"Hello World!" というテキストが表示される TextView が含まれています。

* TextView 要素を選択します。 右上隅にあるその ID 属性を `hello` に変更します。

* `activity_main.xml` ウィンドウの左上のパレットから、テキストの上の何もないスペースにボタンをドラッグします。

* 右側のボタンの属性で、`onClick` 属性の値に「`onSpeechButtonClicked`」と入力します。 ボタン イベントを処理するこの名前を持つメソッドを記述します。 右上隅にあるその ID 属性を `button` に変更します。

* レイアウトの制約を推測したい場合、デザイナーの上部にある魔法の杖のアイコンを使用します。

  ![魔法の杖アイコンのスクリーンショット](media/sdk/qs-java-android-10-infer-layout-constraints.png)

この時点で、UI のテキストおよびグラフィカル表現は次のようになります。

![ユーザー インターフェイス](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル `MainActivity.java` を開きます。 このファイル内のすべてのコードを次の内容に置き換えます。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` メソッドには、マイクとインターネットのアクセス許可を要求し、ネイティブ プラットフォーム バインディングを初期化するコードが含まれています。 ネイティブ プラットフォーム バインディングの構成は 1 回だけ必要です。 アプリケーションの初期化中に早期に行う必要があります。

   * メソッド `onSpeechButtonClicked` は上述の通りボタン クリック ハンドラーです。 ボタンを押下すると、音声からテキストへの文字起こしがトリガーされます。

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. さらに、文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. Android デバイスを開発用 PC に接続します。 デバイスで[開発モードと USB デバッグ](https://developer.android.com/studio/debug/dev-options)を有効にしていることを確認します。

1. アプリケーションをビルドするには、Ctrl + F9 キーを押すか、メニュー バーから **[Build]\(ビルド\)**  >  **[Make Project]\(プロジェクトの作成\)** を選択します。

1. アプリケーションを起動するには、Shift + F10 キーを押すか、 **[Run]\(実行\)**  >  **[Run 'app']\("アプリ" の実行\)** を選択します。

1. 表示された配置ターゲット ウィンドウで、対象の Android デバイスを選択します。

   ![配置ターゲットの選択ウィンドウのスクリーンショット](media/sdk/qs-java-android-12-deploy.png)

アプリケーションのボタンを押して音声認識に関するセクションを開始します。 次の 15 秒間の英語スピーチが Speech Services に送信されて文字起こしされます。 結果は Android アプリケーションと Android Studio の logcat ウィンドウに表示されます。

![Android アプリケーションのスクリーンショット](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
