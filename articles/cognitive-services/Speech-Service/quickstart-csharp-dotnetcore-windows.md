---
title: 'クイック スタート: 音声を認識する (.NET Core (Windows)) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Windows の .NET Core で C# と Speech Service SDK を使用して音声を認識する方法について説明します。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: e82e39eb3fc6c7ebaf4798ad10038bfd2fa9a41b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085486"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>クイック スタート: Windows の .NET Core で Speech SDK を使用して C# で音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用してWindows の .NET Core 用の C# コンソール アプリケーションを作成します。 PC のマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

> [!NOTE]
> .NET Core は、[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 仕様を実装した、オープン ソース、クロスプラットフォームの .NET プラットフォームです。

このクイック スタートを完了するには、Speech サービス サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、[Sppech サービスを無料で試す](get-started.md)ための記事を参照してください。


## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 を起動します。

1. **.NET クロスプラットフォーム開発**ワークロードが使用できることを確認してください。 Visual Studio のメニュー バーから **[ツール]** > **[Get Tools and Features]\(ツールと機能の入手\)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、ダイアログ ボックスを閉じます。

    ![[ワークロード] タブを強調表示した Visual Studio インストーラーのスクリーンショット](media/sdk/vs-enable-net-core-workload.png)

    それ以外の場合、**[.NET Core cross-platform development] (.NET Core クロス プラットフォーム開発)** の横にあるボックスを選択し、ダイアログ ボックスの右下隅にある **[変更]** を選択します。 新しい機能のインストールにはしばらく時間がかかります。

1. 新しい Visual C# .NET Core コンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、左側のウィンドウから **[インストール済み]** > **[Visual C#]** > **[.NET Core]** を展開します。 **[コンソール アプリ (.NET Core)]** を選択します。 プロジェクト名として「*helloworld*」と入力します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# コンソール アプリ (.NET Core) の作成")

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![[ソリューションの NuGet パッケージの管理] が強調表示されたソリューション エクスプローラーのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "[ソリューションの NuGet パッケージの管理]")

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![[ソリューションの NuGet パッケージの管理] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet パッケージのインストール")

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

    ![[ライセンスの同意] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "ライセンスへの同意")

パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。


## <a name="add-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開き、その中のすべてのコードを次の内容に置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示され、何か言うように求められます。 英語の語句または文を読み上げます。 音声が Speech サービスに送信されてテキストに変換され、同じウィンドウに表示されます。

    ![正常に認識された後のコンソールの出力のスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "正常に認識された後のコンソールの出力")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/csharp-dotnetcore-windows` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK for C# を使用して音声から意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
