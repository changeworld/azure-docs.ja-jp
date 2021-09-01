---
title: ツインからツインへのイベント処理を設定する
titleSuffix: Azure Digital Twins
description: ツイン グラフを介してイベントを伝達するために Azure で関数を作成する方法を確認します。
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252583"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>Azure Functions を使用してツインからツインへのイベント処理を設定する

完全に接続された Azure Digital Twins グラフは、イベントの伝達によって駆動されます。 データは IoT Hub などの外部ソースから Azure Digital Twins に届いてから、Azure Digital Twins グラフを介して伝達され、必要に応じて関連するツインが更新されます。

たとえば、建物のフロアとルームを表す図について考えます。各フロアには複数のルームがあります。 ルーム ツインの気温プロパティが更新されるたびに、同じフロアのすべてのルームについて新しい平均気温が計算され、フロア ツインの気温プロパティが、そこに含まれるすべてのルーム (更新されたものも含む) の新しい平均気温を反映するように更新されるように、ツインからツインへのデータ フローを設定する必要があります。 

この記事では、ツインからツインにイベントを送信して、グラフ内の別のツインでのプロパティの変更やその他のデータに応じてツインを更新する方法について説明します。 現在、ツインからツインへの更新は、グラフの他の領域に影響するツインのライフサイクル イベントを監視し、それに応じて他のツインに変更を加える [Azure 関数](../azure-functions/functions-overview.md)を設定して処理します。

## <a name="prerequisites"></a>前提条件

この記事では、**Visual Studio** を使用します。 最新バージョンは、[Visual Studio のダウンロード](https://visualstudio.microsoft.com/downloads/) ページからダウンロードできます。

ツインからツインの処理を設定するには、**Azure Digital Twins インスタンス** を使用する必要があります。 インスタンスの作成方法については、「[Azure Digital Twins インスタンスと認証を設定する](./how-to-set-up-instance-portal.md)」を参照してください。 インスタンスには、データを相互に送信するために、少なくとも **2 つのツイン** が含まれる必要があります。

必要に応じて、ツインのために [IoT Hub を介した自動テレメトリ インジェスト](how-to-ingest-iot-hub-data.md)を設定することをお勧めします。 これは、ツインからツインにデータを送信するためには必要ありませんが、ツイン グラフがライブ テレメトリによって駆動される完成ソリューションでは重要な部分になります。

## <a name="set-up-endpoint-and-route"></a>エンドポイントとルートを設定する

ツインからツインへのイベント処理を設定するには、最初に、Azure Digital Twins に **エンドポイント** を作成し、そのエンドポイントに対する **ルート** を作成します。 更新が行われているツインは、ルートを使用して、更新イベントに関する情報をエンドポイントに送信します (そこで、Event Grid が後で情報を選択し、処理のために Azure 関数に渡します)。

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>Azure 関数を作成する

次に、エンドポイントでリッスンし、ルート経由で送信されるツイン イベントを受信する Azure 関数を作成します。 

1. 最初に、コンピューターの Visual Studio に Azure Functions プロジェクトを作成します。 これを行う方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)」を参照してください。

2. 次のパッケージをプロジェクトに追加します (Visual Studio NuGet パッケージ マネージャーまたはコマンド ライン ツールの `dotnet`コマンドを使用できます)。

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. 関数のロジックを入力します。 開始する際に役立つ、いくつかのシナリオのサンプル関数コードを [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) リポジトリで参照できます。

5. 関数アプリを Azure に発行します。 関数アプリを発行する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>Function App を構成する

関数が Azure Digital Twins にアクセスするには、アクセスするインスタンスの情報とアクセス許可が必要です。 このセクションでは、インスタンスを見つけてアクセスできるように、関数に **アクセス ロールを割り当て** て、**アプリケーション設定を構成** します。

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>Event Grid に関数を接続する

次に、Azure 関数を、前に作成したイベント グリッド トピックにサブスクライブします。 これにより、更新されたツインからイベント グリッド トピックを介して関数にデータがフローされます。

これを行うには、先ほど作成したイベント グリッド トピックから Azure 関数にデータを送信する **Event Grid サブスクリプション** を作成します。

次の CLI コマンドを使用して、サブスクリプション ID、リソース グループ、関数アプリ、関数名のプレースホルダーに入力します。

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

これで、関数が Event Grid トピックを介してイベントを受信できます。 データ フローの設定が完了しました。

## <a name="test-and-verify-results"></a>テストして結果を検証する

最後の手順では、ツインを更新し、関連するツインが Azure 関数のロジックに従って更新されることを確認して、フローが動作することを確かめます。

プロセスを開始するには、イベント フローのソースであるツインを更新します。 [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update)、[Azure Digital Twins SDK](how-to-manage-twin.md#update-a-digital-twin)、または [Azure Digital Twins REST API](how-to-use-postman.md?tabs=data-plane) を使用して、更新を行うことができます。

次に、関連するツインについて Azure Digital Twins インスタンスにクエリを実行します。 [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query)、または [Azure Digital Twins REST API および SDK](how-to-query-graph.md#run-queries-with-the-api) を使用できます。 ツインがデータを受信し、期待した通り更新されることを確認します。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Digital Twins でのツインからツインへのイベント処理を設定しました。 次は、IoT Hub デバイスからの受信テレメトリに基づいてこのフローを自動的にトリガーする Azure 関数を設定します ([IoT Hub からのテレメトリの取り込み](how-to-ingest-iot-hub-data.md)に関するページ)。
