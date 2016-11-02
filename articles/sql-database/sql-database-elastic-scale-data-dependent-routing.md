<properties 
	pageTitle="データ依存ルーティング | Microsoft Azure" 
	description="データ依存ルーティング (Azure SQL Database のElastic Database の機能) のために .NET アプリで ShardMapManager クラスを使用する方法について説明します。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="torsteng"/>

#データ依存ルーティング

**データ依存ルーティング**は、クエリ内のデータを使用して、要求を適切なデータベースにルーティングできる機能です。これは、シャード化されたデータベースを操作するときの基本的なパターンです。特にシャーディング キーがクエリの一部でない場合は、要求コンテキストを使用して要求をルーティングすることもできます。データ依存ルーティングを使用したアプリケーションで、特定のクエリまたはトランザクションがそれぞれアクセスするデータベースは、要求ごとに 1 つに制限されています。Azure SQL Database Elastic ツールでは、このルーティングは ADO.NET アプリケーションの **[ShardMapManager クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)**で実行します。

アプリケーションは、シャード化された環境の各種データ スライスに関連付けられた、さまざまな接続文字列または DB の場所を追跡する必要はありません。代わりに [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) が正しいデータベースへの接続処理を必要に応じて管理します。これは、シャード マップのデータとアプリケーションの要求の対象であるシャーディング キーの値に基づいて行われます(通常、このキーは、データベース要求の基本的なパラメーターである、*customer\_id*、*tenant\_id*、*date\_key*、または他の特定の識別子です)。

詳細については、「[データ依存型ルーティングを使用した SQL Server のスケール アウト](https://technet.microsoft.com/library/cc966448.aspx)」を参照してください。

## クライアント ライブラリのダウンロード

クラスを取得するには、[Elastic Database クライアント ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)をインストールします。

## データ依存ルーティング アプリケーションでの ShardMapManager の使用 

アプリケーションは、ファクトリ呼び出し **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** を使用して、初期化中に **ShardMapManager** をインスタンス化する必要があります。この例では、**ShardMapManager** と、これが含んでいる特定の **ShardMap** の両方が初期化されます。また、GetSqlShardMapManager メソッドと [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) メソッドを使用しています。

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### シャード マップの取得には最小限のアクセス許可を持つ資格情報を使用する

アプリケーションでシャード マップ自体を操作しない場合は、ファクトリ メソッドで使用される資格情報には、**グローバル シャード マップ** データベースに対する読み取り専用アクセス許可を付与します。これらの資格情報は、通常、シャード マップ マネージャーへの接続で使用される資格情報とは異なります。「[Elastic Database クライアント ライブラリへのアクセスに使用する資格情報](sql-database-elastic-scale-manage-credentials.md)」も参照してください。

## OpenConnectionForKey メソッドを呼び出す

**[ShardMap.OpenConnectionForKey メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)**は、**key** パラメーターの値に基づいて、適切なデータベースへのコマンド発行の準備が整った ADO.Net 接続を返します。シャード情報は、**ShardMapManager** によってアプリケーションにキャッシュされるため、これらの要求では通常は、**グローバル シャード マップ** データベースに対するルックアップは行われません。

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* **key** パラメーターは、要求に対する適切なデータベースを特定するためのシャード マップへのルックアップ キーとして使用されます。

* **connectionString** は、必要な接続を行うためのユーザーの資格情報のみを渡すために使用されます。この *connectionString* には、データベース名またはサーバー名は含まれません。メソッドがデータベースとサーバーを特定するために **ShardMap** を使用するためです。

* 分割やマージの操作の結果、シャード マップが変更されている可能性や、行が他のデータベースに移動している可能性のある環境では、**[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** を **ConnectionOptions.Validate** に設定する必要があります。そうすることで、接続要求がアプリケーションに配信される前に、ターゲット データベースにあるローカルのシャード マップ (グローバル シャード マップではない) に対する簡単なクエリが実行されます。

ローカルのシャード マップの検証が失敗した (キャッシュが正しくないことが示された) 場合は、シャード マップ マネージャーがグローバル シャード マップに対してクエリを実行し、ルックアップの正しい値を新しく取得します。その後、キャッシュを更新、取得してから適切なデータベース接続に戻ります。

**ConnectionOptions.None** を使用するのは、アプリケーションがオンラインの間にシャード マッピングの変更が想定されない場合のみです。その場合、キャッシュされた値は常に正しいと見なすことができ、ターゲット データベースに対する今後のラウンドトリップ検証呼び出しを安全にスキップすることができます。これによりデータベース トラフィックを軽減できます。また、**connectionOptions** は、一定期間内にシャーディングの変更が想定されるかどうかを示すために構成ファイルの値で設定することもできます。

この例では、**customerShardMap** という名前のオブジェクト **ShardMap** を使用した、整数キー **CustomerID** の値を使用しています。

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

**OpenConnectionForKey** メソッドは、正しいデータベースに対し、既に開いている接続を新たに返します。この方法で接続した場合も引き続き ADO.Net 接続プールの利点を最大限に活用できます。トランザクションと要求を満たすことができるのが 1 回に 1 つのシャードである限り、ADO.Net を既に使用しているアプリケーションで必要な変更はこれだけです。

アプリケーションが ADO.Net との非同期プログラミングを使用する場合は、**[OpenConnectionForKeyAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)** メソッドも使用できます。その動作は、ADO.Net の **[Connection.OpenAsync] \(https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)** メソッドと同等のデータ依存型ルーティングです。

## 一時的な障害処理との統合 

クラウドでのデータ アクセス アプリケーション開発のベスト プラクティスは、一時的な障害をアプリケーションで確実に捕捉し、エラーがスローされる前に操作を確実にリトライすることです。クラウド アプリケーションにおける一時的な障害処理の詳細については、一時的な障害処理に関するページ (https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx)) を参照してください。
 
一時的な障害処理は、データ依存ルーティングのパターンと自然に共存できます。そのための主な要件は、データ依存ルーティング接続を取得した **using** ブロックを含めたデータ アクセス要求全体をリトライすることです。上記の例は、次のように書き換えることができます (変更箇所が強調表示されています)。

### 例 – 一時的な障害処理機能を伴うデータ依存ルーティング 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID.
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Update completed"); 
        { 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


一時的な障害処理の実装に必要なパッケージは、エラスティック データベースのサンプル アプリケーションのビルド時に自動的にダウンロードされます。パッケージは、「[Enterprise Library - Transient Fault Handling Application Block (エンタープライズ ライブラリ - 一時的な障害処理アプリケーション ブロック)](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)」からも個別に入手することができます。バージョン 6.0 以降を使用してください。

## トランザクションの整合性 

トランザクションのプロパティは、シャードにとってローカルなすべての操作で保証されています。たとえば、データ依存ルーティングを通じて送信されるトランザクションは、接続するターゲット シャードの範囲内で実行されます。現時点では、複数の接続を 1 つのトランザクションに登録するために提供された機能はありません。したがって、複数のシャードにまたがる操作にもトランザクション上の保証はありません。

## 次のステップ
シャードをデタッチまたは再アタッチする方法については、「[RecoveryManager クラスを使用したシャード マップに関する問題の解決](sql-database-elastic-database-recovery-manager.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0706_2016-->