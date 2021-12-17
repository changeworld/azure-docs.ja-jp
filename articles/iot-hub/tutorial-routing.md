---
title: チュートリアル - Azure CLI を使用して Azure IoT Hub のメッセージ ルーティングを構成する
description: チュートリアル - Azure CLI と Azure portal を使用して Azure IoT Hub のメッセージ ルーティングを構成する
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: fbe2108a50e29b91c8c324201085d9ba1f6969bc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550143"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>チュートリアル:Azure CLI と Azure portal を使用して IoT Hub のメッセージ ルーティングを構成する

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Azure CLI を使用してベース リソースを作成する

このチュートリアルでは、Azure CLI を使用してベース リソースを作成してから、[Azure portal](https://portal.azure.com) を使用してメッセージ ルーティングを構成する方法およびテスト用の仮想デバイスを設定する方法を説明します。

以下のスクリプトをコピーして Cloud Shell に貼り付け、Enter キーを押してください。 スクリプトが 1 行ずつ実行されます。 これで、ストレージ アカウント、IoT ハブ、Service Bus 名前空間、Service Bus キューなど、このチュートリアルのベース リソースが作成されます。

IoT ハブ名やストレージ アカウント名など、いくつかのリソース名はグローバルに一意であることが必要です。 それを簡単にするために、それらのリソース名には、*randomValue* という英数字のランダム値が追加されます。 randomValue はスクリプトの冒頭で 1 度生成され、スクリプト全体で必要に応じてリソース名に追加されます。 これをランダムにしたくない場合は、空の文字列または特定の値に設定できます。

> [!TIP]
> デバッグのヒント: このスクリプトでは、読みやすくするために継続記号 (バックスラッシュ `\`) を使用しています。 スクリプトの実行で問題が生じた場合は、Cloud Shell セッションで `bash` が実行されていること、およびバックスラッシュの後ろにスペースがないことを確認してください。
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

```

ベース リソースが設定されたら、[Azure portal](https://portal.azure.com) でメッセージ ルーティングを構成できます。

## <a name="set-up-message-routing"></a>メッセージ ルーティングを設定する

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>ストレージ アカウントへのルーティング

ストレージ アカウントへのルーティングを設定します。 [メッセージ ルーティング] ウィンドウに移動して、ルートを追加します。 ルートを追加するときは、ルートに対する新しいエンドポイントを定義します。 このルーティングを設定した後、**level** プロパティが **storage** に設定されているメッセージは、ストレージ アカウントに自動的に書き込まれます。 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

次に、メッセージ ルーティングの構成を Azure Storage に設定します。

1. [Azure portal](https://portal.azure.com) で **[リソース グループ]** を選択し、自分のリソース グループを選択します。 このチュートリアルでは、**ContosoResources** を使います。

2. リソースの一覧で IoT ハブを選択します。 このチュートリアルでは、**ContosoTestHub** を使います。

3. 中央の **_[メッセージング]_ * という列で **[メッセージ ルーティング]** を選択します。+** [追加]** を選択して **[ルートの追加]** ペインを表示します。 [エンドポイント] フィールドの横にある + **[エンドポイントの追加]** を選択し、 **[ストレージ]** を選択します。 **[Add storage endpoint]\(ストレージ エンドポイントの追加\)** ウィンドウが表示されます。

   ![ルートのエンドポイントの追加を開始する](./media/tutorial-routing/01-add-a-route-to-storage.png)

4. エンドポイントの名前を入力します。 このチュートリアルでは、**ContosoStorageEndpoint** を使います。

   ![エンドポイント名の指定](./media/tutorial-routing/02-add-a-storage-endpoint.png)

5. **[コンテナーを選択します]** を選択します。 ストレージ アカウントの一覧が表示されます。 準備の手順で設定したものを選択します。このチュートリアルでは、**contosostorage** を使用します。 そのストレージ アカウント内のコンテナーの一覧が表示されます。 準備ステップで設定したコンテナーを **選択** します。 このチュートリアルでは、**contosoresults** を使います。 次に、画面の下部にある **[選択]** をクリックします。 これにより、別の **[Add storage endpoint]\(ストレージ エンドポイントの追加\)** ペインに戻ります。 選択したコンテナーの URL が表示されます。 

6. エンコードを AVRO または JSON に設定します。 このチュートリアルでは、残りのフィールドには既定値を使用します。 選択したリージョンで JSON エンコードがサポートされていない場合、このフィールドは淡色表示されます。 ファイル名の形式を設定します。 

   > [!NOTE]
   > BLOB 名の形式は、**BLOB ファイル名形式** を使用して設定します。 既定では、 `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`です。 形式には任意の順序で {iothub}、{partition}、{YYYY}、{MM}、{DD}、{HH}、および {mm} を含める必要があります。
   >
   > たとえば、既定の BLOB ファイル名形式を使用して、ハブ名が ContosoTestHub であり、日付/時刻が 2018 年 10 月 30 日午前 10:56 である場合、BLOB 名は `ContosoTestHub/0/2018/10/30/10/56` のようになります。
   > 
   > 既定では、BLOB は AVRO 形式で書き込まれます。
   >

7. ページの下部にある **[作成]** を選択してストレージ エンドポイントを作成し、ルートに追加します。 **[ルートの追加]** ペインに戻ります。 

8. ルーティング クエリ情報の残りの部分を完成させます。 このクエリでは、エンドポイントとして追加したストレージ コンテナーにメッセージを送信するための条件を指定します。 画面のフィールドを入力します。

9. フィールドの残りの部分を入力します。

   - **[名前]** : ルートの名前を入力します。 このチュートリアルでは、**ContosoStorageRoute** を使います。 次に、ストレージのエンドポイントを指定します。 このチュートリアルでは、ContosoStorageEndpoint を使います。
   
   - **[データ ソース]** の指定: ドロップダウン リストから **[Device Telemetry Messages]\(デバイス テレメトリ メッセージ\)** を選びます。   

   - **[ルートの有効化]** の選択: このフィールドが `enabled` に設定されていることを確認します。

   - **ルーティング クエリ**:クエリ文字列として、「`level="storage"`」と入力します。

   ![ルーティング クエリ情報の保存](./media/tutorial-routing/04-save-storage-route.png)
  
10.  **[保存]** を選択します。 完了すると [メッセージ ルーティング] ウィンドウに表示が戻り、そこでストレージの新しいルーティング クエリを確認できます。 [メッセージ ルーティング] ペインを閉じて、[リソース グループ] ページに戻ります。


### <a name="route-to-a-service-bus-queue"></a>Service Bus キューへのルーティング

次に、Service Bus キューへのルーティングを設定します。 [メッセージ ルーティング] ウィンドウに移動して、ルートを追加します。 ルートを追加するときは、ルートのエンドポイントとして Service Bus キューを定義します。 このルートを設定した後、**level** プロパティが **critical** に設定されているメッセージは、Service Bus キューに書き込まれます。それにより、ロジック アプリがトリガーされて、情報を含むメールが送信されます。

1. [リソース グループ] ページで、自分の IoT ハブを選択し、 **[メッセージ ルーティング]** を選択します。

2. **[メッセージ ルーティング]** ペインで、+ **[追加]** を選択します。

3. **[ルートの追加]** ペインで、+ **[エンドポイント]** の隣にある + **[追加]** を選択します。 **[Service Bus キュー]** を選択します。 **[Service Bus エンドポイントを追加する]** ウィンドウが表示されます。

   ![最初の Service Bus エンドポイントの追加](./media/tutorial-routing/05-setup-sbq-endpoint.png)

4. フィールドの残りの部分を入力します。

   **エンドポイント名**:エンドポイントの名前を入力します。 このチュートリアルでは、**ContosoSBQEndpoint** を使います。
   
   **Service Bus 名前空間**:ドロップダウン リストを使用し、準備ステップで設定したサービス バスの名前空間を選択します。 このチュートリアルでは、**ContosoSBNamespace** を使います。

   **Service Bus キュー**:ドロップダウン リストを使用して Service Bus キューを選択します。 このチュートリアルでは、**contososbqueue** を使います。

5. **[作成]** を選択して最初の Service Bus キューのエンドポイントを追加します。 **[Add a route]\(ルートの追加\)** ウィンドウに表示が戻ります。

   ![2 番目の Service Bus エンドポイントの追加](./media/tutorial-routing/06-save-sbq-endpoint.png)

6. ルーティング クエリ情報の残りの部分を完成します。 このクエリでは、エンドポイントとして追加した Service Bus キューにメッセージを送信するための条件を指定します。 画面のフィールドを入力します。 

   **[名前]** : ルートの名前を入力します。 このチュートリアルでは、**ContosoSBQueueRoute** を使います。 

   **エンドポイント**: ここには、先ほど設定したエンドポイントが表示されます。

   **データ ソース**:ドロップダウン リストから **[デバイス テレメトリのメッセージ]** を選びます。

   **[ルートの有効化]** : このフィールドを `enable` に設定します。

   **[ルーティング クエリ]** : ルーティング クエリとして `level="critical"` と入力します。 

   ![Service Bus キューのルーティング クエリを作成する](./media/tutorial-routing/07-save-servicebusqueue-route.png)

7. **[保存]** を選択します。 [ルート] ペインに戻ると、新しいルートが両方とも表示されます。

   ![設定したばかりのルート](./media/tutorial-routing/08-show-both-routes.png)

8. **[カスタム エンドポイント]** タブを選択することで、設定したカスタム エンドポイントを確認できます。

   ![設定したばかりのカスタム エンドポイント](./media/tutorial-routing/09-show-custom-endpoints.png)

9. [メッセージ ルーティング] ウィンドウを閉じて、[リソース グループ] ウィンドウに戻ります。

## <a name="create-a-simulated-device"></a>シミュレート対象デバイスを作成します

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>次のステップ

リソースを設定してメッセージ ルートを構成したら、次のチュートリアルに進んで、IoT ハブにメッセージを送信する方法と、それらがさまざまな宛先にルーティングされるようすを確認しましょう。 

> [!div class="nextstepaction"]
> [パート 2 - メッセージ ルーティング結果の表示](tutorial-routing-view-message-routing-results.md)
