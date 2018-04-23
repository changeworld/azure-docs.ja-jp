---
title: Azure Blob ストレージ イベントをカスタム Web エンドポイントにルーティングする - Powershell | Microsoft Docs
description: Blob Storage のイベントをサブスクライブするには、Azure Event Grid を使用します。
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 6d7ccd94243d7064008197518f6194d5837b17be
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>PowerShell を使って Blob Storage のイベントをカスタム Web エンドポイントにルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure PowerShell を使用して Blob Storage のイベントをサブスクライブし、イベントをトリガーして結果を表示します。 

通常、webhook や Azure 関数など、イベントに応答するエンドポイントが、イベントの送信先になります。 この記事では、紹介している例を単純化するために、メッセージをただ収集するだけの URL に対してイベントを送信します。 この URL は、サード パーティ製ツール ([Hookbin](https://hookbin.com/)) を使用して作成します。

> [!NOTE]
> **Hookbin** は、高スループットでの使用を目的としていません。 ここでは、デモンストレーションのためにこのツールを使用します。 一度に複数のイベントをプッシュすると、一部のイベントがツールから見えない場合があります。

この記事の手順の最後に、イベント データがエンドポイントに送信済みであることを確認できます。

## <a name="setup"></a>セットアップ

この記事では、Azure PowerShell の最新バージョンを実行していることを前提にしています。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Storage イベントの可用性は、Event Grid の[可用性](../../event-grid/overview.md)と関連付けられており、Event Grid の場合と同様に、他のリージョンで使用可能になります。

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

Blob ストレージ イベントを使用するには、[Blob ストレージ アカウント](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)または[汎用 v2 ストレージ アカウント](../common/storage-account-options.md#general-purpose-v2)のどちらかが必要です。 **汎用 v2 (GPv2)** は、BLOB、Files、Queues、Tables をはじめとする全ストレージ サービスに関して、すべての機能をサポートするストレージ アカウントです。 **BLOB ストレージ アカウント**とは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 Blob Storage アカウントは、汎用ストレージ アカウントと同様に、現在使われているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。  

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

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 ここではイベントに応答するコードを作成する代わりに、皆さんが確認できるように、メッセージを収集するエンドポイントを作成します。 Hookbin は、エンドポイントを作成し、そこに送信された要求を表示できるサードパーティ ツールです。 [Hookbin](https://hookbin.com/) に移動し、**[新しいエンドポイントの作成]** をクリックします。 bin の URL をコピーして、次のスクリプトの `<bin URL>` を置き換えます。

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>ストレージ アカウントをサブスクライブする

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では、作成したストレージ アカウントをサブスクライブし、Hookbin からの URL をイベント通知のエンドポイントとして渡します。 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Blob Storage からのイベントのトリガー

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 最初に、コンテナーとオブジェクトを作成しましょう。 次に、コンテナーにオブジェクトをアップロードしましょう。

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 先ほど作成したエンドポイント URL に移動します。 または、開いているブラウザーで [更新] をクリックします。 先ほど送信したイベントが表示されます。 

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
