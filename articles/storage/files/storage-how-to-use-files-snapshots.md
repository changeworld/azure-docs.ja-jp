---
title: "Azure ファイル共有スナップショットの使用方法 (プレビュー) | Microsoft Docs"
description: "Azure ファイル共有スナップショットを使用します。 Azure ファイル共有スナップショットは、ある時点で作成された読み取り専用の Azure ファイル共有です。 作成された共有スナップショットは、読み取り、コピー、削除はできますが、変更はできません。 共有スナップショットを使用すると、共有をある時点での表示内容のままバックアップできます。"
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
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Azure ファイル共有スナップショットの操作 (プレビュー)
Azure ファイル共有スナップショット (プレビュー) は、ある時点で作成された読み取り専用の Azure ファイル共有です。 作成された共有スナップショットは、読み取り、コピー、削除はできますが、変更はできません。 共有スナップショットを使用すると、共有をある時点での表示内容のままバックアップできます。 この記事では、Azure ファイル共有スナップショットを作成、管理、削除する方法について説明します。 共有スナップショットの詳細については、[共有スナップショットの概要](storage-snapshots-files.md)または[スナップショットに関してよく寄せられる質問](storage-files-faq.md)の記事をご覧ください。

## <a name="create-azure-files-share-snapshots"></a>Azure ファイル共有スナップショットの作成

ポータル、Powershell、CLI、REST、または任意のストレージ SDK を使用して共有スナップショットを作成できます。 次のいくつかのセクションでは、ポータル、CLI、および Powershell を使用して共有スナップショットを作成する方法を説明します。 

ファイル共有を使用中に、該当のファイル共有の共有スナップショットを取得できます。 ただし、共有スナップショットでは、共有スナップショット コマンドが実行された時点で Azure ファイル共有に既に書き込まれていたデータのみを取得します。 このため、任意のアプリケーションやオペレーション システムでキャッシュされたデータが除外される可能性があります。

### <a name="create-share-snapshot-using-portal"></a>ポータルを使用した共有スナップショットの作成  
ポータルでファイル共有に移動して、`Create a Snapshot` ボタンを選択するだけで、ある時点での共有スナップショットを作成できます。

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>CLI 2.0 を使用した共有スナップショットの作成
`az storage share snapshot` コマンドを使用して、共有スナップショットを作成できます。

```azurecli-interactive
az storage share snapshot -n <share name>
```

サンプル出力
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

### <a name="create-share-snapshot-using-powershell"></a>Powershell を使用した共有スナップショットの作成
`$share.Snapshot()` コマンドを使用して、共有スナップショットを作成できます。

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
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>共有スナップショットの一覧表示、共有スナップショット コンテンツの参照、およびスナップショットからの復元

REST、クライアント ライブラリ、PowerShell、およびポータル経由で Windows の "以前のバージョン" の統合を使用して、ファイル共有に関連付けられた共有スナップショットを列挙できます。 Azure ファイル共有をマウントすると、SMB の"以前のバージョン" の統合を使用して、以前のバージョンの全ファイルを表示できるようになります。 Azure ファイル共有をマウントすると、SMB の"以前のバージョン" の統合を使用して、以前のバージョンの全ディレクトリを表示できるようになります。 次のいくつかのセクションでは、Azure ポータル、Windows、および Azure CLI 2.0 を使用して共有スナップショットを一覧表示、参照、および復元する方法について説明します。

### <a name="share-snapshot-operations-in-portal"></a>ポータルでの共有スナップショットの操作

ポータルでのファイル共有の共有スナップショットをすべて閲覧し、共有スナップショットを参照してそのコンテンツを表示できます。

#### <a name="view-share-snapshot"></a>共有スナップショットの表示
スナップショットのファイル共有で、**[スナップショットの表示]** を選択します。

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>共有スナップショット コンテンツの一覧表示と参照
共有スナップショットの一覧を表示し、目的のタイムスタンプから共有スナップショットを選択して、コンテンツを直接参照します。

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

一覧のスナップショット ビューにある **[接続]** ボタンを選択して、`net use` コマンドと直接参照できる特定の共有スナップショットへのディレクトリ パスを取得することもできます。


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>共有スナップショットからのダウンロードまたは復元
ポータル内から、スナップショットの目的のファイルをダウンロードまたは復元します。

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Windows でのファイル共有スナップショットの操作
ファイル共有の共有のスナップショットを既に取得している場合、Windows 上のマウントされている Azure ファイル共有から、共有、ディレクトリ、または特定のファイルの以前のバージョンを表示できます。 たとえば、**以前のバージョン**機能を使用して、Windows で以前のバージョンの特定のディレクトリを表示および復元します。

> [!Note]  
> ファイル レベルだけでなく共有レベルでも、同じ操作を実行できます。 該当のディレクトリまたはファイルの変更を含むバージョンのみが、一覧に表示されます。 特定のディレクトリまたはファイルが 2 つの共有スナップショット間で変更されなかった場合、共有レベルの以前のバージョン一覧にのみ共有スナップショットが表示され、ディレクトリまたはファイルの以前のバージョン一覧には表示されません。

#### <a name="mount-file-share"></a>ファイル共有をマウントする
最初に、net use コマンドを使用してファイル共有をマウントします。

#### <a name="open-mounted-share-in-explorer"></a>エクスプローラーでマウントされた共有を開く
ファイル エクスプローラーに移動し、マウントされた共有を見つけます。

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>以前のバージョンを一覧表示する
 復元が必要な項目または親項目に移動します。 ダブルクリックして、目的のディレクトリに移動します。 右クリックして、メニューから [プロパティ] を選択する

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

**[以前のバージョン]** を選択して、このディレクトリの共有スナップショットの一覧を表示します。 ネットワークの速度と共有のスナップショットの数に応じて、一覧の読み込みに数秒かかる場合があります。

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

**[開く]** を選択して、特定のスナップショットを参照できます。 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>以前のバージョンからの復元
**復元**すると、共有スナップショット作成時のディレクトリ全体のコンテンツが元の場所に再帰的にコピーされます。
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 でのファイル共有スナップショットの操作
Azure CI 2.0 を使用して、共有スナップショットの一覧表示、共有スナップショット コンテンツの参照、共有スナップショットからのファイルの復元またはダウンロード、共有スナップショットの削除などの同じ操作を実行できます。

#### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示

`--include-snapshots` と共に [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list)を使用して、特定の共有の共有スナップショットを一覧表示できます。

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>サンプル出力
コマンドによって、共有スナップショットの一覧が、関連するすべてのプロパティと共に表示されます。

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

#### <a name="browse-share-snapshots"></a>共有スナップショットの参照
また、[`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list)を使用して、特定の共有スナップショットを参照して、コンテンツを表示することもできます。 共有の名前は、`--share-name` およびタイムスタンプと共に指定する必要があります。ここでは、`--snapshot '2017-10-04T19:45:18.0000000Z'` を参照します。

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>サンプル出力

共有スナップショットのコンテンツが、その共有スナップショットが作成された時点の共有のコンテンツと同じであることがわかります。

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
#### <a name="restore-from-share-snapshots"></a>共有スナップショットからの復元

`az storage file download` コマンドを使用して、共有スナップショットからファイルをコピーまたはダウンロードすることで、ファイルを復元できます。

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>サンプル出力

ダウンロードされたファイルのコンテンツおよびそのプロパティは、共有スナップショットが作成された時点のコンテンツおよびプロパティと同じであることがわかります。

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

## <a name="delete-azure-files-share-snapshot"></a>Azure ファイル共有スナップショットの削除

Azure ポータル、PowerShell、CLI、REST API、または任意のストレージ SDK を使用して、ファイル共有スナップショットを削除できます。 次のいくつかのセクションでは、Azure ポータル、CLI、および Powershell を使用して共有スナップショットを削除する方法を説明します。

削除する共有スナップショットを決定するために、任意の比較ツールを使用して、共有スナップショットを参照して、2 つの共有スナップショット間の違いを表示することができます。 

共有スナップショットを保持している共有を削除することはできません。 共有を削除できるようにするには、最初に、すべての共有スナップショットを削除しておく必要があります。

### <a name="delete-share-snapshot-using-portal"></a>ポータルを使用した共有スナップショットの削除  
ファイル共有ブレードに移動してポータルの`delete` ボタンを選択し、1 つまたは複数の共有スナップショットを削除できます。

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Azure CLI 2.0 を使用した共有スナップショットの削除
共有スナップショットのタイムスタンプと共に `--snapshot '2017-10-04T23:28:35.0000000Z' ` パラメーターを指定した `[az storage share delete]` コマンドを使用して、共有スナップショットを削除することができます。

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

サンプル出力
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Powershell を使用した共有スナップショットの削除
次のように、`Remove-AzureStorageShare -Share` コマンドを使用して、共有スナップショットを削除できます。

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
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