---
title: 'クイック スタート: Windows 用 Cognitive Services Speech C# SDKを使用して音声を認識する | Microsoft Docs'
description: Speech サービス用の C# SDK を使用して音声を認識する方法について説明します。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016836"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>クイック スタート: Windows 用 Cognitive Services Speech C# SDK を使用して音声を認識する

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換する C# コンソール アプリケーションを Windows で作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* Visual Studio 2017 Community Edition 以上。
* Visual Studio の **.NET デスクトップ開発**ワークロード。 **[ツール]** \> **[ツールと機能の取得]** で有効にできます。 

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 で、新しい Visual C# コンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** を展開し、**[コンソール アプリ (.NET Framework)]** を選択します。 プロジェクト名として、「*CsharpHelloSpeech*」を入力します。

    ![Visual C# コンソール アプリの作成 (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Visual C# コンソール アプリの作成")

2. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![右クリック後の [ソリューションの NuGet パッケージの管理]](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "ソリューションの NuGet パッケージの管理")

3. 右上隅の **[パッケージ ソース]** フィールドで、**[Nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索し、**CsharpHelloSpeech** プロジェクトにインストールします。

    ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Nuget のインストール パッケージ")

4. ポップアップ表示されるライセンス画面で、ライセンスに同意します。

    ![ライセンスに同意](media/sdk/speechsdk-09-vs-cs-nuget-license.png "ライセンスに同意")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>お使いの PC アーキテクチャと一致するプラットフォーム構成を作成する

このセクションでは、お使いのプロセッサのアーキテクチャと一致する構成に新しいプラットフォームを追加します。

1. 構成マネージャーを起動します。 **[ビルド]** > **[構成マネージャー]** を選択します。

    ![構成マネージャーの起動](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "構成マネージャーの起動")

2. **[構成マネージャー]** ダイアログ ボックスで、新しいプラットフォームを追加します。 **[アクティブ ソリューション プラットフォーム]** ドロップダウン リストから **[新規]** を選択します。

    ![[構成マネージャー] ウィンドウでの新しいプラットフォームの追加](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "[構成マネージャー] ウィンドウでの新しいプラットフォームの追加")

3. 64 ビット Windows を実行している場合は、`x64` という名前の新しいプラットフォーム構成を作成します。 32 ビット Windows を実行している場合は、`x86` という名前の新しいプラットフォーム構成を作成します。 この記事では、`x64` プラットフォーム構成を作成します。 

    ![64 ビット Windows での "x64" という名前の新しいプラットフォームの追加](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "x64 プラットフォームの追加")

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. Visual Studio プロジェクトの `Program.cs` で、`Program` クラスの本文を以下に置き換えます。 サブスクリプション キーとリージョンを、このサービスのために取得した値に置き換えたことを確認します。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. コードを貼り付けた後、`Main()` メソッドは、次のスクリーン ショットのようになる必要があります。

    ![コード貼り付け後の Main メソッド](media/sdk/speechsdk-17-vs-cs-paste-code.png "最終的な Main メソッド")

3. Visual Studio の IntelliSense によって、解決できなかった Speech SDK のクラスへの参照が強調表示されます。 このエラーを修正するには、次の `using` ステートメントを (手動で、または Visual Studio の[クイック アクション](https://docs.microsoft.com/visualstudio/ide/quick-actions) を使用して) コードの先頭に追加します。

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![クイック アクションを使用した欠落している using ステートメントの追加](media/sdk/speechsdk-18-vs-cs-add-using.png "IntelliSense の問題の解決")

4. IntelliSense の強調表示が解決されたことを確認し、変更をプロジェクトに保存します。

## <a name="build-and-run-the-sample"></a>サンプルをビルドして実行する

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![成功したビルド](media/sdk/speechsdk-20-vs-cs-build.png "成功したビルド")

2. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。 

    ![アプリのデバッグの開始](media/sdk/speechsdk-21-vs-cs-f5.png "アプリのデバッグの開始")

3. コンソール ウィンドウがポップアップ表示 され、(英語で) 何か言うように求められます。
認識の結果が画面に表示されます。

    ![正常に認識された後のコンソールの出力](media/sdk/speechsdk-22-cs-vs-console-output.png "正常に認識された後のコンソールの出力")

## <a name="download-code"></a>コードをダウンロードする

最新のサンプル セットについては、[Cognitive Services の Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="next-steps"></a>次の手順

- [音声を変換する](how-to-translate-speech.md)
- [音声モデルをカスタマイズする](how-to-customize-speech-models.md)
