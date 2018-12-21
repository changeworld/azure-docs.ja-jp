---
title: Azure IoT Hub を使用してメッセージ ルーティングを構成する (.NET) | Microsoft Docs
description: Azure IoT Hub を使用してメッセージ ルーティングを構成します
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6f1cd08e3c786a1d163a22b5da5150fde5f45b95
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135340"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>チュートリアル:IoT Hub を使用してメッセージ ルーティングを構成する

[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)を使うと、IoT デバイスから組み込みイベント ハブ互換エンドポイントまたはカスタム エンドポイント (BLOB ストレージ、Service Bus キュー、Service Bus トピック、イベント ハブなど) に、利用統計情報を送信できます。 メッセージ ルーティングの構成中に、特定の条件と一致するルートをカスタマイズするための[ルーティング クエリ](iot-hub-devguide-routing-query-syntax.md)を作成できます。 設定が済むと、受信データは IoT Hub によってエンドポイントに自動的にルーティングされるようになります。 

このチュートリアルでは、IoT Hub を使用してルーティング クエリを設定および使用する方法を説明します。 IoT デバイスから Blob ストレージや Service Bus キューなどの複数のサービスのいずれかに、メッセージをルーティングします。 Service Bus キューへのメッセージは、ロジック アプリによって取得されて、メールで送信されます。 ルーティングが明示的に設定されていないメッセージは、既定のエンドポイントに送信され、Power BI の視覚エフェクトに表示されます。

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * Azure CLI または PowerShell を使って、基本リソース (IoT ハブ、ストレージ アカウント、Service Bus キュー、シミュレートされたデバイス) を設定します。
> * ストレージ アカウントおよび Service Bus キューに対するエンドポイントとルートを IoT hub で構成します。
> * メッセージが Service Bus キューに追加されるとトリガーされてメールを送信するロジック アプリを作成します。
> * 異なるルーティング オプションでハブにメッセージを送信する IoT デバイスをシミュレートするアプリをダウンロードして実行します。
> * 既定のエンドポイントに送信されたデータに対する Power BI の視覚エフェクトを作成します。
> * 結果を表示します ...
> * ... Service Bus キューおよびメール内の結果。
> * ... ストレージ アカウント内の結果。
> * ... Power BI の視覚エフェクト内の結果。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- [Visual Studio](https://www.visualstudio.com/) のインストール。 

- 既定のエンドポイントの Stream Analytics を分析するための Power BI アカウント。 ([Power BI を無料で試す](https://app.powerbi.com/signupredirect?pbi_source=web))

- 通知メールを送信するための Office 365 アカウント。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>リソースを設定する

このチュートリアルでは、IoT ハブ、ストレージ アカウント、および Service Bus キューが必要です。 これらのリソースは、Azure CLI または Azure PowerShell を使って作成できます。 すべてのリソースに同じリソース グループと場所を使います。 最後に、リソース グループを削除することによって、すべてのものを一度に削除できます。

以下のセクションでは、これらの必要な手順の実行方法を説明します。 CLI "*または*" PowerShell の手順に従ってください。

1. [リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 

2. S1 レベルに IoT ハブを作成します。 コンシューマー グループを IoT ハブに追加します。 コンシューマー グループは、データを取得するときに Azure Stream Analytics によって使われます。

   > [!NOTE]
   > このチュートリアルを完了するには、有料レベルで IoT ハブを使用する必要があります。 無料レベルで設定できるのは 1 つのエンドポイントのみです。このチュートリアルでは複数のエンドポイントが必要です。
   > 

3. Standard_LRS レプリケーションで Standard V1 ストレージ アカウントを作成します。

4. Service Bus の名前空間とキューを作成します。 

5. ハブにメッセージを送信するシミュレートされたデバイスのデバイス ID を作成します。 テスト フェーズ用にキーを保存します。

### <a name="set-up-your-resources-using-azure-cli"></a>Azure CLI を使用してリソースを設定する

次のスクリプトをコピーして Cloud Shell に貼り付けます。 既にログインしているものとすると、スクリプトが 1 行ずつ実行されます。 

グローバルに一意でなければならない変数には `$RANDOM` が連結されています。 スクリプトが実行され、変数が設定されるときに、ランダムな数値文字列が生成され、固定文字列の末尾に連結されて一意の変数を作ります。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="set-up-your-resources-using-azure-powershell"></a>Azure PowerShell を使用してリソースを設定する

次のスクリプトをコピーして Cloud Shell に貼り付けます。 既にログインしているものとすると、スクリプトが 1 行ずつ実行されます。

グローバルに一意でなければならない変数には `$(Get-Random)` が連結されています。 スクリプトが実行され、変数が設定されるときに、ランダムな数値文字列が生成され、固定文字列の末尾に連結されて一意の変数を作ります。

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

次に、デバイス ID を作成し、後で使用するのでそのキーを保存します。 このデバイス ID は、IoT ハブにメッセージを送信するためにシミュレーション アプリケーションによって使われます。 この機能は PowerShell では使用できませんが、[Azure portal](https://portal.azure.com) でデバイスを作成できます。

1. [Azure portal](https://portal.azure.com) を開き、Azure アカウントにログインします。

2. **[リソース グループ]** をクリックして、リソース グループを選びます。 このチュートリアルでは、**ContosoResources** を使います。

3. リソースの一覧で、お使いの IoT ハブをクリックします。 このチュートリアルでは、**ContosoTestHub** を使います。 [ハブ] ウィンドウで **[IoT デバイス]** を選びます。

4. **[+ 追加]** をクリックします。 [デバイスの追加] ウィンドウで、デバイス ID を入力します。 このチュートリアルでは、**Contoso-Test-Device** を使います。 キーは空のままにし、**[キーの自動生成]** をオンにします。 **[デバイスを IoT Hub に接続]** を有効にします。 **[Save]** をクリックします。

   ![[デバイスの追加] 画面のスクリーンショット。](./media/tutorial-routing/add-device.png)

5. デバイスが作成されたので、デバイスをクリックして生成されたキーを表示します。 プライマリ キーの [コピー] アイコンをクリックし、このチュートリアルのテスト フェーズのために、メモ帳などの場所に保存します。

   ![キーを含む、デバイスの詳細を示すスクリーンショット。](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>メッセージ ルーティングを設定する

シミュレートされたデバイスによってメッセージに添付されたプロパティに基づいて、メッセージを異なるリソースにルーティングします。 カスタム ルーティングされないメッセージは、既定のエンドポイント (メッセージ/イベント) に送信されます。 

|value |結果|
|------|------|
|level="storage" |Azure Storage に書き込みます。|
|level="critical" |Service Bus キューに書き込みます。 ロジック アプリがキューからメッセージを取得し、Office 365 を使ってメールでメッセージを送信します。|
|default |Power BI を使ってこのデータを表示します。|

### <a name="routing-to-a-storage-account"></a>ストレージ アカウントへのルーティング 

ストレージ アカウントへのルーティングを設定します。 [メッセージ ルーティング] ウィンドウに移動して、ルートを追加します。 ルートを追加するときは、ルートに対する新しいエンドポイントを定義します。 これを設定した後、**level** プロパティが **storage** に設定されているメッセージは、ストレージ アカウントに自動的に書き込まれます。 

データは BLOB ストレージに Avro 形式で書き込まれます。

1. [Azure portal](https://portal.azure.com) で **[リソース グループ]** をクリックし、使うリソース グループを選びます。 このチュートリアルでは、**ContosoResources** を使います。 

2. リソース一覧の下で IoT ハブをクリックします。 このチュートリアルでは、**ContosoTestHub** を使います。 

3. **[メッセージ ルーティング]** をクリックします。 **[メッセージ ルーティング]** ウィンドウで、**[+ 追加]** をクリックします。 次の図に示すように、**[Add a Route]\(ルートの追加\)** ウィンドウで、[エンドポイント] フィールドの隣にある **[+ 追加]** をクリックします。

   ![ルートへのエンドポイントの追加を開始する方法を示すスクリーンショット。](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. **[Blob ストレージ]** を選択します。 **[Add Storage Endpoint]\(ストレージ エンドポイントの追加\)** ウィンドウが表示されます。 

   ![エンドポイントの追加を示すスクリーンショット。](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. エンドポイントの名前を入力します。 このチュートリアルでは、**StorageContainer** を使います。

6. **[コンテナーを選択します]** をクリックします。 ストレージ アカウントの一覧が表示されます。 準備ステップで設定したものを選択します。 このチュートリアルでは、**contosostorage** を使います。 そのストレージ アカウント内のコンテナーの一覧が表示されます。 準備ステップで設定したコンテナーを選択します。 このチュートリアルでは、**contosoresults** を使います。 **[選択]** をクリックします。 **[エンドポイントの追加]** ウィンドウに表示が戻ります。 

7. このチュートリアルでは、残りのフィールドには既定値を使用します。 

   > [!NOTE]
   > BLOB 名の形式は、**[BLOB ファイル名形式]** を使用して設定することができます。 既定では、 `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`です。 形式には任意の順序で {iothub}、{partition}、{YYYY}、{MM}、{DD}、{HH}、および {mm} を含める必要があります。 
   > 
   > たとえば、既定の BLOB ファイル名形式を使用して、ハブ名が ContosoTestHub であり、日付/時刻が 2018 年 10 月 30 日午前 10:56 である場合、BLOB 名は `ContosoTestHub/0/2018/10/30/10/56` のようになります。
   > 
   > BLOB は、Avro 形式で書き込まれます。
   >

8. **[作成]** をクリックしてストレージ エンドポイントを作成し、ルートに追加します。 **[Add a route]\(ルートの追加\)** ウィンドウに表示が戻ります。

9. ルーティング クエリ情報の残りの部分を完成します。 このクエリでは、エンドポイントとして追加したストレージ コンテナーにメッセージを送信するための条件を指定します。 画面のフィールドを入力します。 

   **[名前]**:ルーティング クエリの名前を入力します。 このチュートリアルでは、「**StorageRoute**」を使います。

   **エンドポイント**:ここには、先ほど設定したエンドポイントが表示されます。 
   
   **データ ソース**:ドロップダウン リストから **[デバイス テレメトリのメッセージ]** を選びます。

   **ルートの有効化**:これは必ず有効にします。
   
   **ルーティング クエリ**:クエリ文字列として、「`level="storage"`」と入力します。 

   ![ストレージ アカウントのルーティング クエリの作成を示すスクリーンショット。](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  
   
   **[Save]** をクリックします。 完了すると [メッセージ ルーティング] ウィンドウに表示が戻り、そこでストレージの新しいルーティング クエリを確認できます。 [ルート] ウィンドウを閉じて、[リソース グループ] ページに戻ります。

### <a name="routing-to-a-service-bus-queue"></a>Service Bus キューへのルーティング 

次に、Service Bus キューへのルーティングを設定します。 [メッセージ ルーティング] ウィンドウに移動して、ルートを追加します。 ルートを追加するときは、ルートに対する新しいエンドポイントを定義します。 これを設定した後、**level** プロパティが **critical** に設定されているメッセージは、Service Bus キューに書き込まれます。それにより、ロジック アプリがトリガーされて、情報を含むメールが送信されます。 

1. [リソース グループ] ページで、お使いの IoT ハブをクリックし、**[メッセージ ルーティング]** をクリックします。 

2. **[メッセージ ルーティング]** ウィンドウで、**[+ 追加]** をクリックします。 

3. **[Add a Route]\(ルートの追加\)** ウィンドウで、[エンドポイント] フィールドの隣にある **[+ 追加]** をクリックします。 **[Service Bus キュー]** を選択します。 **[Service Bus エンドポイントを追加する]** ウィンドウが表示されます。 

   ![Service Bus エンドポイントの追加のスクリーンショット](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. フィールドに入力します。

   **エンドポイント名**:エンドポイントの名前を入力します。 このチュートリアルでは、**CriticalQueue** を使います。
   
   **Service Bus 名前空間**:このフィールドをクリックしてドロップダウン リストを表示し、準備ステップで設定したサービス バスの名前空間を選択します。 このチュートリアルでは、**ContosoSBNamespace** を使います。

   **Service Bus キュー**:このフィールドをクリックしてドロップダウン リストを表示し、そこから Service Bus キューを選択します。 このチュートリアルでは、**contososbqueue** を使います。

5. **[作成]** をクリックして Service Bus キュー エンドポイントを追加します。 **[Add a route]\(ルートの追加\)** ウィンドウに表示が戻ります。 

6.  ルーティング クエリ情報の残りの部分を完成します。 このクエリでは、エンドポイントとして追加した Service Bus キューにメッセージを送信するための条件を指定します。 画面のフィールドを入力します。 

   **[名前]**:ルーティング クエリの名前を入力します。 このチュートリアルでは、**SBQueueRoute** を使います。 

   **エンドポイント**:ここには、先ほど設定したエンドポイントが表示されます。

   **データ ソース**:ドロップダウン リストから **[デバイス テレメトリのメッセージ]** を選びます。

   **ルーティング クエリ**:クエリ文字列として、「`level="critical"`」と入力します。 

   ![Service Bus キューのルーティング クエリの作成を示すスクリーンショット。](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. **[Save]** をクリックします。 [ルート] ウィンドウに戻ると、次のように、新しいルートが両方とも表示されます。

   ![設定したルートを示すスクリーンショット。](./media/tutorial-routing/message-routing-show-both-routes.png)

8. **[カスタム エンドポイント]** タブをクリックすることで、設定したカスタム エンドポイントを確認できます。

   ![設定したカスタム エンドポイントを示すスクリーンショット。](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. [メッセージ ルーティング] ウィンドウを閉じて、[リソース グループ] ウィンドウに戻ります。

## <a name="create-a-logic-app"></a>ロジック アプリの作成  

Service Bus キューは、critical と指定されているメッセージを受信するために使います。 Service Bus キューを監視してメッセージがキューに追加されたらメールを送信する、ロジック アプリを設定します。 

1. [Azure portal](https://portal.azure.com) で、**[+ リソースの作成]** をクリックします。 検索ボックスに「**ロジック アプリ**」と入力して、Enter キーを押します。 表示される検索結果で、ロジック アプリを選び、**[作成]** をクリックして **[ロジック アプリの作成]** ウィンドウに進みます。 フィールドに入力します。 

   **[名前]**:このフィールドはロジック アプリの名前です。 このチュートリアルでは、**ContosoLogicApp** を使います。 

   **サブスクリプション**:Azure サブスクリプションを選択します。

   **リソース グループ**:**[既存のものを使用]** をクリックして、リソース グループを選びます。 このチュートリアルでは、**ContosoResources** を使います。 

   **場所**:自分の場所を使用します。 このチュートリアルでは、**[米国西部]** を使います。 

   **Log Analytics**:このトグルはオフにする必要があります。 

   ![[ロジック アプリの作成] 画面のスクリーンショット。](./media/tutorial-routing/create-logic-app.png)

   **Create** をクリックしてください。

2. ロジック アプリに移動します。 ロジック アプリに最も簡単に移動するには、**[リソース グループ]** をクリックし、お使いのリソース グループ (このチュートリアルでは **ContosoResources** を使っています) を選び、リソースの一覧からロジック アプリを選びます。 [Logic Apps デザイナー] ページが表示されます (ページ全体を見るには右にスクロールする必要がある場合があります)。 [Logic Apps デザイナー] ページで、下にスクロールして **[空のロジック アプリ +]** タイルをクリックします。 

3. コネクタの一覧が表示されます。 **[Service Bus]** を選びます。 

   ![コネクタの一覧を示すスクリーンショット。](./media/tutorial-routing/logic-app-connectors.png)

4. トリガーの一覧が表示されます。 **[Service Bus - メッセージがキューに着信したとき (オート コンプリート)]** を選びます。 

   ![Service Bus のトリガーの一覧を示すスクリーンショット。](./media/tutorial-routing/logic-app-triggers.png)

5. 次の画面で、[接続名] を入力します。 このチュートリアルでは、**ContosoConnection** を使います。 

   ![Service Bus キューの接続の設定を示すスクリーンショット。](./media/tutorial-routing/logic-app-define-connection.png)

   Service Bus の名前空間をクリックします。 このチュートリアルでは、**ContosoSBNamespace** を使います。 名前空間を選ぶと、ポータルは Service Bus 名前空間を照会してキーを取得します。 **RootManageSharedAccessKey** を選び、**[作成]** をクリックします。 
   
   ![接続設定の終了を示すスクリーンショット。](./media/tutorial-routing/logic-app-finish-connection.png)

6. 次の画面で、ドロップダウン リストからキューの名前を選びます (このチュートリアルでは **contososbqueue**)。 残りのフィールドは既定値のままでかまいません。 

   ![キューのオプションを示すスクリーンショット。](./media/tutorial-routing/logic-app-queue-options.png)

7. 次に、キューでメッセージを受け取ったときにメールを送信するアクションを設定します。 Logic Apps デザイナーで、**[+ 新しいステップ]** をクリックしてステップを追加し、**[アクションの追加]** をクリックします。 **[アクションの選択]** ウィンドウで、**[Office 365 Outlook]** を探してクリックします。 トリガー画面で、**[Office 365 Outlook - メールの送信]** を選びます。  

   ![Office365 のオプションを示すスクリーンショット。](./media/tutorial-routing/logic-app-select-outlook.png)

8. 次に、Office 365 アカウントにログインして、接続を設定します。 メールの受信者のメール アドレスを指定します。 また、件名を指定し、受信者が見るメッセージ本文を入力します。 テストでは、受信者として自分のメール アドレスを入力します。

   **[動的なコンテンツの追加]** をクリックして、含めることができるメッセージの内容を表示します。 **[コンテンツ]** を選びます。メッセージがメールに追加されます。 

   ![ロジック アプリのメール オプションを示すスクリーンショット。](./media/tutorial-routing/logic-app-send-email.png)

9. **[Save]** をクリックします。 Logic Apps デザイナーを閉じます。

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics を設定する

Power BI の視覚エフェクトにデータを表示するには、最初に、データを取得するように Stream Analytics ジョブを設定します。 既定のエンドポイントに送信されるのは **level** が **normal** のメッセージだけであり、メッセージは Power BI の視覚エフェクトのために Stream Analytics ジョブによって取得されることに注意してください。

### <a name="create-the-stream-analytics-job"></a>Stream Analytics ジョブを作成する

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[モノのインターネット]** > **[Stream Analytics ジョブ]** の順にクリックします。

2. 次の情報をジョブに入力します。

   **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。 このチュートリアルでは、**contosoJob** を使います。

   **リソース グループ**:IoT ハブで使用されるのと同じリソース グループを使います。 このチュートリアルでは、**ContosoResources** を使います。 

   **場所**:セットアップ スクリプトで使用されるのと同じ場所を使います。 このチュートリアルでは、**[米国西部]** を使います。 

   ![Stream Analytics ジョブの作成方法を示すスクリーンショット。](./media/tutorial-routing/stream-analytics-create-job.png)

3. **[作成]** をクリックしてジョブを作成します。 ジョブに戻るには、**[リソース グループ]** をクリックします。 このチュートリアルでは、**ContosoResources** を使います。 リソース グループを選択し、リソースの一覧で Stream Analytics ジョブをクリックします。 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

4. **[ジョブ トポロジ]** で **[入力]** をクリックします。

5. **[入力]** ウィンドウで、**[ストリーム入力の追加]** をクリックして [IoT Hub] を選びます。 表示される画面で、次のフィールドを入力します。

   **入力のエイリアス**:このチュートリアルでは、**contosoinputs** を使います。

   **サブスクリプション**:サブスクリプションを選択します。

   **IoT Hub**:IoT ハブを選びます。 このチュートリアルでは、**ContosoTestHub** を使います。

   **エンドポイント**:**[メッセージング]** を選びます。 ([操作の監視] を選ぶと、チュートリアルで送信しているデータではなく、IoT ハブに関する利用統計情報が取得されます)。 

   **共有アクセス ポリシー名**:**[iothubowner]** を選びます。 [共有アクセス ポリシー キー] はポータルによって自動的に設定されます。

   **コンシューマー グループ**:前に作成したコンシューマー グループを選びます。 このチュートリアルでは、**contosoconsumers** を使います。
   
   その他のフィールドについては、既定値を指定できます。 

   ![Stream Analytics ジョブの入力の設定方法を示すスクリーンショット。](./media/tutorial-routing/stream-analytics-job-inputs.png)

6. **[Save]** をクリックします。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で **[出力]** をクリックします。

2. **[出力]** ウィンドウで **[追加]** をクリックし、**[Power BI]** を選びます。 表示される画面で、次のフィールドを入力します。

   **出力のエイリアス**:出力の一意のエイリアス。 このチュートリアルでは、**contosooutputs** を使います。 

   **データセット名**:Power BI で使用されるデータセットの名前。 このチュートリアルでは、**contosodataset** を使います。 

   **テーブル名**:Power BI で使用されるテーブルの名前。 このチュートリアルでは、**contosotable** を使います。

   その他のフィールドについては、既定値を指定できます。

3. **[承認]** をクリックして、Power BI アカウントにサインインします。

   ![Stream Analytics ジョブの出力の設定方法を示すスクリーンショット。](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. **[Save]** をクリックします。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で **[クエリ]** をクリックします。

2. `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。 このチュートリアルでは、**contosoinputs** を使います。

3. `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。 このチュートリアルでは、**contosooutputs** を使います。

   ![Stream Analytics ジョブのクエリの設定方法を示すスクリーンショット。](./media/tutorial-routing/stream-analytics-job-query.png)

4. **[Save]** をクリックします。

5. [クエリ] ウィンドウを閉じます。 [リソース グループ] のリソースの表示に戻ります。 Stream Analytics ジョブをクリックします。 このチュートリアルでは **contosoJob** という名前です。

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、**[開始]** > **[現在]** > **[開始]** の順にクリックします。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

Power BI レポートを設定するにはデータが必要なので、デバイスを作成し、デバイス シミュレーション アプリケーションを実行した後で、Power BI を設定します。

## <a name="run-simulated-device-app"></a>シミュレートされたデバイス アプリを実行する

スクリプト設定セクションの前半では、IoT デバイスを使ってシミュレートするようにデバイスを設定しました。 このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする .NET コンソール アプリをダウンロードします。 このアプリケーションは、異なるルーティング方法ごとにメッセージを送信します。 

[IoT デバイス シミュレーション](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)のソリューションをダウンロードします。 これにより、複数のアプリケーションを含むリポジトリがダウンロードされます。探しているソリューションは、iot-hub/Tutorials/Routing/SimulatedDevice/ にあります。

ソリューション ファイル (SimulatedDevice.sln) をダブルクリックしてコードを Visual Studio で開いた後、Program.cs を開きます。 `{iot hub hostname}` を、IoT ハブのホスト名で置き換えます。 IoT ハブのホスト名の形式は、**{iot-hub-name}.azure-devices.net** です。 このチュートリアルでのハブのホスト名は、**ContosoTestHub.azure-devices.net** です。 次に、`{device key}` を、シミュレートされたデバイスを設定するときに保存したデバイス キーに置き換えます。 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>実行してテストする 

コンソール アプリケーションを実行します。 数分待ちます。 アプリケーションのコンソール画面で、メッセージが送信されていることを確認できます。

アプリは、1 秒おきにデバイスからクラウドへの新しいメッセージを IoT ハブに送信します。 メッセージには、デバイス ID、温度、湿度、およびメッセージ レベル (既定値は `normal`) を含む、JSON でシリアル化されたオブジェクトが含まれます。 アプリはランダムにレベル `critical` または `storage` を割り当てるので、メッセージはストレージ アカウントまたは Service Bus キュー (ロジック アプリによるメールの送信がトリガーされます) にルーティングされます。 既定値 (`normal`) は、次に設定する BI レポートに表示されます。

すべてが正しく設定されている場合、この時点で次の結果が表示されるはずです。

1. 重大メッセージに関するメールの受信が始まります。 

   ![結果のメールを示すスクリーンショット。](./media/tutorial-routing/results-in-email.png)

   これは次のことを意味します。

   * Service Bus キューへのルーティングは、正常に動作しています。
   * Service Bus キューからメッセージを取得するロジック アプリは、正常に動作しています。
   * Outlook へのロジック アプリ コネクタは、正常に動作しています。 

2. [Azure portal](https://portal.azure.com) で **[リソース グループ]** をクリックし、使うリソース グループを選びます。 このチュートリアルでは、**ContosoResources** を使います。 ストレージ アカウントを選び、**[BLOB]** をクリックして、コンテナーを選びます。 このチュートリアルでは、**contosoresults** を使います。 フォルダーが表示され、ファイルが表示されるまでディレクトリをドリルダウンできます。 ファイルの 1 つを開くと、ストレージ アカウントにルーティングされたエントリが含まれています。 

   ![ストレージの結果ファイルを示すスクリーンショット。](./media/tutorial-routing/results-in-storage.png)

これは次のことを意味します。

   * ストレージ アカウントへのルーティングは、正常に動作しています。

アプリケーションをまだ実行したまま、既定のルーティングで送られたメッセージを表示するように、Power BI の視覚エフェクトを設定します。 

## <a name="set-up-the-power-bi-visualizations"></a>Power BI の視覚エフェクトを設定する

1. [Power BI](https://powerbi.microsoft.com/) アカウントにサインインします。

2. **[ワークスペース]** に移動し、Stream Analytics ジョブの出力を作成するときに設定したワークスペースを選びます。 このチュートリアルでは、**マイ ワークスペース**を使います。 

3. **[データセット]** をクリックします。

   Stream Analytics ジョブの出力を作成したときに指定したデータセットが一覧で表示されます。 このチュートリアルでは、**contosodataset** を使います。 (データセットが初めて表示されるまでに、5 ～ 10 分かかる場合があります。)

4. **[アクション]** の左端のアイコンをクリックし、レポートを作成します。

   ![アクションとレポートのアイコンが強調表示されている Power BI ワークスペースを示すスクリーンショット。](./media/tutorial-routing/power-bi-actions.png)

5. 時間の経過に伴う温度の変化を示す折れ線グラフを作成します。

   * レポート作成ページで、折れ線グラフ アイコンをクリックして折れ線グラフを追加します。

   ![視覚エフェクトとフィールドを示すスクリーンショット。](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   * **[フィールド]** ウィンドウで、Stream Analytics ジョブの出力を作成したときに指定したテーブルを展開します。 このチュートリアルでは、**contosotable** を使います。

   * **EventEnqueuedUtcTime** を、**[視覚化]** ウィンドウの **[軸]** にドラッグします。

   * **temperature** を **[値]** にドラッグします。

   折れ線グラフが作成されます。 x 軸は日付と時刻 (UTC タイム ゾーン) を示し、 y 軸はセンサーから取得した温度を示します。

6. 時間の経過に伴う湿度の変化を示す、別の折れ線グラフを作成します。 2 番目のグラフを設定するには、上記と同じ手順を行ったうえで、**EventEnqueuedUtcTime** を x 軸、**humidity** を y 軸に設定します。

   ![2 つのグラフを含む最終的な Power BI レポートを示すスクリーンショット。](./media/tutorial-routing/power-bi-report.png)

7. **[保存]** をクリックしてレポートを保存します。

両方のグラフにデータを表示できます。 これは次のことを意味します。

   * 既定のエンドポイントへのルーティングは、正常に動作しています。
   * Azure Stream Analytics ジョブは、正常にストリーミングしています。
   * Power BI の視覚エフェクトは、正しく設定されています。

Power BI ウィンドウの上部にある [最新の情報に更新] ボタンをクリックすることで、グラフを更新して最新のデータを表示できます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

作成したリソースをすべて削除する場合は、リソース グループを削除します。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この例では、IoT ハブ、Service Bus の名前空間とキュー、ロジック アプリ、ストレージ アカウント、およびリソース グループ自体が削除されます。 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Power BI の視覚エフェクトのリソースをクリーンアップする

[Power BI](https://powerbi.microsoft.com/) アカウントにログインします。 ワークスペースに移動します。 このチュートリアルでは、**マイ ワークスペース**を使います。 Power BI の視覚エフェクトを削除するには、[データセット] に移動し、ごみ箱アイコンをクリックしてデータセットを削除します。 このチュートリアルでは、**contosodataset** を使います。 データセットを削除すると、レポートも削除されます。

### <a name="clean-up-resources-using-azure-cli"></a>Azure CLI を使用してリソースをクリーンアップする

リソース グループを削除するには、[az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使います。

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>PowerShell を使用してリソースをクリーンアップする

リソース グループを削除するには、 [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使います。 $resourceGroup は、このチュートリアルの開始時に **ContosoIoTRG1** に設定されていました。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下のタスクを実行することにより、メッセージ ルーティングを使って IoT Hub メッセージをさまざまな宛先にルーティングする方法を学習しました。  

> [!div class="checklist"]
> * Azure CLI または PowerShell を使って、基本リソース (IoT ハブ、ストレージ アカウント、Service Bus キュー、シミュレートされたデバイス) を設定します。
> * ストレージ アカウントおよび Service Bus キューに対するエンドポイントとルートを IoT hub で構成します。
> * メッセージが Service Bus キューに追加されるとトリガーされてメールを送信するロジック アプリを作成します。
> * 異なるルーティング オプションでハブにメッセージを送信する IoT デバイスをシミュレートするアプリをダウンロードして実行します。
> * 既定のエンドポイントに送信されたデータに対する Power BI の視覚エフェクトを作成します。
> * 結果を表示します ...
> * ... Service Bus キューおよびメール内の結果。
> * ... ストレージ アカウント内の結果。
> * ... Power BI の視覚エフェクト内の結果。

次のチュートリアルに進み、IoT デバイスの状態を管理する方法を学習してください。 

> [!div class="nextstepaction"]
[バックエンド サービスからデバイスを構成する](tutorial-device-twins.md)
