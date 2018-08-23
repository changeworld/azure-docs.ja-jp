---
title: 'クイック スタート: UWP アプリで C# と Cognitive Services Speech SDK を使用して音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: UWP アプリで Cognitive Services Speech SDK を使用して C# で音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 551439d28bcd7c64faf7ac6ac7388145456f7a53
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929924"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>クイック スタート: UWP アプリで Speech SDK を使用して音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換するユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。
このアプリケーションの構築には、[Microsoft Cognitive Services Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 を使用します。

> [!NOTE]
> ユニバーサル Windows プラットフォームを使用すると、PC、Xbox、Surface Hub、その他のデバイスなど、Windows 10 をサポートする任意のデバイスで動作するアプリを開発できます。 Speech SDK を使用するアプリは、まだ Windows アプリ認定キット (WACK) に合格していません。 アプリのサイドローディングは可能ですが、現時点では Windows ストアに提出することはできません。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* マイクが動作する Windows PC (Windows 10 Fall Creators Update 以降)。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/) Community Edition 以上。
* Visual Studio の**ユニバーサル Windows プラットフォーム開発**ワークロード。**[ツール]** \> **[Get Tools and Features]\(ツールと機能の入手\)** で有効化できます。

  ![ユニバーサル Windows プラットフォーム開発を有効化する](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 で、Visual C# Windows ユニバーサルの空のアプリを新しく作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** \> **[Visual C#]** \> **[Windows ユニバーサル]** を展開し、**[空のアプリ (ユニバーサル Windows)]** を選択します。 プロジェクト名として、「*helloworld*」を入力します。

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. ポップアップ表示される **[新しいユニバーサル Windows プラットフォーム プロジェクト]** ウィンドウで、**[最小バージョン]** は **[Windows 10 Fall Creators Update (10.0; Build 16299)]** を、**[ターゲットのバージョン]** はそれと同じかそれ以降のバージョンを 選択して **[OK]** をクリックします。

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 64 ビットの Windows インストールで実行している場合は、ビルド プラットフォームを `x64` に切り替えることができます。

   ![ビルド プラットフォームを x64 に切り替える](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 現時点で、Speech SDK は Intel 互換プロセッサをサポートしますが、ARM はサポートしません。

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![右クリックして [ソリューションの NuGet パッケージの管理] を選択する](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 右上隅の **[パッケージ ソース]** フィールドで、**[Nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索してインストールし、**helloworld** プロジェクトにインストールします。

    ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Nuget のインストール パッケージ")

1. 表示されるライセンスに同意します。

    ![ライセンスに同意](media/sdk/qs-csharp-uwp-06-nuget-license.png "ライセンスに同意")

1. パッケージ マネージャー コンソールに次の出力行が表示されます。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.6.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. ソリューション エクスプローラーで **Package.appxmanifest** をダブルクリックして、アプリケーション マニフェストを編集します。
   **[機能]** タブを選択し、**[マイク]** 機能のチェック ボックスをオンにして、変更を保存します。

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. ソリューション エクスプローラーで `MainPage.xaml` をダブルクリックして、アプリケーションのユーザー インターフェイスを編集します。 

    デザイナーの XAML ビューで、次の XAML スニペットを Grid タグに挿入します (`<Grid>` と `</Grid>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. ソリューション エクスプローラーで `MainPage.xaml.cs` をダブルクリックして、XAML コードビハインドを編集します (`MainPage.xaml` 項目の下にグループ化されています)。
   このファイル内のすべてのコードを次の内容に置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md)に置き換えます (たとえば、無料試用版サブスクリプションでは `westus`)。

1. プロジェクトのすべての変更を保存します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![成功したビルド](media/sdk/qs-csharp-uwp-08-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![アプリのデバッグの開始](media/sdk/qs-csharp-uwp-09-start-debugging.png "アプリのデバッグの開始")

1. GUI ウィンドウがポップアップ表示されます。 まず **[Enable Microphone]** ボタンをクリックして、ポップアップ表示されるアクセス許可要求を確認します。

    ![アプリのデバッグの開始](media/sdk/qs-csharp-uwp-10-access-prompt.png "アプリのデバッグの開始")

1. **[Speech recognition with microphone input]** をクリックし、デバイスのマイクに向かって短いフレーズを話します。 認識されたテキストがウィンドウに表示されます。

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/csharp-uwp` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
