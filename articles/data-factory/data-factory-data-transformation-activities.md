<properties 
	pageTitle="データ変換のアクティビティ | Microsoft Azure"
	description="Azure Data Factory サービスを使用してデータを変換、分析する方法について説明します。"
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
	ms.date="07/26/2015"
	ms.author="spelluru"/>

# Azure Data Factory を使用した変換と分析
Azure Data Factory の変換アクティビティにより、未加工のデータを変換して処理することで、予測や把握が容易になります。変換アクティビティは、Azure HDInsight クラスターや Azure Batch などのコンピューティング環境で実行されます。Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態で[パイプライン](data-factory-create-pipelines.md)に追加できます。


変換アクティビティ | Compute 環境 
----------------------- | --------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](https://msdn.microsoft.com/library/mt185698.aspx) | HDInsight [Hadoop]
[Machine Learning Batch Scoring](data-factory-create-predictive-pipelines.md) | Azure VM 
[ストアド プロシージャ](data-factory-stored-proc-activity.md) | Azure SQL | 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] または Azure Batch    

変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用する必要があります。Data Factory でサポートされているコンピューティング環境は 2 種類あります。

1. **オンデマンド**: この場合、コンピューティング環境は Data Factory で完全に管理されます。環境はデータを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
2. **Bring Your Own**: この場合、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。 

Data Factory でサポートされている Compute がリンクされたサービスについては、[Compute がリンクされたサービス](data-factory-compute-linked-services.md)に関する記事を参照してください。

<!---HONumber=August15_HO9-->