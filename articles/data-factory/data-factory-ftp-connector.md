---
title: "Azure Data Factory を使用した FTP サーバーからのデータ移動 | Microsoft Docs"
description: "Azure Data Factory を使用して FTP サーバーからデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e7abf7b5cd6042ce64e6e09683b259bac82d6df
ms.openlocfilehash: 492c383865e86a19e56af816a77e02922adbc790


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Azure Data Factory を使用して FTP サーバーからデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、FTP サーバーのデータをサポートされたシンク データ ストアに移動する方法について説明します。 この記事では、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要と、ソース/シンクとしてサポートされるデータ ストアの一覧を紹介します。

データ ファクトリは、他のデータ ストアから FTP サーバーへのデータの移動ではなく、FTP サーバーから他のデータ ストアへのデータの移動のみをサポートします。 オンプレミスとクラウド FTP サーバーの両方をサポートします。

データを**オンプレミス**の FTP サーバーからクラウド データ ストアに移動する場合は (例: Azure Blob Storage)、Data Management Gateway をインストールして使用します。 Data Management Gateway はオンプレミスのコンピューターにインストールされたクライアント エージェントで、これにより、クラウド サービスはオンプレミスのリソースに接続できます。 ゲートウェイについて詳しくは、[Data Management Gateway に関する記事](data-factory-data-management-gateway.md)をご覧ください。 ゲートウェイの設定手順とその使用方法については、[オンプレミスの場所とクラウド間のデータ移動に関する記事](data-factory-move-data-between-onprem-and-cloud.md)を参照してください。 サーバーが Azure IaaS 仮想マシン (VM) 上にある場合でも、FTP サーバーに接続するためにゲートウェイを使用します。

FTP サーバーとして、同じオンプレミスのコンピューターまたは Azure IaaS VM にゲートウェイをインストールできます。 ただし、リソースの競合を防ぎ、パフォーマンスの向上を図るために、別のコンピューターまたは別の Azure IaaS VM にゲートウェイをインストールすることをお勧めします。 別のコンピューターにゲートウェイをインストールすると、そのコンピューターが FTP サーバーにアクセスできるようになります。

## <a name="copy-data-wizard"></a>データのコピー ウィザード
FTP サーバーとの間でデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

以下の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>サンプル: FTP サーバーから Azure BLOB にデータをコピーする
このサンプルは、FTP サーバーから Azure Blob Storage にデータをコピーする方法を示します。 Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているシンクのいずれかにデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることもできます。  

このサンプルでは、次の Data Factory のエンティティがあります。

* [FtpServer](#ftp-linked-service-properties) 型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)型のリンクされたサービス。
* [FileShare](#fileshare-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [FileSystemSource](#ftp-copy-activity-type-properties) および [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに FTP サーバーから Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**FTP のリンクされたサービス** この例では、ユーザー名とパスワードをプレーン テキストで使用した基本認証を使用します。 また、次のいずれかの方法を使用することもできます。

* 匿名認証
* 資格情報が暗号化された基本認証
* FTP over SSL/TLS (FTPS)

使用可能なさまざまな種類の認証については、[FTP のリンクされたサービス](#ftp-linked-service-properties)に関するセクションを参照してください。

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Azure Storage のリンクされたサービス**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**FTP 入力データセット** このデータセットは FTP フォルダー `mysharedfolder` とファイル `test.csv` を参照します。 パイプラインにより、ファイルがコピー先にコピーされます。

"external" を "true" に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```

## <a name="ftp-linked-service-properties"></a>FTP のリンクされたサービスのプロパティ
次の表は、FTP のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| type |type プロパティを FtpServer に設定する必要があります |はい |&nbsp; |
| host |FTP サーバーの名前または IP アドレス |はい |&nbsp; |
| authenticationType |認証の種類を指定します |はい |Basic、Anonymous |
| username |FTP サーバーへのアクセスを持つユーザー |いいえ |&nbsp; |
| パスワード |ユーザーのパスワード (ユーザー名) |いいえ |&nbsp; |
| encryptedCredential |FTP サーバーにアクセスするための暗号化された資格情報 |いいえ |&nbsp; |
| gatewayName |オンプレミスの FTP サーバーに接続するための Data Management Gateway ゲートウェイの名前 |いいえ |&nbsp; |
| ポート |FTP サーバーがリッスンしているポート |いいえ |21 |
| enableSsl |FTP over SSL/TLS チャネルを使用するかどうかを指定します |いいえ |true |
| enableServerCertificateValidation |FTP over SSL/TLS チャネルを使用した場合にサーバーの SSL 証明書の検証を有効にするかどうかを指定します |いいえ |true |

### <a name="using-anonymous-authentication"></a>匿名認証を使用する

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>基本認証のためユーザー名とパスワードをプレーン テキストで使用

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>ポート、enableSsl、enableServerCertificateValidation を使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>認証およびゲートウェイに encryptedCredential を使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

オンプレミスの FTP データ ソースの資格情報の設定について詳しくは、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>FTP のコピー アクティビティの type プロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、type プロパティはソースとシンクの種類によって異なります。

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

* [コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) を参照してください。



<!--HONumber=Jan17_HO4-->


