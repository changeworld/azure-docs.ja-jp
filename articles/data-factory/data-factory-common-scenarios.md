<properties 
	pageTitle="Azure Data Factory を使用する一般的なシナリオ" 
	description="Azure Data Factory サービスを使用するいくつかの一般的なシナリオについて説明します" 
	services="data-factory" 	
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Azure Data Factory を使用する一般的なシナリオ
このセクションでは、Azure Data Factory が現在サポートしているシナリオの例をいくつか紹介します。これらは今後、ハブのシナリオとして引き続き発展していく予定です。

> [AZURE.NOTE]この記事を読み進める前に、[Azure Data Factory の概要][datafactory-introduction]に関するページに目を通してください。

##シナリオ #1: データ ソースをデータ ハブに集約する
![データ ハブからデータを取得する][image-data-factory-introduction-secenario1-source-datahub]

企業が所有するデータは多種多様で、ソースもさまざまです。情報生成システム構築の最初のステップは、SaaS サービス、ファイル共有、FTP、Web サービスなど、必要なすべてのデータ ソースと処理の機能に接続し、必要に応じて後続の処理機能にデータを移動させることです。

Data Factory を使用していない企業では、カスタムのデータ移動コンポーネントをビルドするか、これらのデータ取得と処理を統合するカスタム サービスを作成しなければなりません。このような作業にはコストがかかり、システムの統合や保守が容易ではありません。エンタープライズ クラスの監視やアラート、完全に管理されたサービスが提供できるような制御機能は、多くの場合望めません。
  
Azure Data Factory を使用すると、データの保存と処理のサービスはハブ コンテナーに集約され、コンピューティングと保存のアクティビティが円滑化、最適化されて、リソース消費の統合管理が可能になり、必要に応じてデータ移動のサービスを利用できます。

##シナリオ #2: 情報生成を運用可能にする
運用のシナリオは、データ取得シナリオの次に進むべきステップです。データがハブに集まったら、データ パイプラインを作成し、運用する必要があります。これにより、保守しやすい管理されたスケジュールで、信頼性の高い方法でデータ変換を実行し、信頼できるデータを運用環境に提供できます。Azure Data Factory のデータ変換は、Hadoop (Azure HDInsight) 上で実行される Hive、Pig、およびカスタム C# 処理を通じて行われます。このような変換を使用すると、データを消去したり、重要なデータ フィールドを隠すなど、さまざまな複雑な方法でデータを操作することができます。通常、運用はインフラストラクチャやカスタム サービスを維持するための複雑性と困難を伴って達成され、 ソリューションなどの実装、管理、拡張、トラブルシューティング、バージョン管理に対して多くの課題をもたらします。
  
Data Factory を完全に管理されたサービスとして使用すれば、ユーザーはパイプラインを運用する際に 1 回限りのスケジュールや複合的な定期スケジュールで定義するだけでよく、調整は Data Factory サービスが直接対応します。ハブは、ユーザーに代わってすべてのデータのクラスター管理とハブ内部の処理を行うため、ユーザーはインフラストラクチャ管理よりも変革に関する分析に集中できます。Azure Data Factory は脆弱なカスタム サービスへの対応の課題を解消し、企業が信頼できる情報を確実に再現可能な方法で生成できるようにします。


##シナリオ #3: 情報生成とデータ探索を統合する
BI に関する従来のアプローチとテクノロジは、"信頼できる情報源" を提供する一方で、ほとんどの場合、変更要求プロセスが慎重に制御されているために要求のバックログが絶えず発生するという重大な副作用が付きものです。絶えず変化するビジネス上の疑問に対応するには、企業が情報利用システムから情報生成システムに接続できる優れた柔軟性が必要です。Azure Data Factory は、情報生成向けに効率化されたデータ パイプラインを使用してこれらのシステムへの接続に関する課題に対処し、使用しやすい形式で信頼できる最新のデータを提供して情報利用の課題を解決します。
  
Azure Data Factory は、生成されたデータをシンプルに利用できるように、次の機能をサポートしています。

- 既存の BI ツール (Excel や Tableau など) で利用できるように、生成されたデータ資産をリレーショナル データ マートに (1 回だけ、またはスケジュールを設定して) 簡単に移動。
- Data Factory によって生成されたデータ資産を Excel の Power Query から直接利用。

Power Query からのデータの利用については、次のトピックを参照してください。

- [Power Query: Microsoft Azure テーブル ストレージに接続する][Power-Query-Azure-Table]
- [Power Query: Microsoft Azure BLOB ストレージに接続する][Power-Query-Azure-Blob]
- [Power Query: Microsoft Azure SQL Database に接続する][Power-Query-Azure-SQL]
- [Power Query: Microsoft オンプレミス SQL Server に接続する][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png



 

<!---HONumber=August15_HO6-->