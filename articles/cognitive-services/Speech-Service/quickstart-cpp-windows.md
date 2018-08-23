---
title: 'クイック スタート: Cognitive Services Speech SDK を使用して Windows デスクトップ上で C++ で音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Speech SDK を使用して Windows デスクトップ上で C++ で音声を認識する方法について説明します。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 59c70d33d537742a5918dc1ba55ab8ac1f84e12c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41936765"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>クイック スタート: Speech SDK を使用して Windows デスクトップ上で C++ で音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Speech SDK を利用する C++ ベースの Windows デスクトップ用コンソール アプリケーションを作成する方法を説明します。
このアプリケーションは、[Microsoft Cognitive Services Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 に基づいています。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* 機能するマイクを備えた Windows PC。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/) Community Edition 以降。
* Visual Studio の **[C++ によるデスクトップ開発]** ワークロードおよび Visual Studio の **[NuGet パッケージ マネージャー]** コンポーネント。
  どちらも、**[ツール]** \> **[ツールと機能を取得]** のそれぞれ、**[ワークロード]** タブと **[個別のコンポーネント]** タブで有効にすることができます。

  ![[C++ によるデスクトップ開発] ワークロードを有効にする](media/sdk/vs-enable-cpp-workload.png)

  ![Visual Studio で NuGet パッケージ マネージャーを有効にする ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

Visual Studio 2017 で、新しい Visual C++ Windows デスクトップ Windows コンソール アプリケーションを作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** \> **[Visual C++]** \> **[Windows デスクトップ]** を展開し、**[Windows コンソール アプリケーション]** を選択します。 プロジェクト名として「*helloworld*」と入力します。

![Visual C++ Windows デスクトップ Windows コンソール アプリケーションを作成する](media/sdk/qs-cpp-windows-01-new-console-app.png)

64 ビットの Windows インストールで実行している場合は、必要に応じて `x64` にビルド プラットフォームを切り替えます。

![ビルド プラットフォームを x64 に切り替える](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Speech SDK NuGet パッケージをインストールして参照する

ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします。

![右クリックして [ソリューションの NuGet パッケージの管理] を選択する](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

右上隅の **[パッケージ ソース]** フィールドで、[Nuget.org] を選択します。
**[参照]** タブで [Microsoft.CognitiveServices.Speech] パッケージを検索して選択し、右側にある **[プロジェクト]** ボックスと **[helloworld]** ボックスをオンにし、**[インストール]** を選択してそれを helloworld プロジェクトにインストールします。

> [!NOTE]
> Cognitive Services Speech SDK の現在のバージョンは `0.6.0` です。

![Microsoft.CognitiveServices.Speech NuGet パッケージをインストールする](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

ポップアップ表示されるライセンス画面で、ライセンスに同意します。

![ライセンスに同意する](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. 既定のスタート コードを次のコードに置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

   ![成功したビルド](media/sdk/qs-cpp-windows-06-build.png)

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

   ![アプリを起動してデバッグする](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. コンソール ウィンドウがポップアップ表示 され、(英語で) 何か言うように求められます。
   認識の結果が画面に表示されます。

   ![認識が成功した後のコンソール出力](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/cpp-windows` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

* [サンプルを入手する](speech-sdk.md#get-the-samples)
