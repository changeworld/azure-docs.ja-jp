---
title: "Azure Data Factory のコピー アクティビティのフォールト トレランス - 互換性のない行のスキップ | Microsoft Docs"
description: "Azure Data Factory を使用して、コピー中に互換性のない行をスキップすることによるフォールト トレランスについて説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>コピー アクティビティのフォールト トレランス - 互換性のない行のスキップ

[コピー アクティビティ](data-factory-data-movement-activities.md)には、ソースとシンク データ ストアの間でデータをコピーするときの互換性のない行の取り扱いについて、さまざまなオプションが用意されています。 互換性のないデータが見つかったときに、コピー アクティビティを中止する (既定の動作) か、互換性のない行をスキップしてすべてのデータのコピーを続行するかを選択できます。 さらに、Azure Blob のログに互換性のない行を記録することもできます。これにより、エラーの原因を特定し、データ ソース上のデータを修正して、再度操作を実行できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
現在、コピー アクティビティはコピー中の次の互換性のない状況を検出、スキップ、およびログに記録できます。

- **ソースとシンク ネイティブ型の間のデータ型に互換性がない**

    例: Azure Blob の CSV ファイルから Azure SQL Database にコピーするときに、Azure SQL Database で定義されたスキーマに 3 つの *INT* 型の列があります。 その場合、ソースの CSV ファイル内の数値データの入った行 (`123,456,789` など) は正常にコピーされる一方で、数値以外の値が入った行 (`123,456,abc` など) は互換性のない行としてスキップされます。

- **ソースとシンクの間で列の数が一致しない**

    例: Azure Blob の CSV ファイルから Azure SQL Database にコピーするときに、SQL Azure で定義されたスキーマに 6 つの列があります。 その場合、ソースの CSV ファイル内の 6 つの列が入った行は正常にコピーされる一方で、その他の数の列が入った行は互換性のない行としてスキップされます。

- **リレーショナル データべースに書き込む際の主キー違反**

    例: SQL Server から Azure SQL Database にコピーするときに、シンクの Azure SQL Database に主キーが定義されているものの、ソースの SQL Server にはそのような主キーが定義されていません。 ソースに存在可能な重複した行はシンクに書き込む際には許可されません。 コピー アクティビティは、その最初の行のみをシンクにコピーし、2 番目以降の主キーが重複する行はスキップされます。

## <a name="configuration"></a>構成
次の例は、コピー アクティビティで互換性のない行をスキップするように構成する方法に関する JSON 定義を提供します。

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | コピー中に互換性のない行のスキップを有効にするかどうか。 | True<br/>False (既定値) | いいえ |
| redirectIncompatibleRowSettings | 互換性のない行をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ |
| linkedServiceName | スキップされるすべての行が含まれるログを格納する Azure Storage のリンク サービス。 | [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) または [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) のリンクされたサービスの名前を指定します。これは、ログ ファイルの格納に使用する Storage のインスタンスです。 | いいえ |
| path | スキップされるすべての行が含まれるログ ファイルのパス。 | 互換性のないデータをログに記録する Blob Storage のパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ |

## <a name="monitoring"></a>監視
コピー アクティビティの実行が完了した後、監視セクションでスキップされた行の数を次のように確認できます。

![互換性のない行のスキップの監視](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

互換性のない行をログに記録するように構成した場合、スキップされた行と互換性のない根本原因を次のパスでログ ファイルから確認できます。`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`

ログ ファイルには元のデータと対応するエラーが記録されています。 ログ ファイルの内容の例は次のとおりです。
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティについて詳しくは、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」をご覧ください。
