---
title: Azure Data Factory での Avro 形式
description: このトピックでは、Azure Data Factory で Avro 形式を処理する方法について説明します。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416346"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory での Avro 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Avro ファイルを解析する場合や、Avro 形式にデータを書き込む場合**は、この記事に従ってください。 

Avro 形式は次のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Avro データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**Avro** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| avroCompressionCodec | Avro ファイルへの書き込み時に使用する圧縮コーデック。 Avro ファイルから読み取る場合、Data Factory では、ファイルのメタデータに基づいて圧縮コーデックが自動的に判別されます。<br>サポートされる型は "**none**" (既定値)、"**deflate**"、"**snappy**" です。 Avro ファイルの読み取りおよび書き込みの場合、コピー アクティビティでは現在、Snappy がサポートされていないことに注意してください。 | いいえ       |

> [!NOTE]
> Avro ファイルでは、列名に空白文字はサポートされません。

Azure Blob Storage の Avro データセットの例を次に示します。

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Avro のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="avro-as-source"></a>ソースとしての Avro

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **AvroSource** に設定する必要があります | はい      |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

### <a name="avro-as-sink"></a>シンクとしての Avro

コピー アクティビティの ***\* sink \**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **AvroSink** に設定する必要があります。 | はい      |
| storeSettings | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

## <a name="data-type-support"></a>データ型のサポート

### <a name="copy-activity"></a>コピー アクティビティ
コピー アクティビティでは、Avro の[複合データ型](https://avro.apache.org/docs/current/spec.html#schema_complex)はサポートされていません (レコード、列挙型、配列、マップ、共用体、および固定)。

### <a name="data-flows"></a>データ フロー
データ フローで Avro ファイルを使用する場合は、複合データ型の読み取りと書き込みを行うことができますが、最初にデータセットから物理スキーマをクリアしてください。 データ フローでは、ロジカル プロジェクションを設定し、複雑な構造体の列を導出し、それらのフィールドを Avro ファイルに自動マッピングできます。

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
