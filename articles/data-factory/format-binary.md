---
title: バイナリ形式
titleSuffix: Azure Data Factory & Azure Synapse
description: このトピックでは、Azure Data Factory と Azure Synapse Analytics でバイナリ形式を処理する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: b1000d83f674614213ad26aa9b926f6c9255720b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779656"
---
# <a name="binary-format-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory と Azure Synapse Analytics でのバイナリ形式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

バイナリ形式は、[Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure Files](connector-azure-file-storage.md)、[File System](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle Cloud Storage](connector-oracle-cloud-storage.md)、[SFTP](connector-sftp.md) の各コネクタでサポートされます。

バイナリ データセットは、[Copy アクティビティ](copy-activity-overview.md)、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)、または [Delete アクティビティ](delete-activity.md)で使用できます。 バイナリ データセットを使用する場合、サービスではファイルの内容は解析されず、そのまま処理されます。 

>[!NOTE]
>コピー アクティビティでバイナリ データセットを使用する場合は、バイナリ データセットからバイナリ データセットへのコピーのみが可能です。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、バイナリ データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは **Binary** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| compression | ファイル圧縮を構成するためのプロパティのグループ。 アクティビティの実行中に圧縮/圧縮解除を行う場合は、このセクションを構成します。 | いいえ |
| type | バイナリ ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**Tar**、または **TarGzip** です。 <br>コピー アクティビティを使用して **ZipDeflate**/**TarGzip**/**Tar** ファイルを圧縮解除し、ファイルベースのシンク データ ストアに書き込む場合、ファイルは既定で `<path specified in dataset>/<folder named as source compressed file>/` フォルダーに解凍されることに **注意** してください。圧縮ファイル名をフォルダー構造として保持するかどうかを制御するには、[コピー アクティビティのソース](#binary-as-source)に対して `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` を使用します。| いいえ       |
| level | 圧縮率です。 データセットがコピー アクティビティのシンクで使用される場合に適用されます。<br>使用できる値は、**Optimal** または **Fastest** です。<br>- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、圧縮後のファイルが最適に圧縮されていない場合があります。<br>- **Optimal**: 圧縮操作で最適に圧縮されますが、操作が完了するまでに時間がかかる場合があります。 詳細については、 [圧縮レベル](/dotnet/api/system.io.compression.compressionlevel) に関するトピックをご覧ください。 | いいえ       |

Azure Blob Storage のバイナリ データセットの例を次に示します。

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、バイナリのソースとシンクでサポートされるプロパティの一覧を示します。

>[!NOTE]
>コピー アクティビティでバイナリ データセットを使用する場合は、バイナリ データセットからバイナリ データセットへのコピーのみが可能です。

### <a name="binary-as-source"></a>ソースとしてのバイナリ

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **BinarySource** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の **バイナリの読み取り設定** に関する表を参照してください。 | いいえ       |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

`formatSettings` でサポートされている **バイナリの読み取り設定**:

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings の type は、**BinaryReadSettings** に設定する必要があります。 | はい      |
| compressionProperties | 特定の圧縮コーデックのデータを圧縮解除する方法のプロパティ グループ。 | いいえ       |
| preserveZipFileNameAsFolder<br>(" *`compressionProperties`->`type` の下に `ZipDeflateReadSettings` として*") | **ZipDeflate** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの ZIP ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、解凍されたファイルがサービスによって `<path specified in dataset>/<folder named as source zip file>/` に書き込まれます。<br>- **false** に設定した場合、サービスにより解凍されたファイルが `<path specified in dataset>` に直接書き込まれます。 競合または予期しない動作を避けるために、異なるソース ZIP ファイルに重複したファイル名がないことを確認します。  | いいえ |
| preserveCompressionFileNameAsFolder<br>(" *`compressionProperties`->`type` で `TarGZipReadSettings` または `TarReadSettings` として*") | **TarGzip**/**Tar** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの圧縮ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、サービスにより圧縮解除されたファイルが `<path specified in dataset>/<folder named as source compressed file>/` に書き込みます。 <br>- **false** に設定した場合、サービスにより圧縮解除されたファイルが `<path specified in dataset>` に直接書き込まれます。 競合または予期しない動作を避けるために、異なるソース ファイルに重複したファイル名がないことを確認します。 | いいえ |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>シンクとしてのバイナリ

コピー アクティビティの ***\* sink \**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **BinarySink** に設定する必要があります。 | はい      |
| storeSettings | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)