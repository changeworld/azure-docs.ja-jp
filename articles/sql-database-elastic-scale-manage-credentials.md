<properties 
	pageTitle="Elastic Scale の資格情報の管理" 
	description="Elastic Scale アプリの適切な資格情報のレベルを設定する方法 (管理者から読み取り専用)。" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="stuartozer" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="stuartozer@microsoft.com"/>

# Elastic Scale の資格情報の管理

[Elastic Scale Client APIs](http://go.microsoft.com/?linkid=9862605) は、さまざまな種類の操作 (特に[シャード マップ マネージャー](http://go.microsoft.com/?linkid=9862595)の作成または操作、シャードに関する情報を取得するための既存のシャード マップ マネージャーの参照、シャードへの接続) を行うために資格情報を使用します。ここでは、これらの種類の処理のための資格情報について説明します。 


* **シャード マップにアクセスするための管理資格情報**:管理資格情報は、シャード マップを操作するアプリケーションの **ShardMapManager** オブジェクトをインスタンス化するときに使用されます。Elastic Scale API のユーザーは、必要な SQL ユーザーと SQL ログインを作成したうえで、グローバル シャード マップ データベースとすべてのシャード データベースに対する読み取り/書き込みアクセス許可がこれらのユーザーに付与されていることを確認する必要があります。これらの資格情報は、シャード マップに変更を加えるときにグローバル シャード マップとローカル シャード マップを維持するために使用されます。たとえば、次のコードに示すように、管理資格情報を使用してシャード マップ マネージャー オブジェクトをインスタンス化します。 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     変数 **smmAdminConnectionString** は、管理資格情報を格納する接続文字列です。ユーザー ID とパスワードにより、シャード マップ データベースと個々のシャードの両方に対する読み取り/書き込みアクセスが提供されます。管理接続文字列には、グローバル シャード マップ データベースを識別するためのサーバー名とデータベース名も含まれます。この処理に使用する一般的な接続文字列を次に示します。

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

     ユーザー ID 値は "username@server" の形式では使用しないでください。代わりに "username" のみを使用します。これは、シャード マップ マネージャー データベースと個々のシャードの両方で資格情報を有効にする必要があり、それらが別々のサーバー上に存在する場合があるためです。
     
* **シャード マップ マネージャーにアクセスするためのユーザー資格情報**:シャード マップの管理用ではないアプリケーションでシャード マップ マネージャーをインスタンス化するときは、グローバル シャード マップに対する読み取り専用アクセス許可が与えられる資格情報を使用します。これらの資格情報を使用してグローバル シャード マップから取得された情報は、[データ依存ルーティング](./sql-database-elastic-scale-data-dependent-routing.md)で クライアントのシャード マップ キャッシュにデータを投入するために使用されます。資格情報は、上に示すように、**GetSqlShardMapManager** と同じ呼び出しパターンを通じて提供されます。 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     ここでは、**管理者以外**のユーザーのアクセス用に使用されるさまざまな資格情報を反映するために **smmReadOnlyConnectionString** を使用しています。これらの資格情報では、グローバル シャード マップに対する書き込みアクセス許可は提供されません。 

* **ユーザー データにアクセスするためのユーザー資格情報**:**OpenConnectionForKey** メソッドを使用してキーに関連付けられているシャードにアクセスするには、追加の資格情報が必要になります。これらの資格情報は、シャード上に存在するローカル シャード マップ テーブルへの読み取り専用アクセス許可を提供する必要があります。これは、シャード上でのデータ依存ルーティングのための接続検証を行う場合に必要になります。次のコード スニペットに、データ依存ルーティングのコンテキストでのユーザー データ アクセスのためのユーザー資格情報の使用方法を示します。 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    この例の **smmUserConnectionString** は、ユーザー資格情報の接続文字列を保持します。Azure SQL DB に対するユーザー資格情報の一般的な接続文字列を次に示します。 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    管理資格情報と同じように、ユーザー ID 値は "username@server" の形式では使用しないでください。代わりに "username" のみを使用します。接続文字列にはサーバー名とデータベース名が含まれていないことにも注意してください。これは、**OpenConnectionForKey** の呼び出しによって接続がキーに基づいた正しいシャードに自動的に転送されるためです。そのため、データベース名とサーバー名を指定しないでください。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
