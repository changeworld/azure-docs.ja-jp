---
title: 'クイック スタート: Windows の .NET Framework 下で Cognitive Services Speech SDK を使用して C# で音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: Windows の .NET Framework 下で Cognitive Services Speech SDK を使用して C# で音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: a625ee4d8d672ddfd42128a9dd871dcfcc013060
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41936766"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>クイック スタート: Windows の .NET Framework 下で Cognitive Services Speech SDK を使用して C# で音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換する、Windows の .NET Framework 向け C# コンソール アプリケーションを作成する方法について説明します。
このアプリケーションの構築には、[Microsoft Cognitive Services Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 を使用します。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* 機能するマイクを備えた Windows PC。
* Visual Studio 2017 Community Edition 以上。
* Visual Studio の **.NET デスクトップ開発**ワークロード。 **[ツール]** \> **[ツールと機能の取得]** で有効にできます。

  ![.NET デスクトップ環境を有効にする](media/sdk/vs-enable-net-desktop-workload.png)

  ![.NET Core クロスプラットフォーム開発を有効にする](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 で、新しい Visual C# コンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** \> **[Visual C#]** \> **[Windows デスクトップ]** を展開し、**[コンソール アプリ (.NET Framework)]** を選択します。 プロジェクト名として、「*helloworld*」を入力します。

    ![Visual C# コンソール アプリの作成 (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Visual C# コンソール アプリの作成 (.NET Framework)")

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![右クリック後の [ソリューションの NuGet パッケージの管理]](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "ソリューションの NuGet パッケージの管理")

1. 右上隅の **[パッケージ ソース]** フィールドで、**[Nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "Nuget のインストール パッケージ")

1. 表示されるライセンスに同意します。

    ![ライセンスに同意](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "ライセンスに同意")

1. パッケージ マネージャー コンソールに次の出力行が表示されます。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.6.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>お使いの PC アーキテクチャと一致するプラットフォーム構成を作成する

このセクションでは、お使いのプロセッサのアーキテクチャと一致する構成に新しいプラットフォームを追加します。

1. 構成マネージャーを起動します。 **[ビルド]** > **[構成マネージャー]** を選択します。

    ![構成マネージャーの起動](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "構成マネージャーの起動")

1. **[構成マネージャー]** ダイアログ ボックスで、新しいプラットフォームを追加します。 **[アクティブ ソリューション プラットフォーム]** ドロップダウン リストから **[新規]** を選択します。

    ![[構成マネージャー] ウィンドウでの新しいプラットフォームの追加](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "[構成マネージャー] ウィンドウでの新しいプラットフォームの追加")

1. 64 ビット Windows を実行している場合は、`x64` という名前の新しいプラットフォーム構成を作成します。 32 ビット Windows を実行している場合は、`x86` という名前の新しいプラットフォーム構成を作成します。 この記事では、`x64` プラットフォーム構成を作成します。

    ![64 ビット Windows での "x64" という名前の新しいプラットフォームの追加](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 プラットフォームの追加")

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開き、その中のすべてのコードを次の内容に置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![成功したビルド](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![アプリのデバッグの開始](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示 され、(英語で) 何か言うように求められます。 認識されたテキストが同じウィンドウに表示されます。

    ![正常に認識された後のコンソールの出力](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "正常に認識された後のコンソールの出力")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/csharp-dotnet-windows` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
