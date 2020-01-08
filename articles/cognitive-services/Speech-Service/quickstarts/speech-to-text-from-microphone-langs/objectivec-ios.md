---
title: クイック スタート:マイクから音声を認識する、Objective-C - Speech サービス
titleSuffix: Azure Cognitive Services
description: iOS で Objective-C と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380781"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>クイック スタート:iOS で Objective-C と Speech SDK を使用して音声を認識する

[音声合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)のクイックスタートも利用できます。

この記事では、音声を録音したファイルやマイクからの音声をテキストに文字起こしする iOS アプリを、Objective-C と Azure Cognitive Services Speech SDK を使用して作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

以降の手順を開始する前に次の要件を満たしておく必要があります。

* Speech サービス用の[サブスクリプション キー](~/articles/cognitive-services/Speech-Service/get-started.md)。
* macOS マシンと [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降。
* iOS バージョン 9.3 以降に設定されたターゲット。

## <a name="get-the-speech-sdk-for-ios"></a>iOS 用の Speech SDK を取得する

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

iOS 用の Cognitive Services Speech SDK は、現在は Cocoa フレームワークとして配布されています。
[こちらの Web サイト](https://aka.ms/csspeech/iosbinary)からダウンロードできます。 ファイルをホーム ディレクトリにダウンロードします。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode を起動し、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Project]\(プロジェクト\)** の順に選択して新しいプロジェクトを開始します。
テンプレートの選択ダイアログ ボックスで、"**iOS Single View App**" テンプレートを選択します。

以降のダイアログ ボックスで、次のように選択します。

1. **[Project Options]\(プロジェクトのオプション\)** ダイアログ ボックスで、次の手順を実行します。
    1. クイックスタート アプリの名前を入力します (例: *helloworld*)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的のため、*testorg* などの名前を使用します。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として **Objective-C** が選択されていることを確認します。
    1. テストとコア データに関するすべてのチェック ボックスをオフにします。

    ![プロジェクトの設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. プロジェクト ディレクトリを選択します。
   1. ホーム ディレクトリを選択して、プロジェクトを配置します。 この手順により、ホーム ディレクトリ内に helloworld ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが格納されます。
   1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
   1. プロジェクト設定画面で、SDK へのパスを調整します。
      1. **[General]\(全般\)** タブの **[Embedded Binaries]\(埋め込みバイナリ\)** ヘッダーで、 **[Add embedded binaries]\(埋め込みバイナリの追加\)**  >  **[Add other]\(その他の追加\)** を選択して、フレームワークとして SDK ライブラリを追加します。 ホーム ディレクトリに移動して、ファイル `MicrosoftCognitiveServicesSpeech.framework` を選択します。 この操作により、SDK のライブラリがヘッダー **[Linked Framework and Libraries]** に自動的に追加されます。
         ![追加されたフレームワーク](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. **[Build Settings]\(ビルド設定\)** タブに移動し、 **[All]\(すべて\)** 設定を選択します。
      1. ディレクトリ $(SRCROOT)/.. を、 **[Search Paths]\(検索パス\)** という見出し下の **[Framework Search Paths]\(フレームワーク検索パス\)** に追加します。

      ![[Framework Search Paths]\(フレームワーク検索パス\) の設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>UI の設定

このサンプル アプリの UI はごく単純なものです。 音声認識を開始するためのボタンが 2 つ (ファイルから開始するためのボタンとマイク入力から開始するためのボタン) と、結果を表示するためのテキスト ラベルが 1 つあるだけです。 UI はプロジェクトの `Main.storyboard` 部分に設定されます。 プロジェクト ツリーの `Main.storyboard` エントリーを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択して、ストーリーボードの XML ビューを開きます。

自動生成された XML をこのコードに置き換えます。

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. リンクを右クリックし、 **[Save target as]\(対象をファイルに保存\)** を選択して、[サンプルの wav ファイル](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)をダウンロードします。
   Finder ウィンドウからプロジェクト ビューのルート レベルにドラッグすることで、wav ファイルをプロジェクトにリソースとして追加します。
   設定を変更せずに、次のダイアログ ボックスで **[Finish]\(完了\)** をクリックします。
1. 自動生成された `ViewController.m` ファイルの内容を次のコードに置き換えます。

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](~/articles/cognitive-services/Speech-Service/regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。
1. マイクへのアクセスの要求を追加します。 プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. **[View]\(表示\)**  >  **[Debug Area]\(デバッグ エリア\)**  >  **[Activate Console]\(コンソールのアクティブ化\)** の順に選択して、デバッグ出力が表示されるようにします。
1. **[Product]\(製品\)**  >  **[Destination]\(ターゲット\)** メニューの一覧から、お使いの開発マシンに接続された iOS デバイスまたは iOS シミュレーターをアプリのターゲットとして選択します。
1. メニューから **[Product]\(製品\)**  >  **[Run]\(実行\)** の順に選択して、コード例をビルドし、iOS シミュレーターで実行します。 **[Play]\(再生\)** ボタンを選択してもかまいません。
1. アプリの **[Recognize (File)]\(認識 (ファイル)\)** ボタンを選択すると、画面の下部にオーディオ ファイルの内容である "What's the weather like?" が 表示されます。

   ![シミュレートされた iOS アプリ](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. アプリの **[Recognize (Microphone)]\(認識 (マイク)\)** ボタンを選択して何か話すと、発話した内容のテキストが画面の下部に表示されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub で Objective-C のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
