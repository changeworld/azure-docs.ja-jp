---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362551"
---
1. Visual Studio 2019 を開きます。

1. [スタート] ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。 

1. **[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択します。

1. **[プロジェクト名]** に `helloworld` と入力して、 **[作成]** を選択します。

1. Visual Studio のメニュー バーから **[ツール]** 、 **[ツールと機能の取得]** を選択し、 **[.NET デスクトップ開発]** ワークロードが利用できることを確認します。 このワークロードがまだインストールされていない場合は、チェック ボックスをオンにし、 **[変更]** を選択してインストールを開始してください。 ダウンロードとインストールに数分かかる場合があります。

   **[.NET デスクトップ開発]** の横のチェック ボックスがオンになっている場合は、ここでダイアログ ボックスを閉じてかまいません。

   ![.NET デスクトップ環境を有効にする](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

次の手順は、コード内で参照できるように、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールすることです。

1. ソリューション エクスプローラーで `helloworld` を右クリックし、 **[NuGet パッケージの管理]** を選択して NuGet パッケージ マネージャーを表示します。

   ![NuGet パッケージ マネージャー](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 右上隅で **[パッケージ ソース]** ドロップダウン ボックスを探し、 **[nuget.org]** が選択されていることを確認します。

1. 左上隅で **[参照]** を選択します。

1. 検索ボックスに「`Microsoft.CognitiveServices.Speech` package」と入力し、Enter キーを押します。

1. `Microsoft.CognitiveServices.Speech` を選択し、 **[インストール]** を選択し、安定している最新版をインストールします。

   ![Microsoft.CognitiveServices.Speech NuGet パッケージをインストールする](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. すべての契約とライセンスに同意して、インストールを開始します。

   パッケージがインストールされると、 **[パッケージマネージャー コンソール]** ウィンドウに確認が表示されます。

次に、コンソール アプリケーションをビルドして実行する目的で、お使いのコンピューターのアーキテクチャに合ったプラットフォーム構成を作成します。

1. メニュー バーから、 **[ビルド]** 、 **[構成マネージャー]** の順に選択します。 **[構成マネージャー]** ダイアログ ボックスが表示されます。

   ![[構成マネージャー] ダイアログボックス](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **[アクティブ ソリューション プラットフォーム]** ドロップダウン ボックスで **[新規]** を選択します。 **[新しいソリューション プラットフォーム]** ダイアログ ボックスが表示されます。

1. **[新しいプラットフォームを入力または選択してください]** ドロップダウン ボックスで:
   - 64 ビット Windows を実行している場合、**x64** を選択します。
   - 32 ビット Windows を実行している場合、**x86** を選択します。

1. **[OK]** 、 **[閉じる]** の順に選択します。
