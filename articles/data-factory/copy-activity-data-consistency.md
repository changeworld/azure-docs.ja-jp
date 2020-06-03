---
title: コピー アクティビティでのデータ整合性の検証
description: Azure Data Factory のコピー アクティビティでデータ整合性の検証を有効にする方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663710"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>コピー アクティビティでのデータ整合性の検証 (プレビュー)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ソース ストアからコピー先ストアにデータを移動するとき、Azure Data Factory コピー アクティビティでは、データがソース ストアからコピー先ストアに正常にコピーされただけでなく、ソース ストアとコピー先ストアの間の整合性も確保されていることを確認するための、追加のデータ整合性検証を行うことができます。 データの移動中に整合性のないデータが検出されたら、コピー アクティビティを中止するか、またはフォールト トレランス設定を有効にして整合性のないデータをスキップすることで、その他のデータをコピーし続けることができます。 スキップされたオブジェクト名を取得するには、コピー アクティビティでセッション ログ設定を有効にします。 

> [!IMPORTANT]
> この機能は現在プレビュー段階にあり、次のような制限があります (これらの制限については現在対応中です)。
>- データ整合性の検証は、コピー アクティビティで 'PreserveHierarchy' 動作が指定された、ファイル ベースのストア間でのバイナリ ファイルのコピーのみで使用できます。 表形式データをコピーする場合、コピー アクティビティではデータ整合性の検証をまだ使用できません。
>- コピー アクティビティのセッション ログ設定で、スキップされた整合性のないファイルのログの記録を有効にした場合、コピー アクティビティが失敗した際のログ ファイルの完全性が 100% 保証されることはありません。
>- 現状では、セッション ログには整合性のないファイルのみが含まれ、正常にコピーされたファイルはログに記録されません。

## <a name="supported-data-stores"></a>サポートされているデータ ストア

### <a name="source-data-stores"></a>ソース データ ストア

-   [Azure BLOB Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [ファイル システム](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>コピー先データ ストア

-   [Azure BLOB Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [ファイル システム](connector-file-system.md)


## <a name="configuration"></a>構成
次の例では、コピー アクティビティでデータ整合性の検証を有効にする JSON 定義を示します。 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
validateDataConsistency | このプロパティに true を設定した場合、ソース ストアとコピー先ストアの間でのデータ整合性を確保するために、ソース ストアからコピー先ストアにコピーされた各オブジェクトのファイル サイズ、lastModifiedDate、および MD5 チェックサムがコピー アクティビティにより確認されます。 このオプションを有効にすると、コピーのパフォーマンスが影響を受けることに注意してください。  | True<br/>False (既定値) | いいえ
dataInconsistency | 整合性のないデータをスキップするかどうかを指定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つです。<br/> -True: 整合性のないデータをスキップして、残りの部分をコピーします。<br/> -False: 整合性のないデータが見つかった場合、コピー アクティビティを中止します。<br/>このプロパティは、validateDataConsistency を True に設定した場合にのみ有効であることに注意してください。  | True<br/>False (既定値) | いいえ
logStorageSettings | セッション ログにより、スキップされたオブジェクトをログに記録できるようにするために指定可能な、プロパティのグループです。 | | いいえ
linkedServiceName | セッション ログ ファイルを格納するための、[Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) のリンクされたサービスです。 | `AzureBlobStorage` または `AzureBlobFS` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | ログ ファイルのパス。 | ログ ファイルを格納するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

>[!NOTE]
>- ステージング コピー シナリオでは、データの整合性はサポートされていません。 
>- 任意のストレージ ストアから Azure Blob Storage または Azure Data Lake Storage Gen2 にバイナリ ファイルをコピーする場合、ソース ストアとコピー先ストアの間のデータの整合性を確保するために、コピー アクティビティによってファイル サイズと MD5 チェックサムの検証が行われます。 
>- 任意のストレージ ストアから Azure Blob Storage または Azure Data Lake Storage Gen2 以外のストレージ ストアにバイナリ ファイルをコピーする場合、ソース ストアとコピー先ストアの間のデータの整合性を確保するために、コピー アクティビティによってファイル サイズの検証が行われます。


## <a name="monitoring"></a>監視

### <a name="output-from-copy-activity"></a>コピー アクティビティからの出力
コピー アクティビティが完全に実行された後、各コピー アクティビティの実行の出力から、データ整合性検証の結果を確認できます。

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
データ整合性の検証の詳細は、"dataConsistencyVerification プロパティ" から確認できます。

**VerificationResult** の値: 
-   **Verified**:コピーしたデータは、ソース ストアとコピー先ストアとの間で整合性があることが確認されています。 
-   **NotVerified**:コピー アクティビティで validateDataConsistency が有効になっていないため、コピーしたデータの整合性が検証されていません。 
-   **Unsupported**:この特定のコピー ペアでは、データの整合性の確認がサポートされていないため、コピーしたデータの整合性が検証されていません。 

**InconsistentData** の値: 
-   **Found**:ADF のコピー アクティビティで、整合性のないデータが検出されました。 
-   **Skipped**:ADF のコピー アクティビティで、整合性のないデータが検出され、スキップされました。 
-   **None**:ADF のコピー アクティビティで、整合性のないデータは検出されませんでした。 ソース ストアとコピー先ストアの間でデータの整合性があることが確認されたか、またはコピー アクティビティで validateDataConsistency を無効にしたためである可能性があります。 

### <a name="session-log-from-copy-activity"></a>コピー アクティビティからのセッション ログ

整合性のないファイルをログに記録するように構成した場合は、`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` のパスからログ ファイルを見つけることができます。  ログ ファイルは csv ファイルになります。 

ログ ファイルのスキーマは次のとおりです。

列 | 説明 
-------- | -----------  
Timestamp | ADF が整合性のないファイルをスキップしたときのタイムスタンプ。
Level | この項目のログ レベル。 ファイルのスキップを示す項目は、'Warning' レベルになります。
OperationName | 各ファイルでの ADF コピー アクティビティの実行動作。 スキップされるファイルを指定する場合は 'FileSkip' になります。
OperationItem | スキップされるファイル名。
Message | ファイルがスキップされた理由を示す詳細情報。

ログ ファイルの例を次に示します。 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
上記のログ ファイルから、sample1.csv がスキップされたことを確認できます。これは、ソース ストアとコピー先ストアの間で、整合性があることを確認できなかったためです。 sample1.csv が不整合になった詳しい理由として、ADF のコピー アクティビティによってファイルがコピーされていたのと同時に、他のアプリケーションによってそれが変更されていたためであることがわかります。 



## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)


