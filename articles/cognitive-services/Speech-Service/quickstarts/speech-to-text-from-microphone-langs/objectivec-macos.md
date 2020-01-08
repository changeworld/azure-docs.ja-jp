---
title: クイック スタート:マイクから音声を認識する、Objective-C - Speech サービス
titleSuffix: Azure Cognitive Services
description: macOS で Objective-C と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380594"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>クイック スタート:macOS で Objective-C と Speech SDK を使用して音声を認識する

[音声合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)のクイックスタートも利用できます。

この記事では、マイクから録音された音声をテキストに文字起こしするために、Objective-C と Azure Cognitive Services Speech SDK を使用して macOS アプリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

以降の手順を開始する前に次の要件を満たしておく必要があります。

* Speech サービス用の[サブスクリプション キー](~/articles/cognitive-services/Speech-Service/get-started.md)。
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 以降と macOS 10.13 以降がインストールされた macOS マシン

## <a name="get-the-speech-sdk-for-macos"></a>macOS 用の Speech SDK を取得する

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Mac 用の Cognitive Services Speech SDK は、フレームワーク バンドルとして配布されています。 Xcode プロジェクトで [CocoaPod](https://cocoapods.org/) として使用することも、 https://aka.ms/csspeech/macosbinary からダウンロードして手動でリンクすることも可能です。 この記事では、CocoaPod を使用します。

## <a name="create-an-xcode-project"></a>Xcode プロジェクトを作成する

Xcode を起動し、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Project]\(プロジェクト\)** の順に選択して新しいプロジェクトを開始します。 テンプレートの選択ダイアログ ボックスで、**Cocoa App** テンプレートを選択します。

以降のダイアログ ボックスで、次のように選択します。

1. **[Project Options]\(プロジェクトのオプション\)** ダイアログ ボックスで、次の手順を実行します。
    1. クイックスタート アプリの名前を入力します (例: *helloworld*)。
    1. 既に Apple Developer アカウントをお持ちの場合は、適切な組織名と組織 ID を入力します。 テスト目的のため、*testorg* などの名前を使用します。 アプリに署名するためには、適切なプロビジョニング プロファイルが必要です。 詳細については、[Apple の開発者向けサイト](https://developer.apple.com/)を参照してください。
    1. プロジェクトの言語として **Objective-C** が選択されていることを確認します。
    1. ストーリーボードを使用するためのチェック ボックスとドキュメントベースのアプリケーションを作成するためのチェック ボックスをオフにします。 サンプル アプリ向けのシンプルな UI がプログラムにより作成されます。
    1. テストとコア データに関するすべてのチェック ボックスをオフにします。

    ![プロジェクトの設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. プロジェクト ディレクトリを選択します。
    1. プロジェクトの配置先のディレクトリを選択します。 この手順により、ホーム ディレクトリ内に helloworld ディレクトリが作成され、Xcode プロジェクトのすべてのファイルが格納されます。
    1. このサンプル プロジェクトの Git リポジトリの作成を無効にします。
1. ネットワークとマイクへのアクセスのエンタイトルメントを設定します。 左側の概要で先頭行にあるアプリ名を選択してアプリの構成にアクセスします。 続けて、 **[機能]** タブを選択します。
    1. そのアプリの **[App sandbox]\(アプリのサンドボックス\)** 設定を有効にします。
    1. **[Outgoing Connections]\(発信接続\)** と **[Microphone]\(マイク\)** へのアクセスに関するチェック ボックスをオンにします。

    ![サンドボックスの設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. アプリによるマイクの使用は、`Info.plist` ファイルでも宣言する必要があります。 対象のファイルを概要でクリックし、 **[Privacy - Microphone Usage Description]\(プライバシー - マイクの用途に関する説明\)** キーを追加して、その値として "*音声認識にマイクが必要*" などを設定します。

    ![Info.plist の設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Xcode プロジェクトを閉じます。 後で CocoaPods の設定後に別のインスタンスを使用します。

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK を CocoaPod としてインストールする

1. CocoaPod 依存関係マネージャーをその[インストールの手順](https://guides.cocoapods.org/using/getting-started.html)に従ってインストールします。
1. サンプル アプリのディレクトリ (helloworld) に移動します。 そのディレクトリに、次の内容を含んだ *Podfile* という名前のテキスト ファイルを配置します。

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. ターミナルで helloworld ディレクトリに移動して、`pod install` コマンドを実行します。 このコマンドにより、サンプル アプリと依存関係としての Speech SDK の両方を含んだ、`helloworld.xcworkspace` という Xcode ワークスペースが生成されます。 以降の手順では、このワークスペースを使用します。

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. Xcode で `helloworld.xcworkspace` ワークスペースを開きます。
1. 自動生成された `AppDelegate.m` ファイルの内容を次のコードに置き換えます。

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。
1. 文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](~/articles/cognitive-services/Speech-Service/regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. **[View]\(表示\)**  >  **[Debug Area]\(デバッグ エリア\)**  >  **[Activate Console]\(コンソールのアクティブ化\)** の順に選択して、デバッグ出力が表示されるようにします。
1. メニューから **[Product]\(製品\)**  >  **[Run]\(実行\)** の順に選択してコード例をビルドし、実行します。 **[Play]\(再生\)** を選択してもかまいません。
1. ボタンを選択して何か話すと、発話した内容のテキストが画面の下部に表示されます。 アプリを初めて実行するときに、お使いのコンピューターのマイクの使用許可をアプリに与えるよう求められます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub で Objective-C のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
