---
title: SQL Data Warehouse を使用した統合ソリューションの作成 | Microsoft Docs
description: 'SQL Data Warehouse と統合されたソリューションを持つツールとパートナー。 '
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 221d5d05906e7e162013c0d4cdddc01a95f4024c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302704"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>他のサービスを SQL Data Warehouse と統合する
SQL Data Warehouse では、コア機能に加えて Azure 内の他のサービスの多くを統合できます。 次のようなサービスがあります。

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse と統合できる Azure のサービスおよび[統合パートナー](sql-data-warehouse-partner-data-integration.md)は増え続けています。

## <a name="power-bi"></a>Power BI
Power BI との統合によって、SQL Data Warehouse のコンピューティング パワーを、Power BI の動的なレポート作成や視覚化と組み合わせることができます。 現在、Power BI との統合には次のものが含まれています。

* **Direct Connect**: SQL Data Warehouse に対する論理的プッシュダウンを備えた、さらに高度な接続です。 プッシュダウンにより、分析の速度と規模を高めることができます。
* **Power BI で開く**: [Power BI で開く] ボタンは、簡単に接続できるように Power BI にインスタンス情報を渡します。

詳しくは、[Power BI との統合](sql-data-warehouse-get-started-visualize-with-power-bi.md)に関する記事、または[Power BI のドキュメント](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)をご覧ください。

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory は、抽出と読み込みの複雑なパイプラインを作成するための管理されたプラットフォームを提供します。 SQL Data Warehouse と Azure Data Factory の統合には、次が含まれます。

* **ストアド プロシージャ**: SQL Data Warehouse でのストアド プロシージャの実行を調整します。
* **コピー**: ADF を使用して、SQL Data Warehouse にデータを移動します。 この操作では、内部で ADF の標準的なデータ移行メカニズムまたは PolyBase を使用できます。 

詳しくは、[Azure Data Factory との統合](sql-data-warehouse-get-started-visualize-with-power-bi.md)に関する記事をご覧ください。

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning は、フル マネージドの分析サービスで、大規模な予測ツール セットを使用する複雑なモデルを作成できます。 SQL Data Warehouse は、次の機能により、これらのモデルのソースと変換先の両方としてサポートされています。

* **データの読み取り** : T-SQL を使用して、SQL Data Warehouse に対して大規模にモデルを駆動します。
* **データの書き込み** : 任意のモデルから SQL Data Warehouse に変更を戻します。

詳しくは、[Azure Machine Learning との統合](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)に関する記事をご覧ください。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics は、Azure Event Hub で生成されたイベント データの処理と使用のための、複雑でフル マネージドのインフラストラクチャです。  SQL Data Warehouse との統合により、ストリーミング データを効率的に処理でき、さらに、関連するデータと並行して格納できることにより、より深く高度な分析が可能になります。  

* **ジョブの出力** : Stream Analytics ジョブから SQL Data Warehouse に直接出力を送信します。

詳しくは、[Azure Stream Analytics との統合](sql-data-warehouse-integrate-azure-stream-analytics.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順
Azure SQL Database との統合については、[SQL Database のエラスティック クエリの構成](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)に関する記事をご覧ください

