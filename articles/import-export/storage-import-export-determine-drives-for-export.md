---
title: Azure Import/Export でのエクスポートに必要なドライブの数を調べる | Microsoft Docs
description: Azure Import/Export サービスを使用したエクスポートに必要なドライブの数を確認します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716286"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>Azure Import/Export でのエクスポートに必要なドライブの数を調べる 

この記事では、Azure Import/Export のエクスポート注文に対して提供する物理ドライブの数を決定する方法について説明します。 Azure Import/Export ツールを使用します。 この記事では、ツールをインストールして使用する方法について説明します。

Azure Import/Export バージョン 1 ツールを使用すると、エクスポートされるすべての BLOB の一覧が表示され、必要なオーバーヘッドを考慮して指定サイズのドライブにそれらを収める方法が計算されてから、BLOB とドライブの使用情報を保持するために必要なドライブの数が推定されます。

エクスポート注文では、2.5 インチ SSD、2.5 インチ HDD、または 3.5 インチ HDD を使用できます。 詳細については、「[サポートされるディスク](storage-import-export-requirements.md#supported-disks)」を参照してください。


## <a name="prerequisite"></a>前提条件

* [サポート対象の OS バージョン](storage-import-export-requirements.md#supported-operating-systems)が実行されている Windows システムで、ツールを使用する必要があります。

## <a name="determine-how-many-drives-you-need"></a>必要なドライブの数を決定する

エクスポート注文に必要な物理ディスクの数を確認するには、次の手順のようにします。

1. Azure Import/Export バージョン 1 ツール (WAImportExportV1.zip) の現在のリリースを、BLOB 用に Windows システムにダウンロードします。
  1. [WAImportExport バージョン 1 をダウンロードします](https://www.microsoft.com/download/details.aspx?id=42659)。 現在のバージョンは 1.5.0.300 です。
  1. 既定のフォルダー `waimportexportv1` に解凍します。 たとえば、「 `C:\WaImportExportV1` 」のように入力します。

2. 管理特権を使用して PowerShell またはコマンド ライン ウィンドウを開きます。 解凍されたフォルダーにディレクトリを変更するには、次のコマンドを実行します。

   `cd C:\WaImportExportV1`

3. 選択した BLOB に必要なディスク数を確認するには、次のコマンドを実行します。

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    パラメーターについては次の表で説明します。

    |コマンド ライン パラメーター|説明|
    |--------------------------|-----------------|
    |**/logdir:**|省略可能。 ログ ディレクトリ。 詳細ログ ファイルは、このディレクトリに書き込まれます。 指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|
    |**/ExportBlobListFile:**|必須。 エクスポートする BLOB の BLOB パスや BLOB パスのプレフィックスの一覧を含む XML ファイルへのパス。 インポート/エクスポート サービス REST API の [Put Job](/rest/api/storageimportexport/jobs) 操作の `BlobListBlobPath` 要素で使用されるファイル形式です。|
    |**/DriveSize:**|必須。 エクスポート ジョブに使用するドライブのサイズ (*例:* 500 GB、1.5 TB)。|

    「[PreviewExport コマンドの例](#example-of-previewexport-command)」を参照してください。

4. エクスポート ジョブ用に送付するドライブの読み取り/書き込みが可能であることを確認します。

## <a name="example-of-previewexport-command"></a>PreviewExport コマンドの例

次の例は `PreviewExport` コマンドを示しています。

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

エクスポート BLOB 一覧ファイルには、次のように BLOB 名や BLOB のプレフィックスが含まれる場合があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>有効な BLOB パスの例

次の表は、BLOB リスト ファイル内の &lt;BlobPath&gt; タグと &lt;BlobPathPrefix&gt; タグで使用できる BLOB パスと BLOB パス プレフィックスの例です。

* "*パス*" は、1 つの BLOB またはファイルを選択して、フィルター処理します。
* "*プレフィックス*" は、複数の BLOB または複数のファイルを選択して、フィルター処理します。

   | セレクター | BLOB パスとプレフィックス | 説明 |
   | --- | --- | --- |
   | [指定値で始まる] |/ |ストレージ アカウントのすべての BLOB をエクスポートする |
   | [指定値で始まる] |/$root/ |ルート コンテナー内のすべての BLOB をエクスポートする |
   | [指定値で始まる] |/book |プレフィックス " **book** |
   | [指定値で始まる] |/music/ |コンテナー " **music** |
   | [指定値で始まる] |/music/love |コンテナー "**music**" 内の、プレフィックス "**love**" で始まるすべての BLOB をエクスポートする |
   | [等しい] |$root/logo.bmp |ルート コンテナー内の BLOB " **logo.bmp** " をエクスポートする |
   | [等しい] |videos/story.mp4 |コンテナー "**videos**" 内の BLOB "**story.mp4**" をエクスポートする |


### <a name="sample-output"></a>サンプル出力

情報ログを省略した出力の例を次に示します。

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>次の手順

- [Azure Blob Storage から BLOB をエクスポートする](storage-import-export-data-from-blobs.md)
