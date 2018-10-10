---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454454"
---
1. Visual Studio 2017 を起動します。
 
1. **.NET デスクトップ環境**ワークロードを使用できることを確認します。 Visual Studio のメニュー バーから **[ツール]**\>**[Get Tools and Features]\(ツールと機能の入手)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、ダイアログを閉じます。 

    それ以外の場合は、**[.NET desktop development]\(.NET デスクトップ開発)** の横のチェック ボックスをオンにし、ダイアログの右下隅にある **[Modify]\(変更)** ボタンをクリックします。 新しい機能のインストールにはしばらく時間がかかります。

    ![.NET デスクトップ環境を有効にする](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual C# の新しいコンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスの左側のウィンドウで、**[インストール済み]** \> **[Visual C#]** \> **[Windows デスクトップ]** を展開し、**[コンソール アプリ (.NET Framework)]** を選択します。 プロジェクト名として「*helloworld*」と入力します。

    ![Visual C# コンソール アプリの作成 (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Visual C# コンソール アプリの作成 (.NET Framework)")

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![右クリック後の [ソリューションの NuGet パッケージの管理]](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "ソリューションの NuGet パッケージの管理")

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

    ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Nuget パッケージのインストール")

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

    ![ライセンスに同意](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "ライセンスに同意")

    パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。

1. 構成マネージャーで、お使いの PC アーキテクチャと一致するプラットフォーム構成を作成します。 **[ビルド]** > **[構成マネージャー]** を選択します。

    ![構成マネージャーの起動](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "構成マネージャーの起動")

1. **[構成マネージャー]** ダイアログ ボックスで、新しいプラットフォームを追加します。 **[アクティブ ソリューション プラットフォーム]** ドロップダウン リストから **[新規]** を選択します。

    ![[構成マネージャー] ウィンドウでの新しいプラットフォームの追加](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "[構成マネージャー] ウィンドウでの新しいプラットフォームの追加")

1. 64 ビット Windows を実行している場合は、`x64` という名前の新しいプラットフォーム構成を作成します。 32 ビット Windows を実行している場合は、`x86` という名前の新しいプラットフォーム構成を作成します。

    ![64 ビット Windows での "x64" という名前の新しいプラットフォームの追加](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 プラットフォームの追加")


