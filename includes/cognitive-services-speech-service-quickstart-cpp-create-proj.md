---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020773"
---
1. Visual Studio 2017 を起動します。

1. **[Desktop development with C++]\(C++ によるデスクトップ開発)** ワークロードを使用できることを確認します。 Visual Studio のメニュー バーから **[ツール]** > **[Get Tools and Features]\(ツールと機能の入手)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、次のステップまでスキップします。

    ![Visual Studio の [ワークロード] タブのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    それ以外の場合、**[Desktop development with C++]\(C++ によるデスクトップ開発)** の横にあるチェック ボックスをオンにします。

1. **[NuGet パッケージ マネージャー]** コンポーネントを使用できることを確認します。 まだ有効になっていない場合は、Visual Studio インストーラーのダイアログ ボックスの **[個別のコンポーネント]** タブに切り替えて、**[NuGet パッケージ マネージャー]** をクリックします。

      ![Visual Studio の [個別のコンポーネント] タブのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. C++ ワークロードまたは NuGet のいずれかを有効にする必要がある場合は (ダイアログ ボックスの右下隅で) **[変更]** を選択します。 新しい機能のインストールにはしばらく時間がかかります。 両方の機能が既に有効になっている場合は、そうせずにダイアログ ボックスを閉じます。

1. 新しい Visual C++ Windows デスクトップ Windows コンソール アプリケーションを作成します。 最初に、メニューから **[ファイル]** > **[新規]** > **[プロジェクト]** を選択します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** > **[Visual C++]** > **[Windows デスクトップ]** を展開します。 次に、**[Windows Console Application]\(Windows コンソール アプリケーション)** を選択します。 プロジェクト名として「*helloworld*」と入力します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 64 ビットの Windows を実行している場合は、Visual Studio ツールバーのドロップダウン メニューを使用して、ビルド プラットフォームを `x64` に切り替えてもかまいません (64 ビット バージョンの Windows では 32 ビット アプリケーションを実行できるため、これは要件ではありません)。

    ![x64 オプションが強調表示されている Visual Studio ツールバーのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![[ソリューションの NuGet パッケージの管理] オプションが強調表示されたソリューション エクスプローラーのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![ソリューションのパッケージの管理ダイアログ ボックスのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Cognitive Services Speech SDK の現在のバージョンは `1.5.0` です。

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

    ![[ライセンスへの同意] ダイアログ ボックスのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。
