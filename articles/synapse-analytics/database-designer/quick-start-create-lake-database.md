---
title: Azure Synapse Lake データベースとデータベース テンプレートのクイック スタート
description: Synapse Lake データベースとデータベース テンプレートの使用方法に関するクイック スタート
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 4db8799afdaf4a69278ce40c76baf05ab0eb1eee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467190"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>クイック スタート: データベース テンプレートを利用して新しい Lake データベースを作成する

このクイック スタートでは、データベース テンプレートを適用して Lake データベースを作成し、新しいモデルにデータを配置し、統合エクスペリエンスを使用してデータを分析する方法について、エンドツーエンドのシナリオを実行できます。 

## <a name="prerequisites"></a>前提条件
- ギャラリーから lake データベース テンプレートを探索するには、少なくとも Synapse User ロールの権限が必要です。
- Synapse ワークスペースで lake データベースを作成するには、Synapse 管理者または Synapse 共同作成者の権限が必要です。
- データ レイクでは、ストレージ BLOB データ共同作成者のアクセス許可が必要です。

## <a name="create-a-lake-database-from-database-templates"></a>データベース テンプレートから Lake データベースを作成する

新しいデータベース テンプレート (プレビュー) 機能を使用して、データベースのデータ モデルを構成するために使用できる Lake データベースを作成します。 

このシナリオでは、小売データベース テンプレートを使用し、次のエンティティを選択します。 
 - **RetailProduct** - 製品とは、潜在顧客のニーズを満たす可能性のある市場に提供できるものすべてを指します。 製品に関連付けられた物理的、心理的、象徴的、およびサービスの属性すべてがその製品に含まれます。
 - **Transaction** - 実行可能な作業または顧客アクティビティの最低レベルです。
トランザクションは、1 つ以上の個別のイベントで構成されます。
 - **TransactionLineItem** - Product と Quantity に分類されるトランザクションのコンポーネントです (項目ごとに 1 つ)。
 - **Party** - パーティとは、ビジネスの目的となる個人、組織、法人、ソーシャル組織、または事業単位です。
 - **Customer** - 顧客は、製品またはサービスを所有する、または購入した個人または法人です。
 - **Channel** - チャネルは、製品またはサービスが販売または配布される手段です。
 - 
これらを見つける最も簡単な方法は、テーブルを含むさまざまなビジネス領域の上にある検索ボックスを使用することです。 
 
![データベース テンプレートの例](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Lake データベースを構成する
 
データベースを作成したら、ストレージ アカウントとファイルパスが、データを格納する場所に設定されていることを確認します。 パスは、Synapse Analytics 内のプライマリ ストレージ アカウントに既定で設定されますが、必要に応じて変更できます。 
  
 ![Lake データベースの例](./media/quick-start-create-lake-database/lake-database-example.png)
 
レイアウトを保存し、Synapse 内で使用可能にするには、すべての変更を発行します。 この手順により、Lake データベースのセットアップが完了し、Synapse Analytics の内部および外部で利用できるようになります。 

## <a name="ingest-data-to-lake-database"></a>Lake データベースにデータを取り込む

Lake データベースにデータを取り込むには、データをデータベース テーブルに直接読み込むための [ワークスペース DB](../data-integration/data-integration-data-lake.md) コネクタを持つ **パイプライン** を実行できます。これには、コーディングなしのデータ フロー マッピングが使用されます。 対話型の Spark ノートブックを使用して、Lake データベース テーブルにデータを取り込むこともできます。

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>データにクエリを実行する

Lake データベースを作成した後、データに対してクエリを実行するさまざまな方法があります。 現在、Synapse 内での SQL-Ondemand をサポートしています。ここでは、新しく作成された Lake データベース形式を自動的に理解し、それを介してデータが公開されます。 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

Synapse 内でデータにアクセスするもう 1 つの方法は、新しい Spark ノートブックを開き、そこで統合エクスペリエンスを使用することです。

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>機械学習モデルをトレーニングする

Lake データベースを使用して機械学習モデルをトレーニングし、データをスコア付けすることができます。 詳細については、「[機械学習モデルをトレーニングする](../machine-learning/tutorial-automl.md)」を参照してください 

## <a name="next-steps"></a>次のステップ

次のリンクを使用して、データベース デザイナーの機能の詳細を引き続き確認します。
 - [データベース テンプレートの概念](concepts-database-templates.md)
 - [Lake データベースの概念](concepts-lake-database.md)
 - [Lake データベースを作成する方法](create-empty-lake-database.md)
