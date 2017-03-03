---
title: "Data Factory を使用して MongoDB からデータを移動する | Microsoft Docs"
description: "Azure Data Factory を使用して MongoDB データベースからデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: af15b530dd512873e4534fb61d276c8c8c3a196a
ms.openlocfilehash: 2de70faa090fb3da25fec8f8946e52fcae2677d3
ms.lasthandoff: 02/10/2017


---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory を使用して MongoDB からデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミスの MongoDB データベースから他のデータ ストアにデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般概要と、コピー アクティビティでサポートされるソース/シンク データ ストアの組み合わせについて紹介している、 [データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事に基づいています。

Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの MongoDB ソースへの接続をサポートします。 [Data Management Gateway](data-factory-data-management-gateway.md) の詳細については、「Data Management Gateway」をご覧ください。また、データを移動するデータ パイプラインにゲートウェイをセットアップするための詳しい手順については、[オンプレミスからクラウドへのデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。

> [!NOTE]
> Azure IaaS VM でホストされている場合でも、MongoDB への接続にゲートウェイを使用する必要があります。 クラウドでホストされている MongoDB のインスタンスに接続しようとしている場合は、IaaS VM にゲートウェイ インスタンスをインストールすることもできます。
>
>

現時点では、Data Factory は、他のデータ ストアから MongoDB へのデータの移動ではなく、MongoDB から他のデータ ストアへのデータの移動のみをサポートしています。

## <a name="supported-versions"></a>サポートされているバージョン
この MongoDB コネクタは、MongoDB バージョン 2.4、2.6、3.0、3.2 をサポートしています。

## <a name="prerequisites"></a>前提条件
Azure Data Factory サービスをオンプレミスの MongoDB データベースに接続できるようにするには、次のコンポーネントをインストールする必要があります。

* データベースをホストするコンピューターと同じコンピューター、またはデータベースとのリソースの競合を避けるために別のコンピューター上にインストールされた、バージョン 2.0 以降の Data Management Gateway。 Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータをクラウド サービスに接続するソフトウェアです。 Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。

    ゲートウェイをインストールすると、MongoDB への接続に使用される Microsoft MongoDB ODBC ドライバーが自動的にインストールされます。

## <a name="copy-data-wizard"></a>データのコピー ウィザード
MongoDB データベースから、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、MongoDB データベースから Azure BLOB ストレージにデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>サンプル: MongoDB から Azure BLOB にデータをコピーする
このサンプルは、オンプレミスの MongoDB データベースから Azure BLOB ストレージにデータをコピーする方法を示しています。 Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているシンクのいずれかにデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることもできます。  

このサンプルでは、次の Data Factory のエンティティがあります。

1. [OnPremisesMongoDb](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)型のリンクされたサービス。
3. [MongoDbCollection](#dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [MongoDbSource](#copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは MongoDB データベースのクエリ結果のデータを BLOB に&1; 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」の指示に従って Data Management Gateway を設定してください。

**MongoDB のリンクされたサービス**

```JSON
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB 入力データセット** external を true に設定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

```JSON
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                        "format": "%M"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%d"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%H"
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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** 型が **MongoDbSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、 **OnPremisesMongoDB** のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **OnPremisesMongoDb** |はい |
| server |MongoDB サーバーの IP アドレスまたはホスト名。 |はい |
| ポート |MongoDB サーバーがクライアント接続のリッスンに使用する TCP ポート。 |省略可能、既定値: 27017 |
| authenticationType |Basic または Anonymous。 |はい |
| username |MongoDB にアクセスするためのユーザー アカウント。 |はい (基本認証が使用される場合)。 |
| パスワード |ユーザーのパスワード。 |はい (基本認証が使用される場合)。 |
| authSource |認証のために資格情報を確認する際に使用する MongoDB データベースの名前。 |省略可能 (基本認証が使用される場合)。 既定では、管理者アカウントと、databaseName プロパティで指定されたデータベースが使用されます。 |
| databaseName |アクセスする MongoDB データベースの名前。 |はい |
| gatewayName |データ ストアにアクセスするゲートウェイの名前。 |はい |
| encryptedCredential |ゲートウェイによって暗号化された資格情報。 |省略可能 |

オンプレミスの MongoDB データ ソースの資格情報の設定について詳しくは、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

## <a name="dataset-type-properties"></a>データセットの type プロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **MongoDbCollection** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| collectionName |MongoDB データベースのコレクション名前。 |はい |

## <a name="copy-activity-type-properties"></a>コピー アクティビティの type プロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **MongoDbSource** である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリ文字列。 例: Select * from MyTable。 |いいえ (**データセット**の **collectionName** が指定されている場合) |

## <a name="schema-by-data-factory"></a>Data Factory によるスキーマ
Azure Data Factory サービスは、MongoDB コレクション内の最新のドキュメント 100 個を使用してスキーマを推論します。 この 100 個のドキュメントにスキーマが完全には含まれていない場合は、コピー操作中に無視される列が生じる可能性があります。

## <a name="type-mapping-for-mongodb"></a>MongoDB の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

MongoDB にデータを移動する場合、MongoDB 型から .NET 型に対する次のマッピングが使用されます。

| MongoDB 型 | .NET Framework 型 |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| 日付 |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |文字列 |
| 文字列 |文字列 |
| UUID |Guid |
| オブジェクト |入れ子の区切り文字に "_" を使用してフラット化された列に再正規化されます。 |

> [!NOTE]
> 仮想テーブルを使用した配列に対するサポートの詳細については、下の「 [仮想テーブルを使用した複合型のサポート](#support-for-complex-types-using-virtual-tables) 」セクションをご覧ください。
>
>

現時点では、次の MongoDB データ型はサポートされていません: DBPointer、JavaScript、Max/Min key、Regular Expression、Symbol、Timestamp、Undefined。

## <a name="support-for-complex-types-using-virtual-tables"></a>仮想テーブルを使用した複合型のサポート
Azure Data Factory では、ビルトインの ODBC ドライバーを使用して、MongoDB データベースへの接続や、MongoDB データベースからのデータのコピーを行います。 複数のドキュメントのさまざまな型が含まれた配列やオブジェクトなどの複合型については、ODBC ドライバーによって、対応する仮想テーブルへのデータの再正規化が行われます。 具体的には、テーブルにそのような列が含まれている場合に、ドライバーによって次の仮想テーブルが生成されます。

* 実テーブルと同じデータ (複合型列を除く) を含む **ベース テーブル**。 ベース テーブルには、それが表す実テーブルと同じ名前が使用されます。
* 複合型列ごとの**仮想テーブル**。入れ子になったデータが展開されます。 仮想テーブルの名前は、実テーブルの名前、区切り文字の "_"、配列またはオブジェクトの名前を使用して付けられます。

仮想テーブルは、非正規化データへのドライバーのアクセスを有効にして、実テーブルのデータを参照します。  詳細については、次の「例」を参照してください。 仮想テーブルのクエリや結合によって、MongoDB の配列の内容にアクセスできます。

[コピー ウィザード](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) を使用して、仮想テーブルを含む MongoDB データベースのテーブルの一覧を直感的に表示し、内部データをプレビューすることができます。 また、コピー ウィザードでクエリを構築して検証し、結果を確認することもできます。

### <a name="example"></a>例
例として下に挙げる "ExampleTable" は、各セルにオブジェクトの配列が&1; つ含まれた列 Invoices と、スカラー型の配列が&1; つ含まれた列 Ratings で構成された MongoDB テーブルです。

| _id | Customer Name | Invoices | Service Level | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”toaster”, price:”456”, discount:”0.2”}, {invoice_id:”124”, item:”oven”, price: ”1235”, discount: ”0.2”}] |シルバー |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |ゴールド |[1,2] |

ドライバーによって、この単一のテーブルを表す複数の仮想テーブルが生成されます。 最初の仮想テーブルは、次に示す "ExampleTable" という名前のベース テーブルです。 ベース テーブルには元のテーブルのすべてのデータが含まれますが、配列のデータは省略され、仮想テーブルで展開されます。

| _id | Customer Name | Service Level |
| --- | --- | --- |
| 1111 |ABC |シルバー |
| 2222 |XYZ |ゴールド |

次のテーブルは、例における元の配列を表す仮想テーブルを示しています。 これらのテーブルには、以下が含まれます。

* 元の配列の行に対応する元のプライマリ キー列への参照 (_Id 列を使用) 
* 元の配列内のデータの位置の指定
* 配列内の各要素の展開されたデータ

テーブル "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

テーブル "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
オンプレミスのデータ ストアから Azure データ ストアにデータを移動するデータ パイプラインを作成するための詳しい手順については、 [オンプレミスとクラウドの間でデータを移動する方法](data-factory-move-data-between-onprem-and-cloud.md) に関する記事をご覧ください。

