---
title: "インポート処理中にプロパティとメタデータを設定する | Microsoft Docs"
description: "Import/Export ツールを実行してドライブを準備中に、目的の BLOB で設定するプロパティとメタデータを指定する方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 5d75bdb5ff39934e02d9af721b51e0aca24026bb
ms.openlocfilehash: f49604cf6738da3576ed2a65ca96d083d5e8219b


---

# <a name="setting-properties-and-metadata-during-the-import-process"></a>インポート処理中にプロパティとメタデータを設定する
Microsoft Azure Import/Export ツールを実行してドライブを準備する場合は、目的の BLOB で設定するプロパティとメタデータを指定できます。 次の手順に従います。  
  
1.  BLOB のプロパティを設定するには、プロパティの名前と値を指定するテキスト ファイルをローカル コンピューターで作成します。  
  
2.  BLOB のメタデータを設定するには、メタデータの名前と値を指定するテキスト ファイルをローカル コンピューターで作成します。  
  
3.  `PrepImport` 操作の一部として、これらのファイルの一方または両方の完全なパスを Azure Import/Export ツールに渡します。  
  
> [!NOTE]
>  コピー セッションの一部としてプロパティまたはメタデータ ファイルを指定すると、それらのプロパティやメタデータは、そのコピー セッションの一部としてインポートされるすべての BLOB に対して設定されます。 インポート中の一部の BLOB のプロパティまたはメタデータの別のセットを指定する場合は、異なるプロパティまたはメタデータ ファイルを使用して別のコピー セッションを作成する必要があります。  
  
## <a name="specify-blob-properties-in-a-text-file"></a>テキスト ファイルに BLOB のプロパティを指定する  
BLOB のプロパティを指定するには、ローカル テキスト ファイルを作成し、要素としてプロパティ名を指定し、値としてプロパティ値を指定する XML を含めます。 一部のプロパティ値を指定する例を次に示します。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
ファイルをローカルの場所 (`C:\WAImportExport\ImportProperties.txt` など) に保存します。  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>テキスト ファイルに BLOB のメタデータを指定する  
同様に、BLOB のメタデータを指定するには、要素としてメタデータ名を指定し、値としてメタデータ値を指定するローカル テキスト ファイルを作成します。 一部のメタデータ値を指定する例を次に示します。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
ファイルをローカルの場所 (`C:\WAImportExport\ImportMetadata.txt` など) に保存します。  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>プロパティ ファイルまたはメタデータ ファイルを含むコピー セッションを作成する  
Azure Import/Export ツールを実行してインポート ジョブの準備をする場合、`PropertyFile` パラメーターを使用してコマンド ラインでプロパティ ファイルを指定します。 `/MetadataFile` パラメーターを使用してコマンド ラインでメタデータ ファイルを指定します。 両方のファイルを指定した例は次の通りです。  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="see-also"></a>関連項目  
[インポート/エクスポート サービスのメタデータとプロパティ ファイルの形式](storage-import-export-file-format-metadata-and-properties.md)



<!--HONumber=Dec16_HO2-->


