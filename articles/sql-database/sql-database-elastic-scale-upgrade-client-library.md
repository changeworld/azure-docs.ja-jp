<properties 
	pageTitle="最新の Elastic Database クライアント ライブラリへのアップグレード" 
	description="PowerShell と C# を使用したアップグレード手順" 
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
	ms.date="05/17/2015" 
	ms.author="sidneyh" />

# 最新の Elastic Database クライアント ライブラリへのアップグレード

Elastic Database クライアント ライブラリの新しいバージョンが [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) と Visual Studio の NuGetPackage マネージャーのインターフェイスから利用できるようになりました。アップグレードにはバグ修正が含まれており、クライアント ライブラリの新機能をサポートします。

## アップグレードの手順

アップグレードでは、新しいライブラリを使用してアプリケーションを再構築し、新しい機能をサポートするために、Azure SQL データベースに格納されている既存のシャード マップ マネージャーのメタデータを変更する必要があります。

次のシーケンスに従って、アプリケーション、シャード マップ マネージャーのデータベース、各シャードにおけるローカルのシャード マップ マネージャーのメタデータをアップグレードします。この順序でアップグレードの手順を実行することで、メタデータ オブジェクトが更新される場合に古いバージョンのクライアント ライブラリが環境内に存在しないようにします。つまりアップグレード後には古いバージョンのメタデータ オブジェクトは作成されません。

**1.アプリケーションをアップグレードします。** Visual Studio で、最新のクライアント ライブラリのバージョンをダウンロードして、ライブラリを使用する開発プロジェクトのすべてに参照させます。その後、再構築して展開します。

 * Visual Studio ソリューションで **[ツール]**、**[NuGet パッケージ マネージャー]**、**[ソリューション用 NuGet パッケージの管理]** の順に選択します。 
 * 左側のパネルで **[更新]** を選択してから、ウィンドウに表示されるパッケージ **Azure SQL データベース Elastic Scale クライアント ライブラリ**の **[更新]** ボタンを選択します。![Nuget パッケージのアップグレード][1]
 
 * 構築して展開します。

**2.スクリプトをアップグレードします。** シャードの管理に **PowerShell** スクリプトを使用している場合は、[新しいライブラリ バージョンをダウンロード](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)して、スクリプトを実行するディレクトリにコピーします。

**3.Split-Merge サービスをアップグレードします。** Elastic Database 分割/マージ ツールを使用してシャード化されたデータを再編成する場合は、[最新バージョンのツールをダウンロードして展開](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)します。サービスの詳しいアップグレード手順については、[こちら](sql-database-elastic-scale-overview-split-and-merge.md)をご覧ください。

**4.シャード マップ マネージャーのデータベースをアップグレードします。**Azure SQL データベースでシャード マップをサポートするメタデータをアップグレードします。これは、PowerShell か C# を使用して実行できます。次に両方の方法について説明します。

***オプション 1: PowerShell を使用してメタデータをアップグレードする***

1. [こちら](http://nuget.org/nuget.exe)から NuGet の最新のコマンド ライン ユーティリティをダウンロードして、フォルダーに保存します。 

2. コマンド プロンプトを開き、同じフォルダーに移動して、次のコマンドを発行します。`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. ダウンロードした新しいクライアントの DLL バージョンを含むサブフォルダーに移動します。例:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. [スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)から Elastic Database クライアントのアップグレード スクリプトレットをダウンロードして、DLL が含まれる同じフォルダーに保存します。

5. そのフォルダーのコマンド プロンプトから"PowerShell.\\upgrade.ps1"を実行し、画面の指示に従います。
 
***オプション 2: C# を使用してメタデータをアップグレードする***

または、次の例のように、メソッド [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) と [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) を呼び出して、ShardMapManager を開き、すべてのシャードを反復し、メタデータのアップグレードを実行する Visual Studio アプリケーションを作成します。

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

このメタデータのアップグレードの手法は問題なく何度も適用できます。たとえば古いクライアント バージョンで、シャードをすでに更新した後に不注意にシャードを作成してしまった場合、すべてのシャードで再びアップグレードを実行し、最新のメタデータ バージョンがインフラストラクチャ全体に存在するようにできます。

**注:** 今まで公開されたクライアント ライブラリの新しいバージョンは、Azure SQL DB の古いバージョンのシャード マップ マネージャー メタデータで (逆の場合も同様) 引き続き操作できます。ただし、最新のクライアントで一部の新機能を活用するには、メタデータをアップグレードする必要があります。メタデータのアップグレードはユーザー データやアプリケーション専用データに影響することはありません。シャード マップ マネージャーで作成し、使用したオブジェクトのみに影響します。アプリケーションは以下に説明するアップグレード シーケンスを通じて引き続き操作できます。

## Elastic Database クライアントのバージョン履歴 

**バージョン 1.0 - 2015 年 4 月**

* 一般公開リリース
* シャーディング キーとして Datetime 型のサポートが追加されました。

**バージョン 0.8 – 2015 年 3 月**

* 新しい ShardMap.OpenConnectionForKeyAsync メソッドのデータ依存型ルーティングに非同期サポートが追加されました。 
* パブリックの KeyType プロパティが ShardMap に追加されました。 
* データベースの復元サポートの改善とシャード用の災害復旧のシナリオが追加されました。 

**バージョン 0.7 – 2014 年 10 月**

初期プレビュー バージョン


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 

<!---HONumber=July15_HO3-->