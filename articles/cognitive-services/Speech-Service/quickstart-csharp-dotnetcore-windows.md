---
title: 'クイック スタート: Windows の .NET Core 下で Cognitive Services Speech SDK を使用して C# で音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: Windows の .NET Core 下で Cognitive Services Speech SDK を使用して C# で音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 74fb62ffa707188779bcf827d427c61a0b9a4347
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576340"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>クイック スタート: Windows の .NET Core 下で Cognitive Services Speech SDK を使用して C# で音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換する、Windows の .NET Core 向け C# コンソール アプリケーションを作成する方法について説明します。
このアプリケーションの構築には、[Microsoft Cognitive Services Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 を使用します。

> [!NOTE]
> .NET Core は、[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 仕様を実装した、オープン ソース、クロスプラットフォームの .NET プラットフォームです。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* 機能するマイクを備えた Windows PC。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/) Community Edition 以上。
* Visual Studio の **.NET Core クロスプラットフォーム開発**ワークロード。 **[ツール]** \> **[ツールと機能の取得]** で有効にできます。

  ![.NET Core クロスプラットフォーム開発を有効にする](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 で、新しい Visual C# .NET Core コンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** \> **[Visual C#]** \> **[.NET Core]** を展開し、**[コンソール アプリ (.NET Core)]** を選択します。 プロジェクト名として、「*helloworld*」を入力します。

    ![Visual C# コンソール アプリの作成 (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# コンソール アプリの作成 (.NET Core)")

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![右クリック後の [ソリューションの NuGet パッケージの管理]](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "ソリューションの NuGet パッケージの管理")

1. 右上隅の **[パッケージ ソース]** フィールドで、**[Nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索してインストールし、**helloworld** プロジェクトにインストールします。

    ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Nuget のインストール パッケージ")

1. 表示されるライセンスに同意します。

    ![ライセンスに同意](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "ライセンスに同意")

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開き、その中のすべてのコードを次の内容に置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![成功したビルド](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![アプリのデバッグの開始](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示 され、(英語で) 何か言うように求められます。 認識されたテキストが同じウィンドウに表示されます。

    ![正常に認識された後のコンソールの出力](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "正常に認識された後のコンソールの出力")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/csharp-dotnetcore-windows` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
