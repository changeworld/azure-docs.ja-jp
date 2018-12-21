---
title: 'クイック スタート: 音声を認識する (C++ (Windows)) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Windows デスクトップで C++ と Speech Service SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 454b84f4ba5e492f61daf67c980b204c82a2090d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075189"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>クイック スタート: Windows 上で C++ と Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Windows 用の C++ コンソール アプリケーションを作成します。 Cognitive Services [Speech SDK](speech-sdk.md) を使用して、リアルタイムに PC のマイクからの音声をテキストに文字起こします。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech サービス サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、[Sppech サービスを無料で試す](get-started.md)ための記事を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. Visual Studio 2017 を起動します。

1. **[Desktop development with C++]\(C++ によるデスクトップ開発)** ワークロードを使用できることを確認します。 Visual Studio のメニュー バーから **[ツール]** > **[Get Tools and Features]\(ツールと機能の入手)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、次のステップまでスキップします。

    ![Visual Studio の [ワークロード] タブのスクリーンショット](media/sdk/vs-enable-cpp-workload.png)

    それ以外の場合、**[Desktop development with C++]\(C++ によるデスクトップ開発)** の横にあるチェック ボックスをオンにします。

1. **[NuGet パッケージ マネージャー]** コンポーネントを使用できることを確認します。 まだ有効になっていない場合は、Visual Studio インストーラーのダイアログ ボックスの **[個別のコンポーネント]** タブに切り替えて、**[NuGet パッケージ マネージャー]** をクリックします。

      ![Visual Studio の [個別のコンポーネント] タブのスクリーンショット](media/sdk/vs-enable-nuget-package-manager.png)

1. C++ ワークロードまたは NuGet のいずれかを有効にする必要がある場合は (ダイアログ ボックスの右下隅で) **[変更]** を選択します。 新しい機能のインストールにはしばらく時間がかかります。 両方の機能が既に有効になっている場合は、そうせずにダイアログ ボックスを閉じます。

1. 新しい Visual C++ Windows デスクトップ Windows コンソール アプリケーションを作成します。 最初に、メニューから **[ファイル]** > **[新規]** > **[プロジェクト]** を選択します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** > **[Visual C++]** > **[Windows デスクトップ]** を展開します。 次に、**[Windows Console Application]\(Windows コンソール アプリケーション)** を選択します。 プロジェクト名として「*helloworld*」と入力します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. all64 ビットの Windows を実行している場合は、Visual Studio ツールバーのドロップダウン メニューを使用して、ビルド プラットフォームを `x64` に切り替えてもかまいません (64 ビット バージョンの Windows では 32 ビット アプリケーションを実行できるため、これは要件ではありません)。

    ![x64 オプションが強調表示されている Visual Studio ツールバーのスクリーン ショット](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![[ソリューションの NuGet パッケージの管理] オプションが強調表示されたソリューション エクスプローラーのスクリーン ショット](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![ソリューションのパッケージの管理ダイアログ ボックスのスクリーン ショット](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Cognitive Services Speech SDK の現在のバージョンは `1.1.0` です。

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

    ![[ライセンスへの同意] ダイアログ ボックスのスクリーン ショット](media/sdk/qs-cpp-windows-05-nuget-license.png)

パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル *helloworld.cpp* を開きます。 最初の include ステートメント (`#include "stdafx.h"` または `#include "pch.h"`) の下にあるすべてのコードを、以下に置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

   ![[ソリューションのビルド] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-06-build.png)

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

   ![[デバッグの開始] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. コンソール ウィンドウが表示され、何か言うように求められます。 英語の語句または文を読み上げます。 音声が Speech サービスに送信されてテキストに変換され、同じウィンドウに表示されます。

   ![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/cpp-windows` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [C++ 用の Speech SDK を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
