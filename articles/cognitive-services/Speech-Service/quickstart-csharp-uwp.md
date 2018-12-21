---
title: 'クイック スタート: 音声を認識する、C# (UWP) - Speech Services'
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションの構築には、Speech SDK NuGet パッケージと Microsoft Visual Studio 2017 を使用します。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 55988ef65e223c76a485c3cbec13626abf68d3b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104631"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>クイック スタート: UWP アプリで Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

> [!NOTE]
> ユニバーサル Windows プラットフォームを使用すると、PC、Xbox、Surface Hub、その他のデバイスなど、Windows 10 をサポートする任意のデバイスで動作するアプリを開発できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech サービス サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、[Sppech サービスを無料で試す](get-started.md)ための記事を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 を起動します。

1. **ユニバーサル Windows プラットフォーム開発**ワークロードが使用できることを確認してください。 Visual Studio のメニュー バーから **[ツール]** > **[Get Tools and Features]\(ツールと機能の入手\)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、ダイアログ ボックスを閉じます。

    ![[ワークロード] タブを強調表示した Visual Studio インストーラーのスクリーンショット](media/sdk/vs-enable-uwp-workload.png)

    それ以外の場合、**[.NET cross-platform development] (.NET クロス プラットフォーム開発)** の横にあるボックスを選択し、ダイアログ ボックスの右下隅にある **[変更]** を選択します。 新しい機能のインストールにはしばらく時間がかかります。

1. 空の Visual C# ユニバーサル Windows アプリを作成します。 最初に、メニューから **[ファイル]** > **[新規]** > **[プロジェクト]** を選択します。 **[新しいプロジェクト]** ダイアログ ボックスで、左側のウィンドウから **[インストール済み]** > **[Visual C#]** > **[Windows ユニバーサル]** を展開します。 次に、**[空白のアプリ (ユニバーサル Windows)]** を選択します。 プロジェクト名として「*helloworld*」と入力します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speech SDK では、Windows 10 Fall Creators Update 以降向けにアプリケーションをビルドすることが必要です。 ポップアップ表示される **[新しいユニバーサル Windows プラットフォーム プロジェクト]** ウィンドウで、**[最小バージョン]** に **[Windows 10 Fall Creators Update (10.0; Build 16299)]** を選択します。 **[ターゲット バージョン]** ボックスで、このバージョンまたはこれ以降のバージョンを選択して、**[OK]** をクリックします。

    ![[新しいユニバーサル Windows プラットフォーム プロジェクト] ウィンドウのスクリーンショット](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 64 ビットの Windows を実行している場合は、Visual Studio ツールバーのドロップダウン メニューを使用して、ビルド プラットフォームを `x64` に切り替えることができます。 (64 ビットの Windows は 32 ビットのアプリケーションを実行できるため、`x86` に設定しておいてもかまいません。)

   ![x64 が強調表示されている Visual Studio のツールバーのスクリーン ショット](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Speech SDK は Intel 互換プロセッサのみをサポートします。 ARM は現在サポートされていません。

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![[ソリューションの NuGet パッケージの管理] オプションが強調表示されたソリューション エクスプローラーのスクリーン ショット](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![[ソリューションの NuGet パッケージの管理] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet パッケージのインストール")

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

    ![[ライセンスの同意] ダイアログ ボックスのスクリーンショット](media/sdk/qs-csharp-uwp-06-nuget-license.png "ライセンスへの同意")

1. パッケージ マネージャー コンソールに次の出力行が表示されます。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.1.0' to helloworld
   ```

1. アプリケーションでは音声入力にマイクを使用するため、**[マイク]** 機能をプロジェクトに追加します。 ソリューション エクスプローラーで **Package.appxmanifest** をダブルクリックして、アプリケーション マニフェストを編集します。 次に、**[機能]** タブに切り替え、**[マイク]** 機能の ボックスをオンにして、変更を保存します。

   ![機能とマイクを強調表示した Visual Studio のアプリケーション マニフェストのスクリーン ショット](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>サンプル コードを追加する

1. アプリケーションのユーザー インターフェイスを定義するには、XAML を使用します。 ソリューション エクスプローラーで `MainPage.xaml` を開きます。 デザイナーの XAML ビューで、次の XAML スニペットを Grid タグに挿入します (`<Grid>` と `</Grid>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. コードビハインド ソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` の下にグループ化されているものを見つけます)。 その中のすべてのコードを次の内容に置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. このファイル内の `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md)に置き換えます (たとえば、無料試用版サブスクリプションでは `westus`)。

1. プロジェクトのすべての変更を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-08-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-09-start-debugging.png "アプリのデバッグの開始")

1. ウィンドウがポップアップ表示されます。 **[Enable Microphone]** を選択して、ポップアップ表示されるアクセス許可要求を確認します。

    ![アクセス許可要求のスクリーンショット](media/sdk/qs-csharp-uwp-10-access-prompt.png "アプリのデバッグの開始")

1. **[Speech recognition with microphone input]** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech サービスに送信されてテキストに変換され、ウィンドウに表示されます。

    ![音声認識ユーザー インターフェイスのスクリーンショット](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/csharp-uwp` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK for C# を使用して音声から意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
