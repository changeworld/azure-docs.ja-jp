---
title: Azure Blob ストレージ イベントをカスタム Web エンドポイントにルーティングする - Powershell | Microsoft Docs
description: Blob Storage のイベントをサブスクライブするには、Azure Event Grid を使用します。
services: storage,event-grid
author: david-stanford
ms.author: dastanfo
ms.date: 07/05/2018
ms.topic: article
ms.service: storage
ms.component: blobs
ms.openlocfilehash: 502f378bd1eddc0a104438037dce50bafd508ad9
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143004"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>PowerShell を使って Blob Storage のイベントをカスタム Web エンドポイントにルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure PowerShell を使用して Blob Storage のイベントをサブスクライブし、イベントをトリガーして結果を表示します。 

通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、この記事では、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

最後に、イベント データが Web アプリに送信されたことを確認します。

![結果の表示](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>セットアップ

この記事では、Azure PowerShell の最新バージョンを実行していることを前提にしています。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzureRmAccount
```

この例では、**westus2** を使って、全体で使用される 1 つの変数に選択肢を格納しています。

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。

次の例では、**gridResourceGroup** という名前のリソース グループを **westus2** の場所に作成します。  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Blob ストレージ イベントを使用するには、[Blob ストレージ アカウント](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)または[汎用 v2 ストレージ アカウント](../common/storage-account-options.md#general-purpose-v2-accounts)のどちらかが必要です。 **汎用 v2 (GPv2)** は、BLOB、Files、Queues、Tables をはじめとする全ストレージ サービスに関して、すべての機能をサポートするストレージ アカウントです。 **BLOB ストレージ アカウント**とは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 Blob Storage アカウントは、汎用ストレージ アカウントと同様に、現在使われているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。  

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を使って LRS レプリケーションで Blob Storage アカウントを作成し、使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントで作業するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。 この例では、ローカル冗長ストレージ(LRS) を使って、**gridstorage** と呼ばれるストレージ アカウントを作成します。 

> [!NOTE]
> このスクリプトで提供されている名前に数個のランダムな文字を追加する必要があるので、ストレージ アカウント名はグローバル名前空間にあります。

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/dbarkol/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

`<your-site-name>` は、Web アプリの一意の名前に置き換えてください。 Web アプリ名は、DNS エントリの一部であるため、一意である必要があります。

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

現在表示されているメッセージがないサイトが表示されます。

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>ストレージ アカウントをサブスクライブする

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では、作成したストレージ アカウントをサブスクライブし、Web アプリからの URL をイベント通知のエンドポイントとして渡します。 Web アプリのエンドポイントには、サフィックス `/api/updates/` が含まれている必要があります。

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

![サブスクリプション イベントの表示](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Blob Storage からのイベントのトリガー

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 最初に、コンテナーとオブジェクトを作成しましょう。 次に、コンテナーにオブジェクトをアップロードしましょう。

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、送信したイベント確認します。

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
引き続きこのストレージ アカウントとイベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

トピックを作成し、イベントをサブスクライブする方法がわかったら、Blob Storage のイベントについて、また Event Grid でできることについて、さらに情報を収集しましょう。

- [Blob Storage のイベント処理](storage-blob-event-overview.md)
- [Event Grid について](../../event-grid/overview.md)
