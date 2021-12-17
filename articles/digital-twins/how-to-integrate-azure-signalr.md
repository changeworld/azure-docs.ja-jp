---
title: Azure SignalR Service と統合する
titleSuffix: Azure Digital Twins
description: Azure SignalR を使用して Azure Digital Twins のテレメトリをクライアントにストリーム配信する方法について説明します
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8690f69bace02e73f144b563a5541073cb976f22
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770610"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Azure Digital Twins を Azure SignalR Service と統合する

この記事では、Azure Digital Twins と [Azure SignalR Service](../azure-signalr/signalr-overview.md) を統合する方法について説明します。

この記事で説明するソリューションでは、1 つの Web ページやモバイル アプリケーションなどのデジタル ツインのテレメトリ データを、接続されたクライアントにプッシュすることができます。 その結果、クライアントは、サーバーをポーリングしたり更新プログラムについて新しい HTTP 要求を送信したりしなくても、IoT デバイスからのリアルタイムのメトリックと状態で更新されます。

## <a name="prerequisites"></a>前提条件

操作を続行する前に完了しておく前提条件を次に示します。

* この記事でソリューションを Azure SignalR Service と統合する前に、Azure Digital Twins の[エンドツーエンドのソリューションの接続](tutorial-end-to-end.md)に関するチュートリアルを完了する必要があります。それに基づいてこの操作方法の記事が作成されているからです。 そのチュートリアルでは、仮想 IoT デバイスで動作してデジタル ツインの更新をトリガーする Azure Digital Twins インスタンスを設定する手順が示されています。 この操作方法の記事では、Azure SignalR Service を使用してそれらの更新プログラムをサンプル Web アプリに接続します。

* そのチュートリアルから次の値が必要になります。
  - Event Grid トピック
  - Resource group
  - App Service/関数アプリの名前
    
* お使いのコンピューターに [Node.js](https://nodejs.org/) がインストールされている必要があります。

このガイドで使用する必要があるため、自分の Azure アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインしてください。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

以下のパスを使用して、Azure SignalR Service を Azure Digital Twins に接続します。 図の A、B、C の各セクションは、[エンド ツー エンドのチュートリアルの事前準備](tutorial-end-to-end.md)のアーキテクチャ図から引用したものです。 この操作方法に関する記事では、SignalR およびクライアント アプリと通信する 2 つの新しい Azure 関数を含む既存のアーキテクチャにセクション D を作成します。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Azure Digital Twins との間でやり取りされるデータを示すエンドツーエンド シナリオでの Azure サービスの図。" lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>サンプル アプリケーションのダウンロード

最初に、必要なサンプル アプリをダウンロードします。 次の両方のサンプルが必要です。
* [Azure Digital Twins のエンドツーエンド サンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): このサンプルには、Azure Digital Twins インスタンス内でデータを移動するための 2 つの Azure 関数を保持する *AdtSampleApp* が含まれています (このシナリオの詳細については、[エンドツーエンドのソリューションの接続](tutorial-end-to-end.md)に関するチュートリアルで学習できます)。 また、IoT デバイスをシミュレートし、1 秒ごとに新しい温度値を生成する *DeviceSimulator* サンプル アプリケーションも含まれています。
    - 「[前提条件](#prerequisites)」にあるチュートリアルの一部としてサンプルをまだダウンロードしていない場合は、[サンプルにアクセス](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)し、タイトルの下にある *[コードの参照]* ボタンを選択してください。 これにより、サンプル用の GitHub リポジトリに移動します。 *[Code]\(コード\)* ボタンと、 *[Download ZIP]\(ZIP のダウンロード\)* を選択することによって、.zip 形式でこれをダウンロードできます。

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub のデジタル ツインのサンプル リポジトリと、それを zip としてダウンロードする手順を示すスクリーンショット。" lightbox="media/includes/download-repo-zip.png":::

    このボタンにより、お使いのマシンにサンプル リポジトリのコピーが **digital-twins-samples-master.zip** としてダウンロードされます。 フォルダーを解凍します。
* [SignalR 統合 Web アプリのサンプル](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): このサンプルの React Web アプリでは、Azure SignalR Service からの Azure Digital Twins テレメトリ データが使用されます。
    -  サンプル リンクに移動し、同じダウンロード プロセスを使用してサンプルのコピーをお使いのコンピューターに _**digitaltwins-signalr-webapp-sample-main.zip**_ としてダウンロードします。 フォルダーを解凍します。

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

次のセクションでもう一度使用するので、Azure portal のブラウザー ウィンドウは開いたままにしておきます。

## <a name="publish-and-configure-the-azure-functions-app"></a>Azure Functions アプリの発行と構成

このセクションでは、次の 2 つの Azure 関数を設定します。
* **negotiate** - HTTP トリガー関数。 *SignalRConnectionInfo* 入力バインドを使用して有効な接続情報を生成し、返します。
* **broadcast** - [Event Grid](../event-grid/overview.md) トリガー関数。 Event Grid から Azure Digital Twins テレメトリ データを受信し、前の手順で作成した *SignalR* インスタンスの出力バインドを使用して、接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。

Visual Studio (または任意の別のコード エディター) を起動し、*digital-twins-samples-master > ADTSampleApp* フォルダー内のコード ソリューションを開きます。 その後、次の手順を実行して関数を作成します。

1. *SampleFunctionsApp* プロジェクトに **SignalRFunctions.cs** と呼ばれる新しい C# クラスを作成します。 新しいクラスを作成する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)」を参照してください。

1. このクラス ファイルの内容を次のコードに置き換えます。
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Visual Studio の "*パッケージ マネージャー コンソール*" ウィンドウ、またはお使いのコンピューター上の任意のコマンド ウィンドウで、フォルダー *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* に移動し、次のコマンドを実行して `SignalRService` NuGet パッケージをプロジェクトにインストールします。
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    このコマンドを実行すると、そのクラスに含まれる依存関係の問題が解決されます。

1. 関数を Azure に発行します。 エンド ツー エンドのチュートリアルの[前提条件](#prerequisites)で使用したものと同じ App Service や関数アプリにそれを発行することも、新しいものを作成することもできます。ただし、重複を最小限に抑えるために同じものを使用することをお勧めします。 Visual Studio を使用して関数を発行する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

### <a name="configure-the-function"></a>関数を構成する

次に、Azure SignalR インスタンスと通信するように関数を構成します。 まず、SignalR インスタンスの **接続文字列** を収集し、関数アプリの設定に追加します。

1. [Azure portal](https://portal.azure.com/) にアクセスし、ポータルの上部にある検索バーで SignalR インスタンスの名前を検索します。 インスタンスを選択して開きます。
1. インスタンスのメニューから **[キー]** を選択して、SignalR Service インスタンスの接続文字列を表示します。
1. "*コピー*" アイコンを選択して、プライマリ接続文字列をコピーします。

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="SignalR インスタンスの [キー] ページが表示された Azure portal のスクリーンショット。接続文字列がコピーされています。" lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. 最後に、次の Azure CLI コマンドを使用して、Azure SignalR の **接続文字列** を関数のアプリ設定に追加します。 また、プレースホルダーを、[チュートリアルの前提条件](how-to-integrate-azure-signalr.md#prerequisites)のリソース グループと App Service/関数アプリの名前に置き換えます。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com) で実行するか、[Azure CLI がマシンにインストールされている](/cli/azure/install-azure-cli)場合はローカルで実行できます。
 
    ```azurecli-interactive
    az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    このコマンドの出力では、Azure 関数用に設定されたすべてのアプリ設定が出力されます。 一覧の下部で `AzureSignalRConnectionString` を検索して、それが追加されていることを確認します。

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="'AzureSignalRConnectionString' と呼ばれるリスト項目が表示されている、コマンド ウィンドウの出力のスクリーンショット。":::

## <a name="connect-the-function-to-event-grid"></a>Event Grid に関数を接続する

次に、*broadcast* Azure 関数を **Event Grid トピック** ([チュートリアルの前提条件](how-to-integrate-azure-signalr.md#prerequisites)で作成) でサブスクライブします。 このアクションにより、テレメトリ データは thermostat67 ツインから Event Grid トピックを経て関数へと流れることができます。 ここから、この関数によってすべてのクライアントにそのデータをブロードキャストできます。

データをブロードキャストするには、Event Grid トピックからエンドポイントとしての *broadcast* Azure 関数への **イベント サブスクリプション** を作成します。

[Azure portal](https://portal.azure.com/) の上部の検索バーで、Event Grid トピックの名前を検索してそのトピックに移動します。 *[+ イベント サブスクリプション]* を選択します。

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure portal でのイベント サブスクリプションの作成方法のスクリーンショット。":::

*[イベント サブスクリプションの作成]* ページで、各フィールドに次のように入力します (既定値が入力されるフィールドは省略しています)。
* *[イベント サブスクリプションの詳細]*  >  **[名前]** : イベント サブスクリプションに名前を付けます。
* *[エンドポイントの詳細]*  >  **[エンドポイントのタイプ]** : メニュー オプションから *[Azure 関数]* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイント]** : *[エンドポイントの選択]* リンクを選択して、 *[Azure 関数の選択]* ウィンドウを開きます。
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[関数アプリ]** 、 **[関数]** (*broadcast*) を入力します。 これらのフィールドの一部は、サブスクリプションの選択後に自動的に設定される場合があります。
    - **[選択の確認]** を選択します。

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure portal でのイベント サブスクリプションを作成するためのフォームのスクリーンショット。":::

再び *[イベント サブスクリプションの作成]* ページで、 **[作成]** を選択します。

この時点で、 *[Event Grid Topic]\(Event Grid トピック\)* ページに 2 つのイベント サブスクリプションが表示されます。

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="[Event Grid トピック] ページの 2 つのイベント サブスクリプションを示す Azure portal のスクリーンショット。" lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Web アプリの構成と実行

このセクションでは、動作中の結果を見ていきます。 まず、設定しておいた Azure SignalR のフローに接続するように **サンプル クライアント Web アプリ** を構成します。 次に、Azure Digital Twins インスタンスを介してテレメトリ データを送信する **シミュレートされたデバイス サンプル アプリ** を起動します。 その後、サンプル Web アプリを表示して、サンプル Web アプリをリアルタイムで更新するシミュレートされたデバイス データを確認します。

### <a name="configure-the-sample-client-web-app"></a>サンプル クライアント Web アプリを構成する

次に、サンプル クライアント Web アプリを構成します。 まず *negotiate* 関数の **HTTP エンドポイント URL** を収集し、それを使用してお使いのコンピューター上でアプリ コードを構成します。

1. Azure portal の [[関数アプリ]](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) ページにアクセスし、一覧から関数アプリを選択します。 アプリ メニューで、 *[関数]* を選択し、*negotiate* 関数を選択します。

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure portal の関数アプリのスクリーンショット。メニューで 'Functions' が強調表示され、関数の一覧で 'negotiate' が強調表示されています。":::

1. *[関数の URL の取得]* を選択し、**_/api_ までの値 (最後の _/negotiate?_ は含めないでください)** をコピーします。 次の手順でこの値を使用します。

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="[関数の URL の取得] ボタンと関数の URL が強調表示されている 'negotiate' 関数が表示された Azure portal のスクリーンショット。":::

1. Visual Studio または任意のコード エディターを使用して、「[サンプル アプリケーションのダウンロード](#download-the-sample-applications)」セクションでダウンロードした解凍済みの _**digitaltwins-signalr-webapp-sample-main**_ フォルダーを開きます。

1. *src/App.js* ファイルを開き、`HubConnectionBuilder` 内の関数 URL を、前の手順で保存した **negotiate** 関数の HTTP エンドポイント URL に置き換えます。

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function-URL>')
            .build();
    ```
1. Visual Studio の "*開発者コマンド プロンプト*" またはコンピューター上の任意のコマンド ウィンドウで、*digitaltwins-signalr-webapp-sample-main\src* フォルダーに移動します。 次のコマンドを実行して、依存ノード パッケージをインストールします。

    ```cmd
    npm install
    ```

次に、Azure Portal で関数アプリでのアクセス許可を設定します。
1. Azure portal の [[関数アプリ]](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) ページで、関数アプリ インスタンスを選択します。

1. インスタンスのメニューを下にスクロールし、 *[CORS]* を選択します。 [CORS] ページで、空のボックスに `http://localhost:3000` を入力して、許可された配信元として追加します。 *[Access-Control-Allow-Credentials を有効にする]* ボックスをオンにして、 *[保存]* を選択します。

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Azure 関数で CORS 設定が表示されている Azure portal のスクリーンショット。":::

### <a name="run-the-device-simulator"></a>デバイス シミュレーターを実行する

エンド ツー エンドのチュートリアルの事前準備では、IoT Hub を介してデータを Azure Digital Twins インスタンスに送信するように[デバイス シミュレーターを構成](tutorial-end-to-end.md#configure-and-run-the-simulation)しました。

ここでは、*digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln* にあるシミュレーター プロジェクトを開始するだけで済みます。 Visual Studio を使用している場合は、プロジェクトを開いてから、ツール バーにあるこのボタンを使用して実行できます。

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="DeviceSimulator プロジェクトが開いた、Visual Studio のスタート ボタンのスクリーンショット。":::

コンソール ウィンドウが開いて、シミュレートされた温度のテレメトリ メッセージが表示されます。 これらのメッセージは Azure Digital Twins インスタンスを介して送信され、そこでその後、Azure 関数と SignalR によって取得されます。

このコンソールで行うべき作業は他にありませんが、次のステップに取り組む間、コンソールは実行したままにしておいてください。

### <a name="see-the-results"></a>結果を見る

動作中の結果を確認するには、**SignalR 統合 Web アプリのサンプル** を起動します。 そのためには、任意のコンソール ウィンドウから、*digitaltwins-signalr-webapp-sample-main\src* の場所で、次のコマンドを実行します。

```cmd
npm start
```

このコマンドを実行すると、サンプル アプリが実行されているブラウザー ウィンドウが開き、ビジュアル温度計が表示されます。 アプリが実行されると、Azure Digital Twins を介して伝達されるデバイス シミュレーターからの温度テレメトリの値が、Web アプリによってリアルタイムで反映されるのを確認できるようになります。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="ビジュアル温度計が表示されている、サンプル クライアント Web アプリのスクリーンショット。反映されている温度は 67.52 です。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースがもう必要ない場合は、次の手順に従って削除します。 

Azure Cloud Shell またはローカルの Azure CLI から [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 リソース グループを削除すると、以下も削除されます。
* Azure Digital Twins インスタンス (エンド ツー エンドのチュートリアルから)
* IoT Hub とハブのデバイス登録 (エンド ツー エンドのチュートリアルから)
* Event Grid トピックと関連付けられているサブスクリプション
* Azure Functions アプリ (3 つのすべての関数と、ストレージなどの関連リソースを含む)
* Azure SignalR インスタンス

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

最後に、ローカル コンピューターにダウンロードしたプロジェクトのサンプル フォルダー (*digital-twins-samples-master.zip*、*digitaltwins-signalr-webapp-sample-main.zip* と、これらを解凍したもの) を削除します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Digital Twins のテレメトリ イベントをサンプル クライアントアプリケーションにブロードキャストするように、SignalR で Azure 関数を設定しました。

次に、Azure SignalR Service の詳細を確認します。
* [Azure SignalR サービスとは](../azure-signalr/signalr-overview.md)

または、Azure Functions を使用した Azure SignalR Service 認証の詳細を確認します。
* [Azure SignalR Service の認証](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)