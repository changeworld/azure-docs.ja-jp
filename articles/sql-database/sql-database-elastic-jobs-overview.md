<properties 
	title="Elastic database jobs overview" 
	pageTitle="エラスティック データベース ジョブの概要" 
	description="エラスティック データベース ジョブ サービスの説明" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# エラスティック データベース ジョブの概要

**エラスティック データベース ジョブ**機能 (プレビュー) を使用すると、カスタム定義のデータベース コレクション、[エラスティック データベース プール (プレビュー)](sql-database-elastic-pool.md)、または ([エラスティック データベース クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して作成された) シャード セットなど、データベース グループ全体に対して、確実に Transact-SQL (T-SQL) スクリプトを実行し、DACPAC を適用することができます。現在、プレビューでは、**エラスティック データベース ジョブ**は顧客がホストする Azure クラウド サービスです。ジョブと呼ばれるアドホック タスクとスケジュールされた管理タスクを実行できます。この機能を使用すると、スキーマの変更、資格情報の管理、参照データの更新、パフォーマンス データの収集、テナント (顧客) 製品利用統計情報の収集など、管理操作を実行する Transact-SQL スクリプトを実行して、データベース グループ全体の規模で Azure SQL Database を簡単かつ確実に管理できます。通常、Transact-SQL ステートメントを実行するか、その他の管理タスクを実行するには、各データベースに個別に接続する必要があります。**エラスティック データベース ジョブ**は、各データベースの実行の状態のログ記録中に、ログインとスクリプトの実行タスクを処理します。インストール手順については、「[エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。

![エラスティック データベース ジョブ][1]

## メリット
* Azure SQL Database のカスタム グループを定義する
* Azure SQL Database のグループに対して実行される Transact-SQL スクリプトの定義、保守、維持 
* データ層アプリケーション (DACPAC) をデプロイする
* 自動再試行を使用して大規模な DACPAC の Transact-SQL スクリプト アプリケーションを確実に実行する
* ジョブの実行状態の追跡
* 実行スケジュールを定義する
* Azure SQL Database のコレクション全体に対してデータ収集を実行して、結果を 1 つの対象テーブルに保存する

> [AZURE.NOTE]Azure ポータルの**エラスティック データベース ジョブ**機能は機能セットが制限されており、SQL Azure エラスティック プールに限定されています。現在のすべての機能にアクセスするには、PowerShell API を使用してください。

## シナリオ

* 新しいスキーマのデプロイなどのパフォーマンスの管理タスク
* 営業時間の終了後に更新が毎週自動実行されるスケジュールも使用して、すべてのデータベース全体で共通の製品情報など、参照データを更新します。
* インデックスを再構築して、クエリのパフォーマンスを向上します。ピーク時以外の時間などに、データベース コレクション全体に対して定期的に実行するように再構築を構成できます。
* データベース セットのクエリ結果をリアルタイムで中央のテーブルに収集します。パフォーマンス クエリを継続的に実行して、その他のタスクの実行をトリガーするように構成できます。
* 顧客の製品利用統計情報収集など、大規模なデータセット全体に対して、時間がかかるデータ処理クエリを実行します。結果は 1 つの対象テーブルに収集され、分析に使用されます。

## エラスティック データベース ジョブの簡単なエンドツーエンドの確認
1.	**エラスティック データベース ジョブ** コンポーネントをインストールします。詳細については、「[エラスティック データベース ジョブのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。インストールが失敗した場合は、「[アンインストールする方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。
2.	カスタム定義のデータベース コレクションの作成、スケジュールの追加、結果セットの収集など、その他の機能にアクセスするには、PowerShell API を使用します。**エラスティック データベース プール**に対して実行するように制限されたジョブの簡単なインストール、作成、監視の場合は、ポータルを使用します。 
3.	ジョブ実行用に暗号化された資格情報を作成し、[グループ内の各データベースにユーザー (またはロール) を追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。
4.	グループ内のすべてのデータベースに対して実行できるべき等 T-SQL スクリプトを作成します。
5.	「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」の手順に従い、スクリプトを実行します。 

## コンポーネントと価格 
次のコンポーネントは、連携して管理ジョブのアドホック実行を可能にする Azure クラウド サービスを作成します。コンポーネントはサブスクリプションで、セットアップ時に自動的にインストールされ、構成されます。これらはすべて同じ自動生成された名前を持っているため、サービスを識別できます。名前は一意であり、プレフィックス "edj" とその後に続くランダムに生成された 21 文字で構成されます。

* **Azure Cloud Service**: エラスティック データベース ジョブ (プレビュー) は、顧客によってホストされる AzureCloud Service として配信され、要求されたタスクを実行します。ポータルから、サービスは Microsoft Azure サブスクリプションにデプロイされ、ホストされます。デプロイされる既定のサービスは、高可用性のための 2 つの worker ロールの最小値で実行されます。各 worker ロール (ElasticDatabaseJobWorker) の既定のサイズは A0 インスタンスで実行されます。料金については、「[Cloud Services 料金](http://azure.microsoft.com/pricing/details/cloud-services/)」をご覧ください。 
* **Azure SQL Database**: このサービスは、**管理データベース**と呼ばれる Azure SQL Database を使用してすべてのジョブ メタデータを保持します。既定のサービス層は、S0 です。詳細については、「[SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。
* **Azure Service Bus**: Azure Service Bus は、Azure Cloud Service 内の作業を調整します。「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。
* **Azure Storage**: Azure ストレージ アカウントは問題にさらにデバッグが必要な場合に、診断出力のログ記録を格納するために使用されます ([Azure 診断](../cloud-services-dotnet-diagnostics.md)の一般的な方法)。料金については、「[Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

## エラスティック データベース ジョブの機能
1.	Azure SQL Database には、すべてのメタデータと状態データが保存される管理データベースが指定されています。
2.	実行するジョブの開始と追跡のために、**エラスティック データベース ジョブ**から管理データベースにアクセスされます。
3.	次の 2 種類のロールが管理データベースと通信します。 
	* コントローラー: 要求されたジョブを実行するタスクが必要なジョブを決定し、新しいジョブ タスクを作成して失敗したジョブを再試行します。
	* ジョブ タスク実行: ジョブ タスクを実行します。

### ジョブ タスクの種類
ジョブを実行するジョブ タスクの種類は複数あります。

* ShardMapRefresh: シャード マップに対してクエリを実行して、シャードとして使用されているすべてのデータベースを決定します
* ScriptSplit: ‘GO’ ステートメント全体のスクリプトを複数のバッチに分割します
* ExpandJob: データベース グループを対象とするジョブから各データベースの子ジョブを作成します
* ScriptExecution: 定義済みの資格情報を使用して、特定のデータベースに対してスクリプトを実行します
* Dacpac: 特定の資格情報を使用して、DACPAC を特定のデータベースに適用します

## エンドツーエンドの実行ワークフロー
1.	ポータルまたは PowerShell API を使用して、ジョブを**管理データベース**に挿入します。ジョブから、特定の資格情報を使用してデータベース グループに対して Transact-SQL スクリプトの実行が要求されます。
2.	コントローラーで、新しいジョブが識別されます。スクリプトを分割し、グループのデータベースを更新するジョブ タスクが作成され、実行されます。最後に、ジョブを展開し、新しい子ジョブを作成する新しいジョブが作成され、実行されます。各子ジョブは、グループ内の個々のデータベースに対して Transact-SQL スクリプトを実行するように指定されています。
3.	コントローラーで、作成された子ジョブが識別されます。コントローラーで、ジョブごとにデータベースに対してスクリプトを実行するジョブ タスクが作成され、トリガーされます。 
4.	すべてのジョブのタスクが完了すると、コントローラーのジョブの状態は完了に更新されます。ジョブ実行中の任意の時点で、ジョブ実行の現在の状態を確認する PowerShell API を使用できます。PowerShell API から返されるすべての時刻は、UTC 形式です。必要に応じて、取り消し要求を開始してジョブを停止できます。 

## 次のステップ
[コンポーネントをインストールし](sql-database-elastic-jobs-service-installation.md)、[データベース グループ内の各データベースにログインを作成し、追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。ジョブの作成と管理の詳細については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->