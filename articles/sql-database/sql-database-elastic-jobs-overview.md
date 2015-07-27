<properties 
	pageTitle="エラスティック データベース ジョブの概要" 
	description="エラスティック データベース ジョブ サービスの説明" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Elastic Database ジョブの概要

**Elastic Database ジョブ** (プレビュー) を使用すると、[エラスティック データベース プール (プレビュー)](sql-database-elastic-pool.md) のすべてのデータベースに対して T-SQL スクリプト (ジョブ) を実行できます。たとえば、すべてのデータベース内のスキーマを簡単に更新して新しいテーブルを含めることができます。通常、T-SQL ステートメントを実行するか、その他の管理タスクを実行するには、各データベースに個別に接続する必要があります。**Elastic Database ジョブ**は、各データベースの実行の状態のログ記録中に、ログインとスクリプトの実行タスクを処理します。プレビューをインストール手順については、「[Elastic Database ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。

![エラスティック データベース ジョブ][1]

## メリット

* エラスティック データベース プールで実行される T-SQL スクリプトの定義、保持、保存
* 自動再試行で、T-SQL スクリプトを大規模に確実に実行する
* ジョブの実行状態の追跡

## シナリオ

* 新しいスキーマのデプロイなどのパフォーマンスの管理タスク
* すべてのデータベース間の共通の製品情報などの参照データを更新する
* インデックスを再構築してクエリのパフォーマンスを向上させる

## ジョブの動作のしくみ

1.	エラスティック データベース ジョブで使用されるサービスをインストールする「[Elastic Database ジョブのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。インストールが失敗した場合は、「[アンインストールする方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。
2.	[各データベースにユーザーを追加して](sql-database-elastic-jobs-add-logins-to-dbs.md)ジョブ実行の エラスティック データベース プールを構成します。
3.	プール内のすべてのデータベースに対して実行できるべき等 T-SQL スクリプトを作成します。
4.	「[Elastic Database ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」の手順に従い、スクリプトを実行します。 

## コンポーネントと価格 

次のコンポーネントは、連携して管理ジョブのアドホック実行を可能にする Azure クラウド サービスを作成します。コンポーネントはサブスクリプションで、セットアップ時に自動的にインストールされ、構成されます。これらはすべて同じ自動生成された名前を持っているため、サービスを識別できます。名前は一意であり、プレフィックス "edj" とその後に続くランダムに生成された 21 文字で構成されます。

* **Azure Cloud Service**: エラスティック データベース ジョブ (プレビュー) は、顧客によってホストされる AzureCloud Service として配信され、要求されたタスクを実行します。ポータルから、サービスは Microsoft Azure サブスクリプションにデプロイされ、ホストされます。デプロイされる既定のサービスは、高可用性のための 2 つの worker ロールの最小値で実行されます。各 worker ロール (ElasticDatabaseJobWorker) の既定のサイズは A0 インスタンスで実行されます。料金については、「[Cloud Services 料金](http://azure.microsoft.com/pricing/details/cloud-services/)」をご覧ください。 
* **Azure SQL Database**: このサービスは、**管理データベース**と呼ばれる Azure SQL Database を使用してすべてのジョブ メタデータを保持します。既定のサービス層は、S0 です。詳細については、「[SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。
* **Azure Service Bus**: Azure Service Bus は、Azure Cloud Service 内の作業を調整します。「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。
* **Azure Storage**: Azure ストレージ アカウントは問題にさらにデバッグが必要な場合に、診断出力のログ記録を格納するために使用されます ([Azure 診断](../cloud-services-dotnet-diagnostics.md)の一般的な方法)。料金については、「[Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

## 次のステップ
[コンポーネントをインストールし](sql-database-elastic-jobs-service-installation.md)、[プール内の各データベースにログインを作成し、追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。ジョブの作成と管理の詳細については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO2-->