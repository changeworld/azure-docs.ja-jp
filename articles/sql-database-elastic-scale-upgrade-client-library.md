<properties 
	title="Upgrade to the Latest Elastic Scale Client Library" 
	pageTitle="最新の Elastic Scale クライアント ライブラリへのアップグレード" 
	description="PowerShell と C# を使用したアップグレード手順" 
	metaKeywords="sharding,elastic scale, Azure SQL DB sharding" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="stuarto", "sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto" />

# 最新の Elastic Scale クライアント ライブラリへのアップグレード

Elastic Scale クライアント ライブラリの新しいバージョンが[NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) と Visual Studio の NuGetPackage マネージャーのインターフェイスから利用できるようになりました。アップグレードにはバグ修正が含まれており、Elastic Scale の新機能をサポートします。

## アップグレードの手順

アップグレードでは、新しいライブラリを使用してアプリケーションを再構築し、新しい機能をサポートするために、Azure SQL データベースに格納されている既存のシャード マップ マネージャーのメタデータを変更する必要があります。

次のシーケンスに従って、アプリケーション、シャード マップ マネージャーのデータベース、各シャードにおけるローカルのシャード マップ マネージャーのメタデータをアップグレードします。この順序でアップグレードの手順を実行することで、メタデータ オブジェクトが更新される場合に古いバージョンのクライアント ライブラリが環境内に存在しないようにします。つまりアップグレード後には古いバージョンのメタデータ オブジェクトは作成されません。   

**1. アプリケーションをアップグレードします。**Visual Studio で、最新のクライアント ライブラリのバージョンをダウンロードして、Elastic Scale のプロジェクトのすべてに参照させます。その後再構築して展開します。 

 * Visual Studio ソリューションで **[ツール]** --> **[NuGet Package Manager]** -->**[Manage NuGet Packages for Solution (ソリューションの NuGet パッケージを管理)]** と選択します。 
 * 左側のパネルで **[Updates (更新)]** を選択して、ウィンドウに表示されるパッケージ **Azure SQL データベース Elastic Scale クライアント ライブラリ** の **[Update (更新)]** ボタンを選択します。
	![Upgrade Nuget Pacakges][1]
 
 * 構築して展開します。 

**2. スクリプトをアップグレードします。**シャードの管理に、**PowerShell** スクリプトを使用している場合、[新しいライブラリ バージョン をダウンロード ](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) して、スクリプトを実行するディレクトリにコピーします。 

**3. Split-Merge サービスをアップグレードします。**Elastic Scale Split-Merge サービスを使用してシャード化されたデータを再編成する場合、[最新バージョンの Service をダウンロード](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/) して展開します。Service のアップグレード手順について詳しくは [こちら](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/) をご覧ください。 

**4. シャード マップ マネージャーのデータベースをアップグレードします。**.Azure SQL データベースでシャード マップをサポートするメタデータをアップグレードします。これは、PowerShell か C# を使用して実行できます。次に両方の方法について説明します。

***オプション 1:PowerShell を使ってメタデータをアップグレードします。***

1. [こちら](http://nuget.org/nuget.exe) から NuGet の最新のコマンド ラインをダウンロードして、フォルダーに保存します。 

2. コマンド プロンプトを開き、同じフォルダーに移動して、コマンドを発行します。
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. ダウンロードした新しいクライアントの DLL バージョンを含むサブフォルダーに移動します。例:
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.0.8.0\lib\net45`

4. [スクリプト センター](http://go.microsoft.com/?linkid=9876343) から Elastic Scale クライアントのアップグレード スクリプトレットをダウンロードして、DLL が含まれる同じフォルダーに保存します。

5. そのフォルダーのコマンド プロンプトから"PowerShell.\upgrade.ps1"を実行し、画面の指示に従います。
 
***オプション 2:C# を使ってメタデータをアップグレードします。***

または次の例のように、メソッド [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) と [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) を呼び出して、ShardMapManager を開き、すべてのシャードを反復し、メタデータのアップグレードを実行する Visual Studio アプリケーションを作成します。 

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

**注:** 今まで公開されたクライアント ライブラリの新しいバージョンは Azure SQL DB の古いバージョンのシャード マップ マネージャーで、そしてその逆でも引き続き操作できますが、最新のクライアントで一部の新機能を活用するには、メタデータをアップグレードする必要があります。メタデータのアップグレードはユーザー データやアプリケーション専用データに影響することはありません。シャード マップ マネージャーで作成し、使用したオブジェクトのみに影響します。アプリケーションは以下に説明するアップグレード シーケンスを通じて引き続き操作できます。

## Elastic Scale クライアントのバージョン履歴 

**バージョン 0.8 - 2015 年 3 月**

* 新しい ShardMap.OpenConnectionForKeyAsync メソッドのデータ依存型ルーティングに非同期サポートが追加しました。 
* パブリックの KeyType プロパティが ShardMap に追加しました。 
* データベースの復元サポートの改善とシャード用の災害復旧のシナリオが追加しました。 

**バージョン 0.7 - 2014 年 10 月**

予期のプレビュー バージョン 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

<!--HONumber=47-->
