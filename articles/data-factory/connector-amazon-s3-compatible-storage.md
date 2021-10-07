---
title: Amazon Simple Storage Service (S3) 対応ストレージからデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインを使用して、Amazon S3 Compatible Storage のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 82abc8fcfacc09621b66192c1f40b8c33d73ae50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563462"
---
# <a name="copy-data-from-amazon-s3-compatible-storage-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用した Amazon S3 対応ストレージからのデータのコピー


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Amazon Simple Storage Service (Amazon S3) 対応ストレージからデータをコピーする方法を概説します。 詳細については、[Azure Data Factory](introduction.md) と [Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Amazon S3 対応ストレージ コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

具体的には、この Amazon S3 対応ストレージ コネクタでは、ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析することをサポートしています。 S3 への要求を認証するために、コネクタでは [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) が使用されます。 この Amazon S3 対応ストレージ コネクタを使用し、あらゆる S3 対応ストレージ プロバイダーからデータをコピーできます。 リンクされているサービスの構成で、対応するサービスの URL を指定します。



## <a name="required-permissions"></a>必要なアクセス許可

Amazon S3 対応ストレージからデータをコピーするには、Amazon S3 オブジェクト操作に対する次のアクセス許可が付与されている必要があります: `s3:GetObject` および `s3:GetObjectVersion`。

UI を使用して作成する場合は、リンクされたサービスへの接続のテストやルートからの参照などの操作に対して、追加の `s3:ListAllMyBuckets` および `s3:ListBucket`/`s3:GetBucketLocation` アクセス許可が必要です。 これらのアクセス許可を付与しない場合は、UI から [ファイル パスへの接続をテスト] または [指定されたパスから参照] オプションを選択できます。

Amazon S3 のアクセス許可の完全な一覧については、[ポリシーでのアクセス許可の指定](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)に関する AWS サイトのページを参照してください。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-s3-compatible-storage-using-ui"></a>UI を使用して Amazon S3 対応ストレージのリンク サービスを作成する

次の手順を使用して、Azure portal UI で Amazon S3 対応ストレージのリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. Amazon を検索し、Amazon S3 対応ストレージ コネクタを選択します。

   :::image type="content" source="media/connector-amazon-s3-compatible-storage/amazon-s3-compatible-storage-connector.png" alt-text="Amazon S3 対応ストレージ コネクタを選択します。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-amazon-s3-compatible-storage/configure-amazon-s3-compatible-storage-linked-service.png" alt-text="Amazon S3 対応ストレージのリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細 

以下のセクションで、Amazon S3 対応ストレージに固有のエンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon S3 互換のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **AmazonS3Compatible** に設定する必要があります。 | はい |
| accessKeyId | シークレット アクセス キーの ID。 |はい |
| secretAccessKey | シークレット アクセス キー自体。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| serviceUrl | カスタム S3 エンドポイント `https://<service url>` を指定します。 | いいえ |
| forcePathStyle | 仮想ホスト形式のアクセスではなく、S3 の[パス形式のアクセス](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access)を使用するかどうかを示します。 指定できる値は **false** (既定値)、**true** です。<br> パス形式のアクセスが必要かどうかについては、各データ ストアのドキュメントを確認してください。 |いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 |いいえ |



**例:**

```json
{
    "name": "AmazonS3CompatibleLinkedService",
    "properties": {
        "type": "AmazonS3Compatible",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```



## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Amazon S3 互換では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の **type** プロパティは、**AmazonS3CompatibleLocation** に設定する必要があります。 | はい      |
| bucketName | S3 互換ストレージ バケット名。                                          | はい      |
| folderPath | 特定のバケットの下のフォルダーへのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でこれを指定します。 | いいえ       |
| fileName   | 特定のバケットおよびフォルダー パスの下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でこれを指定します。 | いいえ       |
| version | S3 互換ストレージのバージョン管理が有効になっている場合、S3 互換ストレージ オブジェクトのバージョン。 指定しない場合は、最新バージョンがフェッチされます。 |いいえ |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 Compatible Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3CompatibleLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Amazon S3 互換ストレージ ソースでサポートされているプロパティの一覧を示します。

### <a name="amazon-s3-compatible-storage-as-a-source-type"></a>ソース タイプとしての Amazon S3 互換ストレージ

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Amazon S3 互換ストレージでは、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` の **type** プロパティは **AmazonS3CompatibleReadSettings** に設定する必要があります。 | はい                                                         |
| ***コピーするファイルを特定する:*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されている所定のバケットまたはフォルダー/ファイル パスからコピーします。 バケットまたはフォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: S3 互換ストレージのプレフィックス<br>- prefix | ソース S3 互換ストレージ ファイルをフィルター処理するために、データセットで構成されている、指定されたバケットにある S3 互換ストレージ キー名のプレフィックス。 名前が `bucket_in_dataset/this_prefix` で始まる S3 互換ストレージ キーが選択されます。 ワイルドカード フィルターより優れたパフォーマンスを提供する S3 互換ストレージのサービス側フィルターを利用します。<br/><br/>プレフィックスを使用して階層を保持した状態でファイルベースのシンクにコピーする場合は、プレフィックスの最後の "/" の後のサブパスが保持されます。 たとえば、ソース `bucket/folder/subfolder/file.txt` があり、プレフィックスを `folder/sub` として構成した場合、保持されるファイル パスは `subfolder/file.txt` です。 | いいえ |
| オプション 3: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のバケットの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 フォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| オプション 3: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、特定のバケットおよびフォルダー パス (またはワイルドカード フォルダー パス) の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 ファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい |
| オプション 4: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスであるファイルを 1 行につき 1 つずつ指定します。<br/>このオプションを使用している場合は、データ セットにファイル名を指定しないでください。 その他の例については、[ファイル リストの例](#file-list-examples)を参照してください。 |いいえ |
| ***追加の設定:*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 **recursive** が **true** に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 <br>使用可能な値: **true** (既定値) および **false**。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| deleteFilesAfterCompletion | 宛先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを示します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルが既に宛先にコピーされソースからは削除されているが、他のファイルはまだソース ストアに残っていることがわかります。 <br/>このプロパティは、バイナリ ファイルのコピー シナリオでのみ有効です。 既定値: false。 |いいえ |
| modifiedDatetimeStart    | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 <br>最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する **NULL** にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が **NULL** の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が **NULL** の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                                          |
| enablePartitionDiscovery | パーティション分割されているファイルの場合は、ファイル パスのパーティションを解析し、それを追加のソース列として追加するかどうかを指定します。<br/>指定できる値は **false** (既定値) と **true** です。 | いいえ                                            |
| partitionRootPath | パーティション検出が有効になっている場合は、パーティション分割されたフォルダーをデータ列として読み取るための絶対ルート パスを指定します。<br/><br/>これが指定されていない場合は、既定で次のようになります。<br/>- ソース上のデータセットまたはファイルの一覧内のファイル パスを使用する場合、パーティションのルート パスはそのデータセットで構成されているパスです。<br/>- ワイルドカード フォルダー フィルターを使用する場合、パーティションのルート パスは最初のワイルドカードの前のサブパスです。<br/>- プレフィックスを使用する場合、パーティションのルート パスは最後の "/" の前のサブパスです。 <br/><br/>たとえば、データセット内のパスを "root/folder/year=2020/month=08/day=27" として構成するとします。<br/>- パーティションのルート パスを "root/folder/year=2020" として指定した場合は、コピー アクティビティによって、ファイル内の列とは別に、それぞれ "08" と "27" の値を持つ `month` と `day` という 2 つの追加の列が生成されます。<br/>- パーティションのルート パスが指定されない場合、追加の列は生成されません。 | いいえ                                            |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ                                                          |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3CompatibleStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3CompatibleReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| bucket | key | recursive | ソースのフォルダー構造とフィルターの結果 (太字のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。

次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | 構成 |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- バケット: `bucket`<br>- フォルダー パス: `FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `bucket/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスで 1 行につき 1 つのファイルを指定します。 |


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
