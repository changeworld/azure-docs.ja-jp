<properties 
	pageTitle="データ依存ルーティング" 
	description="データ依存ルーティング (Azure SQL DB の Elastic Scale の機能) に ShardMapManager を使用する方法" 
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
	ms.date="03/05/2015" 
	ms.author="stuarto"/>

# データ依存ルーティング

ADO.NET アプリケーションで **ShardMapManager** クラスを利用することで、シャード化された環境内の適切な物理データベースにデータベース クエリとコマンドを直接送ることができます。これは、シャード化されたデータベースを操作するときの基本的なパターンである、**データ依存ルーティング**と呼ばれています。データ依存ルーティングを使用したアプリケーションで、特定のクエリまたはトランザクションがそれぞれアクセスするデータベースは、要求ごとに 1 つに制限されています。  

データ依存ルーティングを使用する場合、アプリケーションは、シャード化された環境の各種データ スライスに関連付けられた、さまざまな接続文字列または DB の場所を追跡する必要はありません。代わりに、[シャード マップ マネージャー](sql-database-elastic-scale-shard-map-management.md) が正しいデータベースへの接続処理を必要に応じて管理します。これは、シャード マップのデータとアプリケーションの要求の対象であるシャーディング キーの値に基づいて行われます(通常、このキーは、 *customer_id*、 *tenant_id*、 *date_key*、データベース要求の基本的なパラメーターである他の特定識別子です)。 

## データ依存ルーティング アプリケーションでの ShardMapManager の使用 

データ依存ルーティングを使用するアプリケーションでは、初期化中に、ファクトリ呼び出し **GetSQLShardMapManager** を使用して、アプリ ドメインごとに **ShardMapManager** を 1 回インスタンス化する必要があります。

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

この例では、**ShardMapManager** と、それが持っている特定の **ShardMap** の両方が初期化されます。 

シャード マップを直接操作しないアプリケーションでは、ファクトリ メソッド内で **ShardMapManager** を取得するために使用される資格情報 (上の例では  *smmConnectionString*) は、接続文字列によって参照される**グローバル シャード マップ** データベースに対して読み取り専用アクセス許可のみを持っている資格情報である必要があります。これらの資格情報は、通常、シャード マップ マネージャーへの接続で使用される資格情報とは異なります。「[Elastic Scale 資格情報の管理](sql-database-elastic-scale-manage-credentials.md)」もご覧ください。 

## データ依存型ルーティングを呼び出す 

メソッド **ShardMap.OpenConnectionForKey (key、connectionString、connectionOptions)** は、**key** パラメーターの値に基づいて、適切なデータベースへのコマンド発行の準備が整った ADO.Net 接続を返します。シャード情報は、**ShardMapManager** によってアプリケーションにキャッシュされるため、これらの要求では通常は、**グローバル シャード マップ** データベースに対するルックアップは行われません。 

* **key** パラメーターは、要求に対する適切なデータベースを特定するためのシャード マップへのルックアップ キーとして使用されます。 

* **connectionString** は、必要な接続を行うためのユーザーの資格情報のみを渡すために使用されます。メソッドがデータベースとサーバーを特定するために **ShardMap** を使用するため、この  *connectionString* には、データベース名またはサーバー名は含まれません。 

* **connectionOptions** 列挙型が、接続時に検証が実施されているかどうかを示すために使用されます。**ConnectionOptions.Validate** を使用することをお勧めします。分割やマージの操作の結果、シャード マップが変更している可能性や、行が他のデータベースに移動している可能性のある環境では、キー値に基づいて行われるデータベースのルックアップ (キャッシュ済み) が引き続き正しいことが検証によって確認されます。接続要求がアプリケーションに配信される前に、検証により、ターゲット データベースにあるローカルのシャード マップ (グローバル シャード マップではない) に対する簡単なクエリが実行されます。 

ローカルのシャード マップの検証が失敗した (キャッシュが正しくないことが示された) 場合は、シャード マップ マネージャーがグローバル シャード マップに対してクエリを実行し、ルックアップの正しい値を新しく取得します。その後、キャッシュを更新、取得してから適切なデータベース接続に戻ります。 

**ConnectionOptions.None** (検証を実施しない) は、アプリケーションがオンラインの間にシャード マッピングの変更が想定されない場合のみ許容できます。その場合、キャッシュされた値は常に正しいと見なすことができ、ターゲット データベースに対する今後のラウンドトリップ検証呼び出しを安全にスキップできます。これによってトランザクションの待機時間やデータベース トラフィックを軽減できます。また、**connectionOptions** は、一定期間内にシャーディングの変更が想定されるかどうかを示すために構成ファイルに値で設定することもできます。  

次に示すのは、シャード マップ マネージャーを使用してデータ依存ルーティングを実行するコードの例です。整数キー **CustomerID** の値に基づいて **customerShardMap** という名前の **ShardMap** オブジェクトが使用されます。  

## 例: データ依存型ルーティング 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
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
    }  

**SqlConnection** のコンストラクターと後続の **Open()** 呼び出しを接続オブジェクトに対して使用する代わりに、**OpenConnectionForKey** メソッドが使用され、既に開いている接続が適切なデータベースに新たに接続されることに注意してください。この方法で接続した場合も引き続き ADO.Net 接続プールの利点を最大限に活用できます。トランザクションと要求を満たすことができるのが 1 回に 1 つのシャードである限り、ADO.Net を既に使用しているアプリケーションで必要な変更はこれだけです。 

アプリケーションが ADO.Net との非同期プログラミングを使用する場合は、メソッド **OpenConnectionForKeyAsync** も使用できます。その動作は、ADO.Net の **Connection.OpenAsync** メソッドと同等のデータ依存型ルーティングです。

## 一時的な障害処理との統合 

クラウドでのデータ アクセス アプリケーション開発のベスト プラクティスは、データベースへの接続またはクエリでの一時的な障害をアプリケーションで確実に捕捉し、エラーがスローされる前に操作を確実にリトライすることです。クラウド アプリケーションにおける一時的な障害処理の詳細については、[一時的な障害処理に関するページ](http://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx)をご覧ください。 
 
一時的な障害処理は、データ依存ルーティングのパターンと自然に共存できます。そのための主な要件は、データ依存ルーティング接続を取得した **using** ブロックを含めたデータ アクセス要求全体をリトライすることです。上記の例は、次のように書き換えることができます (変更箇所が強調表示されています)。 

### 例 - 一時的な障害処理機能を伴うデータ依存ルーティング 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connection to the shard for that customer ID 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


一時的な障害処理の実装に必要なパッケージは、Elastic Scale Starter Kit アプリケーションのビルド時に自動的にダウンロードされます。パッケージは、「[Enterprise Library - Transient Fault Handling Application Block (エンタープライズ ライブラリ - 一時的な障害処理アプリケーション ブロック)](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)」からも個別に入手できます。バージョン 6.0 以降を使用してください。 

## トランザクションの整合性 

トランザクションのプロパティは、シャードにとってローカルなすべての操作で保証されています。たとえば、データ依存ルーティングを通じて送信されるトランザクションは、接続するターゲット シャードの範囲内で実行されます。現時点では、複数の接続を 1 つのトランザクションに登録するために提供された機能はありません。したがって、複数のシャードにまたがる操作にもトランザクション上の保証はありません。  

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
