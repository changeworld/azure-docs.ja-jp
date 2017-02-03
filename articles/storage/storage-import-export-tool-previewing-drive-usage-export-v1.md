---
title: "エクスポート ジョブのドライブ使用率のプレビュー | Microsoft Docs"
description: "Azure Import-Export サービスでエクスポート ジョブに選択した BLOB の一覧をプレビューする方法について説明します"
author: renashahmsft
manager: aungoo
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2015
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 78abb839badf99c6251673ee9914955df8c950bc
ms.openlocfilehash: d8ea4cb51c9609b8fa9ed5ee50762f981da68e20


---

# <a name="previewing-drive-usage-for-an-export-job"></a>エクスポート ジョブのドライブ使用率のプレビュー
エクスポート ジョブを作成する前に、エクスポートする一連の BLOB を選択する必要があります。 Microsoft Azure Import/Export サービスを使用すると、BLOB のパスの一覧や BLOB プレフィックスを使用して、選択した BLOB を表すことができます。  
  
 次に送信する必要があるドライブの数を決定する必要があります。 Microsoft Azure Import/Export ツールでは、使用するドライブのサイズに基づいて、選択した BLOB に対するドライブの使用率をプレビューする `PreviewExport` コマンドを提供します。 次のパラメーターを指定できます。  
  
|コマンド ライン オプション|Description|  
|--------------------------|-----------------|  
|**/logdir:**<LogDirectory\>|省略可能。 ログ ディレクトリです。 詳細ログ ファイルは、このディレクトリに書き込まれます。 ログ ディレクトリが指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|  
|**/sn:**<StorageAccountName\>|必須。 エクスポート ジョブのストレージ アカウント名です。|  
|**/sk:**<StorageAccountKey\>|コンテナー SAS が指定されていない場合のみ必須。 エクスポート ジョブのストレージ アカウントのアカウント キーです。|  
|**/csas:**<ContainerSas\>|ストレージ アカウント キーが指定されていない場合のみ必須。 エクスポート ジョブでエクスポートされる BLOB を一覧表示するためのコンテナー SAS です。|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|必須。 エクスポートする BLOB の BLOB のパスや BLOB のプレフィックスの一覧を含む XML ファイルへのパスです。 Import/Export サービス REST API の [Put Job](/rest/api/storageservices/importexport/Put-Job) 操作の `BlobListBlobPath` 要素で使用されるファイル形式です。|  
|**/DriveSize:**<DriveSize\>|必須。 エクスポート ジョブに使用するドライブのサイズ (*例:* 500 GB、1.5 TB)。|  
  
次の例は `PreviewExport` コマンドを示しています。  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
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

Azure Import/Export ツールでは、エクスポートするすべての BLOB を一覧表示し、必要なオーバーヘッドを考慮に入れて指定したサイズのドライブにそれらを詰め込む方法を計算し、BLOB とドライブの使用状況の情報を保持するために必要なドライブの数を推定します。  
  
情報ログを省略した出力の例を次に示します。  
  
```  
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
  
## <a name="see-also"></a>関連項目  
[Azure Import-Export ツールの参照](storage-import-export-tool-how-to-v1.md)


<!--HONumber=Dec16_HO2-->


