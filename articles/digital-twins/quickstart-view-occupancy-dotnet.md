---
title: Azure Digital Twins を使用して空き部屋を探す (C#) | Microsoft Docs
description: このクイック スタートでは、2 つの .NET Core サンプル アプリケーションを実行して、Azure Digital Twins の空間にシミュレートされたモーションと二酸化炭素のテレメトリを送信します。 目標は、クラウドで計算処理した後に、管理 API から空気の良い空き部屋を探すことです。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/7/2018
ms.author: alinast
ms.openlocfilehash: 6e83ca543937948ad8028969cceca0f8787972c9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281220"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>クイック スタート: Azure Digital Twins を使用して空き部屋を探す

Azure Digital Twins サービスを使用すると、物理環境のデジタル イメージを再作成できます。 その後、環境内のイベントによって通知を受け取り、それらのイベントへの応答をカスタマイズすることができます。 

このクイック スタートでは、[1 組の .NET サンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp)を使用して架空のオフィス ビルをデジタル化し、その建物内の空き部屋を探す方法を示します。 Digital Twins を使用すると、複数のセンサーを環境に関連付けることができます。 部屋の空き状況に加えて、シミュレートされた二酸化炭素センサーを利用して、空き部屋の空気の質が最適かどうかを調べることもできます。 サンプル アプリケーションの 1 つでは、このシナリオの視覚化に役立つランダムなセンサー データを生成します。

クイック スタートのセットアップについては、次のビデオで要約しています。

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>前提条件

1. Azure アカウントをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

1. このクイック スタートで実行する 2 つのコンソール アプリケーションは、C# を使って書かれています。 開発用マシンに [.NET Core SDK バージョン 2.1.403 以上](https://www.microsoft.com/net/download)をインストールする必要があります。 .NET Core SDK がインストールされている場合は、コマンド プロンプトで `dotnet --version` を実行して、開発用マシンに現在インストールされている C# のバージョンを確認できます。

1. [サンプル C# プロジェクト](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)をダウンロードし、digital-twins-samples-csharp-master.zip アーカイブを抽出します。 


## <a name="create-a-digital-twins-instance"></a>Digital Twins インスタンスの作成

このセクションの手順を使用して、[ポータル](https://portal.azure.com)で Digital Twins の新しいインスタンスを作成します。

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>アプリのアクセス許可の設定

このセクションでは、サンプル アプリケーションを Azure Active Directory (AAD) に登録して、サンプル アプリケーションが Digital Twins インスタンスにアクセスできるようにします。 既に AAD アプリ登録がある場合は、このセクションで説明しているとおりにその登録が構成されていることを確認したうえで、その登録をサンプルに再利用できます。 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>アプリケーションのビルド

次の手順を使用して空間利用率アプリケーションをビルドします。

1. コマンド プロンプトを開き、digital-twins-samples-csharp-master.zip ファイルを抽出したフォルダーに移動します。
1. `cd occupancy-quickstart/src` を実行します。
1. `dotnet restore` を実行します。
1. *appSettings.json* を編集して、次の変数を更新します。
    - *ClientId*: 前のセクションでメモした、AAD アプリ登録の*アプリケーション ID* を入力します。
    - *Tenant*: AAD テナントの*ディレクトリ ID* を入力します。これも、同様に前のセクションでメモしたものです。
    - *BaseUrl*: Digital Twins インスタンスの*管理 API* の URL。`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/` という形式になります。 この URL 内のプレースホルダーを前のセクションのインスタンスの値に置き換えます。

## <a name="provision-graph"></a>グラフのプロビジョニング

この手順では、複数の空間、1 つのデバイス、2 つのセンサー、1 つのカスタム関数、1 つのロールの割り当てがある Digital Twins 空間グラフをプロビジョニングします。 空間グラフは、[*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ファイルを使ってプロビジョニングします。

1. `dotnet run ProvisionSample` を実行します。
    >[!NOTE]
    >Device Login Azure CLI ツールを使用して、ユーザーを Azure AD に対して認証します。 [Microsoft のログイン](https://microsoft.com/devicelogin) ページを使用して認証するには、ユーザーが特定のコードを入力する必要があります。 コードを入力した後、手順に従って認証します。 ユーザーは、ツールを実行するたびに認証を要求されます。
    
    >[!TIP]
    > この手順の実行中に次のエラーが発生した場合は、変数が正しくコピーされていることを確認してください。 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. プロビジョニング手順には数分かかることがあります。 また、Digital Twins インスタンス内に IoT Hub がプロビジョニングされ、IoTHub が Status=`Running` になるまでループ処理されます。

    ![サンプルのプロビジョニング][4]

1. 実行の最後に、デバイス シミュレーター サンプルで使用するデバイスの `ConnectionString` をコピーします。 下の図に示す文字列のみをコピーします。

    ![サンプルのプロビジョニング][1]

## <a name="send-sensor-data"></a>センサー データの送信

次の手順を使用して、センサー シミュレーター アプリケーションをビルドして実行できます。

1. 新しいコマンド プロンプトを開き、ダウンロードしたプロジェクト (digital-twins-samples-csharp-master フォルダー内) に移動します。
1. `cd device-connectivity` を実行します。
1. `dotnet restore` を実行します。
1. *appsettings.json* を編集し、上記の `ConnectionString` を使って *DeviceConnectionString* を更新します。
1. `dotnet run` を実行してセンサー データの送信を開始します。下の画像のように Digital Twins サービスにデータが送信されていることを確認できます。

     ![デバイスの接続性][2]

1. このシミュレーターを実行したままにし、次の手順の操作によって結果を並べて表示することができます。 このウィンドウには、Digital Twins に送信されたシミュレートされたセンサー データが表示されます。次の手順では、リアルタイムで照会して空気の良い空き部屋を検索します。

    >[!TIP]
    > この手順の実行中に次のエラーが発生した場合は、`DeviceConnectionString` が正しくコピーされていることを確認してください。  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>空気の良い空き空間の検索

このセンサー サンプルでは、2 つのセンサー (モーションと二酸化炭素) のランダムなデータ値をシミュレートしています。 空気の良い空き空間は、このサンプルでは、室内にだれも存在せず、二酸化炭素濃度が 1,000 ppm 未満であると定義します。 この条件が満たされない場合、その空間が利用できないか、空気の質が悪いことになります。

1. 上記のプロビジョニング手順の実行に使用したコマンド プロンプトを開きます。
1. `dotnet run GetAvailableAndFreshSpaces` を実行します。
1. 下記のように、このコマンド プロンプトとセンサー データ コマンド プロンプトを並べて確認します。 
1. 一方のコマンド プロンプトは、シミュレートされたモーションと二酸化炭素のデータを 5 秒ごとに Digital Twins に送信します。 もう一方のコマンドはグラフをリアルタイムで読み取り、シミュレートされたランダムなデータに基づいて空気の良い空き部屋を探します。 最後に送信されたセンサー データに基づいて、ほぼリアルタイムで次のどちらかの状況が表示されます。
    - 空気の良い空き部屋。
    - 空いていないか、空気の質が悪い部屋。

     ![空気の良い空き空間の取得][3]

このクイック スタートで何が起こったのかと、どの API が呼び出されたのかを理解するには、digital-twins-samples-csharp にあるコード ワークスペース プロジェクトを [Visual Studio Code](https://code.visualstudio.com/Download) で開いてください (以下のコマンドを参照)。 このチュートリアルでは、コードの詳細を確認し、構成データを変更する方法と呼び出される API について説明します。 管理 API の詳細については、Digital Twins Swagger のページ (`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger`) に移動するか、参考のために [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) を参照してください。 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、施設管理者が入居者の生産性を高め、建物をより効率的に運用するためのアプリケーションを構築する方法について詳しく説明します。

チュートリアルに進む場合は、このクイック スタートで作成したリソースを削除しないでください。 使用する予定がない場合は、次の手順を使用して、このクイック スタートで作成したすべてのリソースを削除してください。

1. サンプル リポジトリのダウンロード時に作成されたフォルダーを削除します。
1. [Azure portal](http://portal.azure.com) の左側のメニューにある **[すべてのリソース]** をクリックし、Digital Twins リソースを選択します。 **[すべてのリソース]** ウィンドウの上部にある **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、作業環境の良い部屋を探す簡単なシナリオの概要を説明しました。 このシナリオの詳細な分析については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Digital Twins をデプロイし、空間グラフを構成する](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
