---
title: クイック スタート:マイクから音声を認識する、Swift - Speech サービス
titleSuffix: Azure Cognitive Services
description: iOS 上で Swift と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380526"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>クイック スタート:iOS 上で Swift と Speech SDK を使用して音声を認識する

[音声合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)のクイックスタートも利用できます。

この記事では、Swift と Azure Cognitive Services Speech SDK を使用して、マイクから録音された音声をテキストに文字起こしする iOS アプリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

以降の手順を開始する前に次の要件を満たしておく必要があります。

* Speech サービス用の[サブスクリプション キー](~/articles/cognitive-services/Speech-Service/get-started.md)。
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降と [CocoaPods](https://cocoapods.org/) 以降がインストールされた macOS マシン。

## <a name="get-the-speech-sdk-for-ios"></a>iOS 用の Speech SDK を取得する

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

このチュートリアルは 1.6.0 より前のバージョンの SDK では動作しません。

iOS 用の Cognitive Services Speech SDK は、フレームワーク バンドルとして配布されています。 Xcode プロジェクトで [CocoaPod](https://cocoapods.org/) として使用することも、 https://aka.ms/csspeech/iosbinary からダウンロードして手動でリンクすることも可能です。 この記事では、CocoaPod を使用します。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode を起動し、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Project]\(プロジェクト\)** の順に選択して新しいプロジェクトを開始します。
テンプレートの選択ダイアログ ボックスで、"**iOS Single View App**" テンプレートを選択します。

以降のダイアログ ボックスで、次のように選択します。

1. **[Project Options]\(プロジェクトのオプション\)** ダイアログ ボックスで、次の手順を実行します。
    1. クイックスタート アプリの名前を入力します (例: *helloworld*)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的のため、*testorg* などの名前を使用します。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として **Swift** が選択されていることを確認します。
    1. ストーリーボードを使用するためのチェック ボックスとドキュメントベースのアプリケーションを作成するためのチェック ボックスを無効にします。 サンプル アプリ向けのシンプルな UI がプログラムにより作成されます。
    1. テストとコア データに関するすべてのチェック ボックスをオフにします。
1. プロジェクト ディレクトリを選択します。
    1. プロジェクトの配置先のディレクトリを選択します。 この手順により、選択したディレクトリ内に helloworld ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが格納されます。
    1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
1. アプリによるマイクの使用は、`Info.plist` ファイルでも宣言する必要があります。 対象のファイルを概要でクリックし、 **[Privacy - Microphone Usage Description]\(プライバシー - マイクの用途に関する説明\)** キーを追加して、その値として "*音声認識にマイクが必要*" などを設定します。

    ![Info.plist の設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Xcode プロジェクトを閉じます。 後で CocoaPods の設定後に別のインスタンスを使用します。

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` という名前の新しいヘッダー ファイルを、helloworld プロジェクト内の helloworld ディレクトリに配置します。 このファイルに次のコードを貼り付けます。

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. helloworld ターゲット用の Swift プロジェクトの設定に対するブリッジ ヘッダーの相対パス `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` を **[Objective-C ブリッジ ヘッダー]** フィールドに追加します。

   ![ヘッダーのプロパティ](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. 自動生成された `AppDelegate.swift` ファイルの内容を次のコードに置き換えます。

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. 自動生成された `ViewController.swift` ファイルの内容を次のコードに置き換えます。

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. `ViewController.swift` で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](~/articles/cognitive-services/Speech-Service/regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK を CocoaPod としてインストールする

1. CocoaPod 依存関係マネージャーをその[インストールの手順](https://guides.cocoapods.org/using/getting-started.html)に従ってインストールします。
1. サンプル アプリのディレクトリ (helloworld) に移動します。 そのディレクトリに、次の内容を含んだ *Podfile* という名前のテキスト ファイルを配置します。

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. ターミナルで helloworld ディレクトリに移動して、`pod install` コマンドを実行します。 このコマンドにより、サンプル アプリと依存関係としての Speech SDK の両方を含んだ、`helloworld.xcworkspace` という Xcode ワークスペースが生成されます。 以降の手順では、このワークスペースを使用します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. Xcode で `helloworld.xcworkspace` ワークスペースを開きます。
1. **[View]\(表示\)**  >  **[Debug Area]\(デバッグ エリア\)**  >  **[Activate Console]\(コンソールのアクティブ化\)** の順に選択して、デバッグ出力が表示されるようにします。
1. **[Product]\(製品\)**  >  **[Destination]\(ターゲット\)** メニューの一覧から、お使いの開発マシンに接続された iOS デバイスまたは iOS シミュレーターをアプリのターゲットとして選択します。
1. メニューから **[Product]\(製品\)**  >  **[Run]\(実行\)** の順に選択して、コード例をビルドし、iOS シミュレーターで実行します。 **[Play]\(再生\)** ボタンを選択してもかまいません。
1. アプリの **[Recognize]\(認識\)** ボタンをクリックして何か話すと、発話した内容のテキストが画面の下部に表示されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
