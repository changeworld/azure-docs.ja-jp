---
title: "共有スナップショット (プレビュー) を操作する| Microsoft Docs"
description: "共有スナップショットは、Azure Files 共有の読み取り専用バージョンであり、共有をバックアップする手段として特定の時点で取得されます。"
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>共有スナップショット (プレビュー) を操作する
共有スナップショット (プレビュー) は、ある時点で取得された Azure Files 共有の読み取り専用バージョンです。 作成された共有スナップショットは、読み取り、コピー、削除はできますが、変更はできません。 共有スナップショットは、共有のある時点の内容をそのままバックアップするための手段を提供します。 

この記事では、共有スナップショットを作成、管理、削除する方法について説明します。 詳細については、[共有スナップショットの概要](storage-snapshots-files.md)または[スナップショットのよく寄せられる質問](storage-files-faq.md)に関する記事を参照してください。

## <a name="create-a-share-snapshot"></a>共有スナップショットの作成

共有スナップショットは、Azure ポータル、PowerShell、Azure CLI、REST API、または任意の Azure Storage SDK を使用して作成できます。 次のセクションでは、ポータル、CLI、および Powershell を使用して共有スナップショットを作成する方法を説明します。 

ファイル共有を使用中に、該当のファイル共有の共有スナップショットを取得できます。 ただし、共有スナップショットでは、共有スナップショット コマンドが実行された時点でファイル共有に既に書き込まれていたデータのみが取得されます。 このため、任意のアプリケーションやオペレーション システムでキャッシュされたデータが除外される可能性があります。

### <a name="create-a-share-snapshot-by-using-the-portal"></a>ポータルを使用して共有スナップショットを作成する  
ある時点の共有スナップショットを作成するには、ポータルでファイル共有に移動し、**[スナップショットの作成]** を選択します。

>   ![ポータルの [スナップショット] メニュー](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Azure CLI 2.0 を使用して共有スナップショットを作成する
次のように、`az storage share snapshot` コマンドを使用して、共有スナップショットを作成できます。

```azurecli-interactive
az storage share snapshot -n <share name>
```

出力例:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>PowerShell を使用して共有スナップショットを作成する
次のように、`$share.Snapshot()` コマンドを使用して、共有スナップショットを作成できます。

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>一般的な共有スナップショット操作の実行

REST、クライアント ライブラリ、PowerShell、およびポータル経由で Windows の **[以前のバージョン]** タブを使用して、ファイル共有に関連付けられた共有スナップショットを列挙できます。 ファイル共有をマウントした後、Windows の **[以前のバージョン]** タブを使用して、ファイルの以前のバージョンをすべて表示できます。 

次のセクションでは、Azure ポータル、Windows、および Azure CLI 2.0 を使用して、共有スナップショットを一覧表示、参照、および復元する方法について説明します。

### <a name="share-snapshot-operations-in-the-portal"></a>ポータルでの共有スナップショットの操作

ポータルでのファイル共有の共有スナップショットをすべて閲覧し、共有スナップショットを参照してその内容を表示できます。

#### <a name="view-a-share-snapshot"></a>共有スナップショットの表示
ファイル共有で、**[スナップショット]** の **[スナップショットの表示]** を選択します。

![ポータルの [スナップショットの表示] コマンド](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>共有スナップショットの内容の一覧表示と参照
共有スナップショットの一覧を表示し、目的のタイムスタンプを選択して、スナップショットの内容を直接参照します。

![タイムスタンプの一覧で選択されたスナップショット](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

スナップショット一覧ビューにある **[接続]** ボタンを選択して、`net use` コマンドと特定の共有スナップショットのディレクトリ パスを取得することもできます。 そのスナップショットを直接参照できます。


![[コマンド] ボックスがある [接続] ウィンドウ](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>共有スナップショットからのダウンロードまたは復元
ポータルで、**[ダウンロード]** または **[復元]** ボタンを使用して、スナップショットからファイルをダウンロードまたは復元します。

![[ダウンロード] ボタンと [復元] ボタン](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Windows でのファイル共有スナップショットの操作
ファイル共有の共有スナップショットを既に取得している場合、Windows 上のマウントされているファイル共有から、共有、ディレクトリ、または特定のファイルの以前のバージョンを表示できます。 たとえば、[以前のバージョン] 機能を使用して、Windows の以前のバージョンのディレクトリを表示して復元します。

> [!Note]  
> 同じ操作を、共有レベルとファイル レベルで実行できます。 該当のディレクトリまたはファイルの変更を含むバージョンのみが、一覧に表示されます。 特定のディレクトリまたはファイルが 2 つの共有スナップショット間で変更されなかった場合、共有スナップショットは、共有レベルの以前のバージョン一覧には表示されますが、ディレクトリまたはファイルの以前のバージョン一覧には表示されません。

#### <a name="mount-a-file-share"></a>ファイル共有をマウントする
最初に、`net use` コマンドを使用してファイル共有をマウントします。

#### <a name="open-a-mounted-share-in-file-explorer"></a>ファイル エクスプローラーで、マウントされた共有を開く
ファイル エクスプローラーに移動し、マウントされた共有を見つけます。

![ファイル エクスプローラーのマウントされた共有](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>以前のバージョンを一覧表示する
復元が必要な項目または親項目を参照します。 ダブルクリックして、目的のディレクトリに移動します。 右クリックして、メニューから **[プロパティ]** を選択します。

![選択したディレクトリの右クリック メニュー](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

**[以前のバージョン]** を選択して、このディレクトリの共有スナップショットの一覧を表示します。 ネットワークの速度とディレクトリ内の共有のスナップショットの数に応じて、一覧の読み込みに数秒かかる場合があります。

![[以前のバージョン] タブ](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

**[開く]** を選択して、特定のスナップショットを開くことができます。 

![開かれたスナップショット](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>以前のバージョンから復元する
共有スナップショット作成時のディレクトリ全体の内容を元の場所に再帰的にコピーするには、**[復元]** を選択します。
 ![警告メッセージ内の [復元] ボタン](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 でのファイル共有スナップショットの操作
Azure CLI 2.0 を使用して、共有スナップショットの一覧表示、共有スナップショットの内容の参照、共有スナップショットからのファイルの復元またはダウンロード、共有スナップショットの削除などの操作を実行できます。

#### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示

`--include-snapshots` と [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) を使用して、特定の共有スナップショットを一覧表示できます。

```azurecli-interactive 
az storage share list --include-snapshots
```

コマンドによって、共有スナップショットの一覧が、関連するすべてのプロパティと共に表示されます。 出力例を次に示します。

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>共有スナップショットの参照
[`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) を使用して、特定の共有スナップショットを参照してその内容を表示できます。 次の例に示すように、参照する共有の名前とタイムスタンプを指定します。

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

この出力では、共有スナップショットのコンテンツが、その共有スナップショットが作成された時点の共有のコンテンツと同じであることがわかります。

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元

共有スナップショットからファイルをコピーまたはダウンロードすることで、ファイルを復元できます。 次の例に示すように、`az storage file download` コマンドを使用します。

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

この出力では、ダウンロードされたファイルの内容とそのプロパティは、共有スナップショットが作成された時点の内容とプロパティと同じであることがわかります。

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-a-share-snapshot"></a>共有スナップショットの削除

Azure ポータル、PowerShell、CLI、REST API、または任意のストレージ SDK を使用して、共有スナップショットを削除できます。 次のセクションでは、Azure ポータル、CLI、および PowerShell を使用して共有スナップショットを削除する方法を説明します。

任意の比較ツールを使用してスナップショットを参照し、2 つのスナップショットの違いを表示できます。 その後、どちらの共有スナップショットを削除するかを決定できます。 

共有スナップショットを保持している共有は削除できません。 共有を削除できるようにするには、すべての共有スナップショットを先に削除しておく必要があります。

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>ポータルを使用して共有スナップショットを削除する  
ポータルで、ファイル共有ブレードに移動し、**[削除]** ボタンを使用して、1 つまたは複数の共有スナップショットを削除できます。

>   ![[削除] ボタン](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Azure CLI 2.0 を使用して共有スナップショットを削除する
`[az storage share delete]` コマンドを使用して、共有スナップショットを削除できます。 次の例では、共有スナップショットのタイムスタンプが `--snapshot '2017-10-04T23:28:35.0000000Z' ` パラメーターで使用されています。

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

出力例:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>PowerShell を使用して共有スナップショットを削除する
`Remove-AzureStorageShare -Share` コマンドを使用して、共有スナップショットを削除できます。

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>次のステップ
* [スナップショットの概要](storage-snapshots-files.md)
* [スナップショットに関してよく寄せられる質問](storage-files-faq.md)
