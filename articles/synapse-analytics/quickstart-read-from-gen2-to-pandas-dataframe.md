---
title: 'クイックスタート: ADLS Gen2 から Pandas データフレームにデータを読み取る'
description: Azure Synapse Analytics の Synapse Studio で Python を使用して、Azure Data Lake Storage Gen2 アカウントから Pandas データフレームにデータを読み取ります。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969578"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>クイックスタート: Azure Synapse Analytics で ADLS Gen2 から Pandas データフレームにデータを読み取る

このクイックスタートでは、Python を使って Azure Data Lake Storage (ADLS) Gen2 から Azure Synapse Analytics の Pandas データフレームにデータを読み取る簡単な方法について説明します。

Synapse Studio ノートブックから、次のことを行います。

- Azure Synapse Analytics ワークスペースにリンクされている Data Lake Storage Gen2 内のコンテナーに接続する
- `spark.read.load` を使用して PySpark ノートブックからデータを読み取る
- `.toPandas()` を使用して、データを Pandas データフレームに変換する

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。
- Data Lake Storage Gen2 が既定のストレージとして構成されている Synapse Analytics ワークスペース - 使用する Data Lake Storage Gen2 ファイルシステムの **ストレージ BLOB データ共同作成者** である必要があります。 ワークスペースの作成方法の詳細については、「[Synapse ワークスペースの作成](get-started-create-workspace.md)」を参照してください。
- ワークスペース内の Apache Spark プール - 「[サーバーレス Apache Spark プールを作成する](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)」を参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="upload-sample-data-to-adls-gen2"></a>サンプル データを ADLS Gen2 にアップロードする

1. Azure portal で、Synapse Studio で使用されるのと同じ Data Lake Storage Gen2 内にコンテナーを作成します。 Azure Synapse Analytics ワークスペース内にリンクされた既定のストレージ アカウントを使用する場合は、この手順を省略できます。

1. Synapse Studio で **[データ]** をクリックし、 **[リンク済み]** タブを選択して、 **[Azure Data Lake Storage Gen2]** でコンテナーを選択します。

1. サンプル ファイル [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) をダウンロードし、コンテナーにアップロードします。

1. アップロードしたファイルを選択し、 **[プロパティ]** をクリックして、**ABFSS パス** の値をコピーします。

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>ADLS Gen2 から Pandas データフレームにデータを読み取る

1. 左側のペインで、 **[開発]** をクリックします。

1. **[+]** をクリックし、[ノートブック] を選択して新しいノートブックを作成します。

1. **[アタッチ先]** で、お使いの Apache Spark プールを選択します。 ない場合は、 **[Apache Spark プールの作成]** をクリックします。

1. ノートブックのコード セルに、次の Python コードを貼り付け、先ほどコピーした ABFSS パスを挿入します。

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. セルを実行します。

数分後、次のようなテキストが表示されます。

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics とは](overview-what-is.md)
- [Azure Synapse Analytics の使用を開始する](get-started.md)
- [サーバーレス Apache Spark プールを作成する](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
