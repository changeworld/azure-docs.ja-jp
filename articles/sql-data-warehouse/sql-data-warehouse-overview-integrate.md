<properties
   pageTitle="SQL Data Warehouse を使用した統合ソリューションの作成 | Microsoft Azure"
   description="SQL Data Warehouse と統合されたソリューションを持つツールとパートナー。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

#SQL Data Warehouse とサービスの統合
SQL Data Warehouse では、コア機能に加えて Azure 内の他のサービスの多くを並行して活用できます。具体的には、現在のところ、次のサービスと密接に統合するための措置を講じています。

+ Power BI
+ Azure Data Factory
+ Azure Machine Learning
+ Azure Stream Analytics

これらに加えて、Azure エコシステムで他の多くのサービスとより緊密に提携するための作業を行っています。

##Power BI
Power BI の統合によって、ユーザーは SQL Data Warehouse のコンピューティング パワーを、Power BI の動的なレポート作成や視覚化と共に、大いに活用することができます。現在、Power BI との統合は次のとおりです。

+ **Direct Connect**: SQL Data Warehouse に対する論理的プッシュダウンを備えた、さらに高度な接続です。これにより、迅速な分析を大規模に行えます。
+ **Power BI で開く**: ［Power BI で開く］ ボタンは、Power BI にインスタンス情報を渡し、よりシームレスな接続を可能にします。 

詳細については、「[SQL Data Warehouse での Power BI の使用](../sql-data-warehouse-integrate-power-bi.md)」または「[Power BI documentation (Power BI ドキュメント)](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)」をご覧ください。

##Azure Data Factory
Azure Data Factory は、複雑な抽出、変換、読み込みのパイプラインを作成するための管理されたプラットフォームを提供します。SQL Data Warehouse と Azure Data Factory の統合には、次が含まれます。

+ **データ移動**: 多数のオンプレミスと Azure サービスにわたるデータ転送をスケジュールします。
+ **ストアド プロシージャ**: SQL Data Warehouse でのストアド プロシージャの実行を調整します。

詳細については、「[SQL Data Warehouse での Azure Data Factory の使用](../sql-data-warehouse-integrate-azure-data-factory.md)」または「[Data Factory のドキュメント](https://azure.microsoft.com/documentation/services/data-factory/)」をご覧ください。

##Azure Machine Learning
Azure Machine Learning は、完全に管理されたを分析サービスで、大規模な予測ツール セットを活用する複雑なモデルを作成できます。SQL Data Warehouse は、次の機能により、これらのモデルのソースと変換先の両方としてサポートされています。

+ **データの読み取り**: T-SQL を使用して、SQL Data Warehouse に対して大規模にモデルを駆動します。 
+ **データの書き込み**: 任意のモデルから SQL Data Warehouse に変更を戻します。

詳細については、「[SQL Data Warehouse での Azure Machine Learning の使用](../sql-data-warehouse-integrate-azure-machine-learning.md)」または「[Machine Learning documentation (Machine Learning ドキュメント)](https://azure.microsoft.com/services/machine-learning/)」をご覧ください。

##Azure Stream Analytics
Azure Stream Analytics は、Azure Event Hub で生成されたイベント データの処理と使用のための、複雑で完全に管理されたインフラストラクチャです。SQL Data Warehouse との統合により、ストリーミング データを効率的に処理でき、さらに、関連するデータと並行して格納できることにより、より深く高度な分析が可能になります。

+ **[ジョブの出力**: Stream Analytics ジョブから SQL Data Warehouse に直接出力を送信します。

詳細については、「[SQL Data Warehouse での Azure Stream Analytics の使用](../sql-data-warehouse-integrate-azure-stream-analytics.md)」または「[Stream Analytics のドキュメント](https://azure.microsoft.com/documentation/services/stream-analytics/)」をご覧ください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->