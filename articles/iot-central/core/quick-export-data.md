---
title: クイック スタート - Azure IoT Central からデータをエクスポートする
description: クイック スタート - 他のクラウド サービスと統合するために IoT Central のエクスポート機能を使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0728eed5a1deeee20554998512edd70f5e983491
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492313"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>クイック スタート: IoT Central アプリケーションからデータをエクスポートする

このクイックスタートでは、Azure IoT Central アプリケーションから別のクラウド サービスにデータを継続的にエクスポートする方法を示します。 このクイックスタートでは、簡単に設定できるよう、リアルタイム分析を実現するフル マネージドのデータ分析サービスである [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) を使用します。 Azure Data Explorer を使用すると、**IoT Plug and Play** スマートフォン アプリなどのデバイスから取得したテレメトリを保存、照会、処理することができます。

このクイック スタートでは次の作業を行います。

- IoT Central のデータ エクスポート機能を使用して、スマートフォン アプリから送信されたテレメトリを Azure Data Explorer データベースにエクスポートします。
- Azure Data Explorer を使用してテレメトリに対するクエリを実行します。

## <a name="prerequisites"></a>前提条件

- 開始する前に、最初のクイックスタート「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」を完了する必要があります。 2 番目のクイックスタート「[デバイスのルールとアクションを構成する](quick-configure-rules.md)」は省略可能です。

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="install-azure-services"></a>Azure サービスをインストールする

IoT Central アプリケーションからデータをエクスポートするには、まず Azure Data Explorer クラスターとデータベースを用意する必要があります。 このクイックスタートでは、[Azure Cloud Shell](https://shell.azure.com) の Bash 環境を使用して、これらの作成と構成を行います。

Azure Cloud Shell で、次のスクリプトを実行します。 スクリプトを実行する前に、`clustername` の値をクラスタの一意の名前に置き換えてください。

> [!IMPORTANT]
> スクリプトの実行には、少なくとも 10 分かかります。

```azurecli
clustername="<A unique name for your cluster>"
databasename="phonedata"
location="eastus"
resourcegroup="IoTCentralExportData"

az extension add -n kusto

# Create a resource group for the Azure Data Explorer cluster
az group create --location $location \
    --name $resourcegroup

# Create the Azure Data Explorer cluster
# This command takes at least 10 minutes to run
az kusto cluster create --cluster-name $clustername \
    --sku name="Standard_D11_v2"  tier="Standard" \
    --enable-streaming-ingest=true \
    --enable-auto-stop=true \
    --resource-group $resourcegroup --location $location

# Crete a database in the cluster
az kusto database create --cluster-name $clustername \
    --database-name $databasename \
    --read-write-database location=$location soft-delete-period=P365D hot-cache-period=P31D \
    --resource-group $resourcegroup

# Create a service principal to use when authenticating from IoT Central
SP_JSON=$(az ad sp create-for-rbac --skip-assignment --name spforiotcentral)

az kusto database-principal-assignment create --cluster-name $clustername \
                                              --database-name $databasename \
                                              --principal-id $(jq -r .appId <<< $SP_JSON) \
                                              --principal-assignment-name spforiotcentral \
                                              --resource-group $resourcegroup \
                                              --principal-type App \
                                              --role Admin

echo "Azure Data Explorer URL: $(az kusto cluster show --name $clustername --resource-group $resourcegroup --query uri -o tsv)"
echo "Client ID: $(jq -r .appId <<< $SP_JSON)"
echo "Tenant ID: $(jq -r .tenant <<< $SP_JSON)"
echo "Client secret: $(jq -r .password <<< $SP_JSON)" 
```

**Azure Data Explorer URL**、**Client ID**、**Tenant ID**、および **Client secret** を書き留めます。 これらの値は本クイックスタートで後ほど使用します。

## <a name="configure-the-database"></a>データベースを構成する

**IoT Plug and Play** スマートフォン アプリから取得した加速度計データを格納するテーブルをデータベースに追加するには、以下の手順を実行します。

1. 前のセクションの **Azure Data Explorer URL** を使用して、Azure Data Explorer 環境に移動します。

1. クラスター ノードを展開し、**phonedata** データベースを選択します。 クエリ ウィンドウのスコープが `Scope:yourclustername.eastus/phonedata` に変わります。

1. クエリ エディターに次の Kusto スクリプトを貼り付けて、 **[実行]** を選択します。

    ```kusto
    .create table acceleration (
      EnqueuedTime:datetime,
      Device:string,
      X:real,
      Y:real,
      Z:real
    );
    ```

    結果は次のスクリーンショットのようになります。

    :::image type="content" source="media/quick-export-data/azure-data-explorer-create-table.png" alt-text="Azure Data Explorer でテーブルを作成した結果を示すスクリーンショット。":::

1. Azure Data Explorer で新しいタブを開き、次の Kusto スクリプトを貼り付けます。 このスクリプトにより、**acceleration** テーブルのストリーミング イングレスが有効になります。

    ```kusto
    .alter table acceleration policy streamingingestion enable;
    ```

Azure Data Explorer ページは、ブラウザー上で開いたままにします。 IoT Central アプリケーションでデータ エクスポートを構成した後、クエリを実行して **acceleration** テーブルに格納された加速度計のテレメトリを表示します。

## <a name="configure-data-export"></a>データのエクスポートを構成する

IoT Central からデータのエクスポート先を構成するには、以下の手順に従います。

1. IoT Central アプリケーションの **[データのエクスポート]** ページに移動します。
1. **[エクスポート先]** タブを選択し、次に **[宛先の追加]** を選択します。
1. エクスポート先の名前として *Azure Data Explorer* と入力します。 エクスポート先の種類として **Azure Data Explorer** を選択します。
1. **[クラスター URL]** に、事前に書き留めておいた **Azure Data Explorer URL** を入力します。
1. **[データベース名]** に、*phonedata* と入力します。
1. **[テーブル名]** に、*acceleration* と入力します。
1. **[クライアント ID]** を、事前に書き留めておいた **Client ID** に置き換えます。
1. **[テナント ID]** に、事前に書き留めておいた **Tenant ID** を入力します。
1. **[クライアント シークレット]** に、事前に書き留めておいた **Client secret** を入力します。
1. **[保存]** を選択します。

データのエクスポートを構成するには、以下の手順を実行します。

1. **[データのエクスポート]** ページで、 **[エクスポート]** タブを選択し、次に **[エクスポートの追加]** を選択します。
1. エクスポート名として、「*Phone accelerometer*」と入力します。
1. エクスポートするデータの種類として **[テレメトリ]** を選択します。
1. 次の表の情報を使用して、2 つのフィルターを追加します。

    | 名前 | 演算子 | 値 |
    | ---- | -------- | ----- |
    | デバイス テンプレート | 次の値に等しい | IoT Plug and Play モバイル |
    | センサー/アクセラレーション/X | Exists | 該当なし |

    すべての条件が true の場合にデータをエクスポートするオプションが設定されていることを確認します。

1. 送信先として **Azure Data Explorer** を追加します。
1. 送信先にデータ変換を追加します。 **データ変換ページ** の **[2. Build transformation query]\(2. 変換クエリの作成\)** フィールドに以下のクエリを追加します。

    ```json
    import "iotc" as iotc;
    {
        Device: .device.id,
        EnqueuedTime: .enqueuedTime,
        X: .telemetry | iotc::find(.name == "accelerometer").value.x,
        Y: .telemetry | iotc::find(.name == "accelerometer").value.y,
        Z: .telemetry | iotc::find(.name == "accelerometer").value.z
    }
    ```

    :::image type="content" source="media/quick-export-data/data-transformation-query.png" alt-text="エクスポートのデータ変換クエリを示すスクリーンショット。":::

    変換の動作を確認したり、クエリを操作したい場合は、以下のテレメトリ メッセージのサンプルを **[1. Add your input message]\(1. 入力メッセージの追加\)** に貼り付けます。

    ```json
    {
      "messageProperties": {},
      "device": {
        "id": "8hltz8xa7n",
        "properties": {
          "reported": []
        },
        "approved": true,
        "types": [],
        "name": "8hltz8xa7n",
        "simulated": false,
        "provisioned": true,
        "modules": [],
        "templateId": "urn:modelDefinition:vlcd3zvzdm:y425jkkpqzeu",
        "templateName": "IoT Plug and Play mobile",
        "organizations": [],
        "cloudProperties": [],
        "blocked": false
      },
      "component": "sensors",
      "applicationId": "40a97c91-50cc-44f0-9f63-71386613facc",
      "messageSource": "telemetry",
      "telemetry": [
        {
          "id": "dtmi:azureiot:PhoneSensors:__accelerometer;1",
          "name": "accelerometer",
          "value": {
            "x": 0.09960123896598816,
            "y": 0.09541380405426025,
            "z": 9.907781600952148
          }
        }
      ],
      "enqueuedTime": "2021-11-12T10:01:30.588Z",
      "enrichments": {}
    }
    ```

1. 変換を保存します。 次に、データ エクスポート定義を保存します。

エクスポート ステータスに **[正常]** と表示されるまで待機します。

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="正常な状態の実行中のデータ エクスポートを示すスクリーンショット。":::

## <a name="query-exported-data"></a>エクスポートされたデータのクエリを実行する

Azure Data Explorer で新しいタブを開き、次の Kusto クエリを貼り付けてから **[実行]** を選択して加速度計のテレメトリをプロットします。

```kusto
['acceleration'] 
    | project EnqueuedTime, Device, X, Y, Z
    | render timechart 
```

十分なデータが収集されるまで数分かかる場合があります。 電話をさまざまな向きに傾けて、テレメトリ値が変化していることを確認します。

:::image type="content" source="media/quick-export-data/acceleration-plot.png" alt-text="加速度計テレメトリに対するクエリ結果のスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

ご自身のサブスクリプションから Azure Data Explorer インスタンスを削除し、不要な課金を避けるには、**IoTCentralExportData** リソース グループを [Azure portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) から削除するか、以下のコマンドを Azure Cloud Shell で実行します。

```azurecli
az group delete --name IoTCentralExportData
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT Central から別の Azure サービスにデータを継続的にエクスポートする方法について説明しました。

これでデータをエクスポートする方法がわかったので、次に以下の手順に進むことをお勧めします。

> [!div class="nextstepaction"]
> [デバイス テンプレートの構築および管理](howto-set-up-template.md)。
