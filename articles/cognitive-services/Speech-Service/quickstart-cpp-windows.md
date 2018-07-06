---
title: C++ と Windows 向け Speech SDK のクイック スタート | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Windows と C++ の環境での Cognitive Services の Speech SDK の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049232"
---
# <a name="quickstart-for-c-and-windows"></a>C++ と Windows 向けのクイック スタート

Cognitive Services Speech SDK の現在のバージョンは `0.4.0` です。

Speech SDK を利用する C++ ベースの Windows デスクトップ用コンソール アプリケーションを作成する方法を説明します。
このアプリケーションは、[Microsoft Cognitive Services SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)と Microsoft Visual Studio 2017 が基になっています。

> [!NOTE]
> C# と Linux 向けのクイック スタートをお探しの場合は、[こちら](quickstart-cpp-linux.md)にお進みください。<br>
> C# と Windows 向けのクイック スタートをお探しの場合は、[こちら](quickstart-csharp-windows.md)にお進みください。

> [!NOTE]
> このクイック スタートには、マイクを使用できる PC が必要です。<br>
> 指定されたオーディオ入力ファイルからの音声を認識するサンプルについては、[サンプル](speech-to-text-sample.md#speech-recognition-from-a-file)をご覧ください。

> [!NOTE]
> Visual Studio のインストールに **C++ によるデスクトップ開発**ワークロードが含まれることを確認します。
> はっきりしない場合は、次の手順で確認して修正します。Visual Studio 2017 で、**[ツール]** \> **[ツールと機能を取得]** を選択し、**[はい]** を選択してユーザー アカウント制御のプロンプトを確認します。
> **[ワークロード]** タブで **[C++ によるデスクトップ開発]** チェック ボックスがオンになっていない場合は、オンにして **[変更]** をクリックし、変更を保存します。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>空のコンソール アプリケーション プロジェクトの作成

Visual Studio 2017 で、新しい Visual C++ Windows デスクトップ Windows コンソール アプリケーション を "CppHelloSpeech" という名前で作成します。

![Visual C++ Windows デスクトップ Windows コンソール アプリケーションを作成する](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

64 ビットの Windows インストールで実行している場合は、必要に応じて `x64` にビルド プラットフォームを切り替えます。

![ビルド プラットフォームを x64 に切り替える](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Speech SDK NuGet パッケージをインストールして参照する

> [!NOTE]
> お使いの Visual Studio 2017 のインストールで NuGet パッケージ マネージャーが有効になっていることを確認します。
> Visual Studio 2017 で、**[ツール]** \> **[ツールと機能を取得]** を選択し、**[はい]** を選択してユーザー アカウント制御のプロンプトを確認します。 **[個別のコンポーネント]** タブを選択し、**[コード ツール]** の下で.**[NuGet パッケージ マネージャー]** を探します。
> そのチェック ボックスがオンになっていない場合は、オンにして **[変更]** をクリックし、変更を保存します。
>
> ![Visual Studio で NuGet パッケージ マネージャーを有効にする ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします。

![右クリックして [ソリューションの NuGet パッケージの管理] を選択する](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

右上隅の **[パッケージ ソース]** フィールドで、[Nuget.org] を選択します。
**[参照]** タブで "Microsoft.CognitiveServices.Speech" パッケージを探して選択し、右側の **[プロジェクト]** ボックスと **[CppHelloSpeech]** ボックスをオンにし、**[インストール]** を選択して CppHelloSpeech プロジェクトにインストールします。

![Microsoft.CognitiveServices.Speech NuGet パッケージをインストールする](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

ポップアップ表示されるライセンス画面で、ライセンスに同意します。

![ライセンスに同意する](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

既定のスタート コードを次のコードに置き換えます。

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> サブスクリプション キーを入手したキーに置き換えます。 <br>
> リージョンを [Speech Service REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis) のリージョン ("westus" など) に置き換えます。

![サブスクリプション キーを追加する](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

これで、コードは、エラーなしでコンパイルされます。

![成功したビルド](media/sdk/speechsdk-16-vs-cpp-build.png)

起動ボタンまたは F5 キーボード ショートカットを使って、デバッガーの下でプログラムを起動します。

![アプリを起動してデバッグする](media/sdk/speechsdk-17-vs-cpp-f5.png)

コンソール ウィンドウがポップアップ表示 され、(英語で) 何か言うように求められます。
認識の結果が画面に表示されます。

![認識が成功した後のコンソール出力](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>サンプルのダウンロード

最新のサンプルのセットについては、[Cognitive Services の Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="next-steps"></a>次の手順

* 追加のサンプルについては、[サンプルのページ](samples.md)を参照してください。
