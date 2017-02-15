---
title: "インポート/エクスポート サービスのメタデータとプロパティ ファイルの形式 | Microsoft Docs"
description: "インポートまたはエクスポート ジョブの一部となる&1; つ以上の BLOB のメタデータとプロパティを指定する方法について説明します"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 78abb839badf99c6251673ee9914955df8c950bc
ms.openlocfilehash: 8c8800682a0ce2c52962a41c1c09fbc59595dee2


---
# <a name="import-export-service-metadata-and-properties-file-format"></a>インポート/エクスポート サービスのメタデータとプロパティ ファイルの形式
インポートまたはエクスポート ジョブの一部として&1; つ以上の BLOB のメタデータとプロパティを指定できます。 インポート ジョブの一部として作成されている BLOB のメタデータまたはプロパティを設定するには、インポートするデータを含むハード ドライブでメタデータまたはプロパティ ファイルを提供します。 エクスポート ジョブの場合、メタデータとプロパティは返されるハード ドライブに含まれるメタデータまたはプロパティ ファイルに書き込まれます。  
  
## <a name="metadata-file-format"></a>メタデータ ファイルの形式  
メタデータ ファイルの形式は、次のとおりです。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML 要素|型|Description|  
|-----------------|----------|-----------------|  
|`Metadata`|ルート要素|メタデータ ファイルのルート要素です。|  
|`metadata-name`|String|省略可能。 XML 要素は、BLOB のメタデータの名前を指定し、その値はメタデータの設定の値を指定します。|  
  
## <a name="properties-file-format"></a>プロパティ ファイルの形式  
メタデータ ファイルの形式は、次のとおりです。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML 要素|型|説明|  
|-----------------|----------|-----------------|  
|`Properties`|ルート要素|プロパティ ファイルのルート要素。|  
|`Last-Modified`|String|省略可能。 BLOB の最終更新時刻。 エクスポート ジョブの場合のみ。|  
|`Etag`|String|省略可能。 BLOB の ETag 値。 エクスポート ジョブの場合のみ。|  
|`Content-Length`|String|省略可能。 BLOB のサイズ (単位: バイト)。 エクスポート ジョブの場合のみ。|  
|`Content-Type`|String|省略可能。 BLOB のコンテンツの種類。|  
|`Content-MD5`|String|省略可能。 BLOB の MD5 ハッシュ。|  
|`Content-Encoding`|String|省略可能。 BLOB コンテンツのエンコーディング。|  
|`Content-Language`|String|省略可能。 BLOB コンテンツの言語。|  
|`Cache-Control`|String|省略可能。 BLOB のキャッシュ制御文字列。|  
  
 BLOB のメタデータとプロパティの設定についての詳細な規則については、「[Set Blob Properties](/rest/api/storageservices/fileservices/set-blob-properties)」 (BLOB プロパティの設定)、「[Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata)」 (BLOB メタデータの設定)、「[Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/fileservices/setting-and-retrieving-properties-and-metadata-for-blob-resources)」 (BLOB リソースのプロパティとメタデータの設定と取得) をご覧ください。



<!--HONumber=Dec16_HO2-->


