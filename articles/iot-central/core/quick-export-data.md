---
title: クイック スタート - Azure IoT Central からデータをエクスポートする
description: クイック スタート - 他のクラウド サービスと統合するために IoT Central のエクスポート機能を使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 06e387e4742d86f4176d262ae2e2e9db863386ca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300930"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>クイック スタート: IoT Central アプリケーションからデータをエクスポートする

このクイックスタートでは、Azure IoT Central アプリケーションから別のクラウド サービスにデータを継続的にエクスポートする方法を示します。 このクイックスタートでは、簡単に準備できるように、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) を使用して、**IoT プラグ アンド プレイ** スマートフォン アプリからのテレメトリの格納、クエリ、処理を行えるようにします。

このクイック スタートでは次の作業を行います。

- IoT Central のデータ エクスポート機能を使用して、スマートフォン アプリから送信されたテレメトリを Azure Event Hubs キューにエクスポートします。
- Event Hubs キューから Azure Data Explorer データベースにテレメトリを取り込みます。
- Azure Data Explorer を使用してテレメトリに対するクエリを実行します。

## <a name="prerequisites"></a>前提条件

開始する前に、最初のクイックスタート「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」を完了する必要があります。 2 番目のクイックスタート「[デバイスのルールとアクションを構成する](quick-configure-rules.md)」は省略可能です。

## <a name="install-azure-services"></a>Azure サービスをインストールする

IoT Central アプリケーションからデータをエクスポートする前に、Event Hubs キューと Azure Data Explorer データベースを作成する必要があります。

次のボタンを選択してサービスをデプロイします。

[![継続的なデータのエクスポートのための、Azure へのデプロイのボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Fquickstart-cde%2Fdeploy-azure-services.json)

**[カスタム デプロイ]** フォームで次の手順を行います。

- *central-quickstart* という名前の新しいリソース グループを作成します。
- 最も近いリージョンを選びます。
- 小文字と数字のみを使用して一意の Azure Data Explorer 名を入力します。 たとえば *contosocentraladx* とします。
- 小文字と数字のみを使用して一意の Azure Event Hubs 名前空間名を入力します。 たとえば *contosocentraleh* とします。

**[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

> [!TIP]
> デプロイには約 10 分かかると予想されます。

デプロイが完了したら、返された Azure Event Hubs の接続文字列をメモします。

1. デプロイが完了するまで待ちます。
1. **[出力]** を選択します。
1. **eventHubConnectionString** 値をメモします。これは後で使用します。

    :::image type="content" source="media/quick-export-data/connection-string.png" alt-text="Event Hubs 接続文字列。":::

## <a name="configure-data-export"></a>データのエクスポートを構成する

このシナリオでは、Azure Event Hubs キューを使用して、IoT Central アプリケーションによって収集されたテレメトリを Azure Data Explorer に配信します。

IoT Central からのデータのエクスポートを構成するには:

1. IoT Central アプリケーションの **[データのエクスポート]** ページに移動します。
1. **[エクスポート先]** タブを選択し、次に **[宛先の追加]** を選択します。
1. 宛先名として「*Event Hub 1*」を入力します。 宛先の種類として **[Azure Event Hubs]** を選択します。
1. 前のセクションで保存した Event Hubs キュー接続文字列を入力します。 **[イベント ハブ]** には *centraltelemetry* が自動的に入力されます。
1. **[保存]** を選択します。
1. **[データのエクスポート]** ページで、 **[エクスポート]** タブを選択し、次に **[エクスポートの追加]** を選択します。
1. エクスポート名として「*Telemetry Export*」と入力します。
1. エクスポートするデータの種類として **[テレメトリ]** を選択します。
1. 宛先として **[Event Hub 1]** を選択します。
1. **[保存]** を選択します。

エクスポートが実行されている場合、 **[データのエクスポート]** ページの状態は **[正常]** になります。

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="正常な状態の実行中のデータ エクスポートを示すスクリーンショット。":::

## <a name="configure-data-ingestion"></a>データ インジェストを構成する

続行する前に、スマートフォン アプリが IoT Central に接続され、データが送信されていることを確認します。

IoT Central アプリケーションから Event Hubs キューにテレメトリが継続的にエクスポートされています。 このセクションでは、テレメトリをクエリ可能なテーブルに継続的に取り込むように Azure Data Explorer クラスターを構成します。

データ インジェストを構成するには:

1. Azure portal の **central-quickstart** リソース グループで、ご自身の Azure Data Explorer クラスターに移動します。

    :::image type="content" source="media/quick-export-data/azure-data-explorer-portal.png" alt-text="Azure Data Explorer の概要ページのスクリーンショット。":::

1. **[Ingest new data]\(新しいデータの取り込み\)** を選択します。
1. **[Ingest new data]\(新しいデータの取り込み\)** ページで、ご自身のクラスターと **iotcentraldata** データベースを選択します。
1. **[新規作成]** を選択して、*telemetry* という新しいテーブルを作成します。
1. ソースの種類として **[Event Hubs]** を選択します。
1. データ接続名として「*IoT-Central-connection*」を入力します。
1. 次の表の情報を使用して、フォームの残りの部分を入力します。

    | フィールド                   | 値                            |
    |-------------------------|----------------------------------|
    | サブスクリプション            | Azure サブスクリプションを選択します。   |
    | Event Hub 名前空間     | Event Hubs 名前空間を選択します。 |
    | イベント ハブ               | `centraltelemetry`               |
    | コンシューマー グループ          | `$Default`                       |
    | データ形式             | JSON                             |
    | 圧縮             | なし                             |
    | イベント システム プロパティ | 空白のまま                      |

1. **[スキーマの編集]** を選択します。
1. **[スキーマ]** ページに、Event Hubs キューにあるメッセージのデータ プレビューが表示されます。
1. 入れ子になったレベルの値を `3` に変更して JSON を展開し、各テレメトリ値をそれぞれの列に表示します。
1. **[Start ingestion]\(インジェストの開始\)** を選択します。 データ インジェストが完了するまで待ちます。

:::image type="content" source="media/quick-export-data/data-ingestion-complete.png" alt-text="Azure Data Explorer での競合するデータ インジェストを示すスクリーンショット。":::

次のセクションで使用するので、このページは開いたままにしておきます。

## <a name="query-exported-data"></a>エクスポートされたデータのクエリを実行する

IoT Central アプリケーションから Azure Data Explorer クラスターにデータが継続的に取り込まれています。 データのクエリを実行するには:

1. 前のセクションの Azure Data Explorer ページで、 **[Take 10]** クイック クエリを選択します。 これは、**telemetry** テーブルから 10 個のレコードを選択するクエリです。
1. このクエリを次のクエリに置き換えます。

    ```kusto
    ['telemetry'] 
    | where isnotnull(telemetry_magnetometer_x)
    | project Time=todatetime(enqueuedTime), deviceId, telemetry_magnetometer_x, telemetry_magnetometer_y, telemetry_magnetometer_z
    | render timechart 
    ```

    このクエリを使用すると、スマートフォン アプリからの磁力計テレメトリ値がタイムラインにプロットされます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

ご自身のサブスクリプションから Azure Data Explorer インスタンスと Event Hubs 名前空間を削除して、不要な課金が発生しないようにするために、**central-quickstarts** リソース グループを [Azure portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) から削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT Central から別の Azure サービスにデータを継続的にエクスポートする方法について説明しました。

これでデータをエクスポートする方法がわかったので、次に以下の手順に進むことをお勧めします。

> [!div class="nextstepaction"]
> [デバイス テンプレートの構築および管理](howto-set-up-template.md)。
