---
title: シャード化された Azure SQL Database をクエリする | Microsoft Docs
description: エラスティック データベース クライアント ライブラリを使用して複数のシャードを対象にクエリを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: bc0ca62699c21848e4d5fdc18bef292dce4634bf
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863467"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Elastic Database ツールを使用したマルチシャード クエリ

## <a name="overview"></a>概要

[Elastic Database ツール](sql-database-elastic-scale-introduction.md)を利用すれば、シャード化されたデータベース ソリューションを作成できます。 **マルチシャード クエリ実行**は、複数のシャードにまたがるクエリの実行が必要となるデータ収集/レポート作成などのタスクに使用されます  (すべての操作を単一のシャード上で実行する[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)と比べてください)。 

1. **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx))、**TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx))、または **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) メソッドを使用して、**RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map)、[.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) または **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map)、[.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) を取得します。 「**[ShardMapManager の作成](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)**」と「**[RangeShardMap または ListShardMap の取得](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**」をご覧ください。
2. **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) オブジェクトを作成します。
3. **MultiShardStatement または MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)) を作成します。 
4. **CommandText プロパティ** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) を T-SQL コマンドに設定します。
5. **ExecuteQueryAsync または ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement.executeQueryAsync)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) メソッドを呼び出してコマンドを実行します。
6. **MultiShardResultSet または MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) クラスを使用して結果を表示します。 

## <a name="example"></a>例

次のコードは、 **myShardMap** という名前の *ShardMap*を使用してマルチシャード クエリを実行する方法を示しています。 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

主な相違点は、マルチシャード接続の構築です。 **SqlConnection** が単一のデータベースに作用する一方、**MultiShardConnection** は入力として***シャードのコレクション***を受け取ります。 シャードのコレクションは、シャード マップから設定します。 次に、 **UNION ALL** セマンティクスを使用してシャードのコレクションに対するクエリを実行して、単一の全体的な結果を生成します。 必要に応じて、コマンドの **ExecutionOptions** プロパティを使用して、行の元になっているシャードの名前を出力に追加できます。 

ここで、 **myShardMap.GetShards()** の呼び出しに注目してください。 このメソッドは、シャード マップからすべてのシャードを取得し、すべての関連データベースを対象にクエリを実行する簡単な方法を提供します。 マルチシャード クエリのためのシャードのコレクションは、 **myShardMap.GetShards()** 呼び出しから返されたコレクションに対して LINQ クエリを実行することによって、さらに絞り込むことができます。 部分的な結果のポリシーとの組み合わせにおいて、マルチシャード クエリ実行の現在の機能は、数十から最大で数百のシャードで適切に動作するように設計されています。

現在のマルチシャード クエリ実行には、クエリの対象となるシャードとシャードレットの検証が欠けているという制限があります。 データ依存ルーティングでは特定のシャードがシャード マップの一部であることが照会時に確認されますが、マルチシャード クエリ実行ではこのようなチェックは実行されません。 そのため、シャード マップから削除されたデータベースに対してマルチシャード クエリが実行される可能性があります。

## <a name="multi-shard-queries-and-split-merge-operations"></a>マルチシャード クエリと分割/マージ操作

マルチシャード クエリでは、照会されたデータベース上のシャードレットが進行中の分割/マージ操作に参加しているかどうかは確認されません。  (「[Elastic Database 分割/マージ ツールを使用したスケーリング](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください)。そのため、同じマルチシャード クエリ内で複数のデータベースに対して同じシャードレットの行が表示される不整合が発生する可能性があります。 これらの制限を考慮したうえで、マルチシャード クエリを実行するときは、進行中の分割/マージ操作とシャード マップへの変更をドレインすることを検討してください。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]