---
title: Elastic Database クライアント ライブラリの資格情報を管理する | Microsoft Docs
description: エラスティック データベース アプリの適切な資格情報のレベルを設定する方法 (管理者から読み取り専用)
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
ms.date: 04/01/2018
ms.openlocfilehash: 8f5de47e9d9c8cc50f77ddadfe9d781ef4c3bc96
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863824"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Elastic Database クライアント ライブラリへのアクセスに使用する資格情報
[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)では、3 種類の資格情報を使用して[シャード マップ マネージャー](sql-database-elastic-scale-shard-map-management.md)にアクセスします。 必要に応じて、最低レベルのアクセスが可能な資格情報を使用します。

* **管理の資格情報**: シャード マップ マネージャーを作成または操作します  ([用語集](sql-database-elastic-scale-glossary.md)をご覧ください)。 
* **アクセス資格情報**: 既存のシャード マップ マネージャーにアクセスして、シャードに関する情報を取得します。
* **接続の資格情報**: シャードに接続します。 

「 [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」も参照してください。 

## <a name="about-management-credentials"></a>管理資格情報について
管理の資格情報は、シャード マップを操作するアプリケーションの **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) オブジェクトを作成するときに使用されます  (例については、「[Elastic Database ツールを使用してシャードを追加する](sql-database-elastic-scale-add-a-shard.md)」および「[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)」をご覧ください)。 エラスティック スケール クライアント ライブラリのユーザーは、SQL ユーザーと SQL ログインを作成し、グローバル シャード マップ データベースとすべてのシャード データベースに対する読み取り/書き込みアクセス許可が各ユーザーに付与されていることを確認する必要があります。 これらの資格情報は、シャード マップに変更を加えるときにグローバル シャード マップとローカル シャード マップを維持するために使用されます。 たとえば、管理の資格情報を使用して、シャード マップ マネージャー オブジェクトを作成します (**GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)) を使用)。 

```
// Obtain a shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy); 
```

変数 **smmAdminConnectionString** は、管理資格情報を格納する接続文字列です。 ユーザー ID とパスワードにより、シャード マップ データベースと個々のシャードの両方に対する読み取り/書き込みアクセスが提供されます。 管理接続文字列には、グローバル シャード マップ データベースを識別するためのサーバー名とデータベース名も含まれます。 この処理に使用する一般的な接続文字列を次に示します。

```
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 
```

値の形式としては、"username@server" ではなく、単に "username" という値を使ってください。  これは、シャード マップ マネージャー データベースと個々のシャードの両方で資格情報を有効にする必要があり、それらが別々のサーバー上に存在する場合があるためです。

## <a name="access-credentials"></a>資格情報にアクセスする
シャード マップの管理用ではないアプリケーションでシャード マップ マネージャーを作成するときは、グローバル シャード マップに対する読み取り専用アクセス許可が与えられる資格情報を使用します。 これらの資格情報の下でグローバル シャード マップから取得される情報は、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)の目的と、クライアント上のシャード マップ キャッシュを設定するために使用されます。 資格情報は、**GetSqlShardMapManager** と同じ呼び出しパターンを通じて提供されます。 

```
// Obtain shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

ここでは、**管理者以外**のユーザーのアクセス用に使用されるさまざまな資格情報を反映するために **smmReadOnlyConnectionString** を使用しています。これらの資格情報では、グローバル シャード マップに対する書き込みアクセス許可は提供されません。 

## <a name="connection-credentials"></a>接続の資格情報
**OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) メソッドを使用してシャーディング キーに関連付けられているシャードにアクセスするには、追加の資格情報が必要になります。 これらの資格情報は、シャード上に存在するローカル シャード マップ テーブルへの読み取り専用アクセス許可を提供する必要があります。 これは、シャード上でのデータ依存ルーティングのための接続検証を行う場合に必要になります。 次のコード スニペットは、データ依存ルーティングのコンテキストでデータ アクセスを許可します。 

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 
```

この例の **smmUserConnectionString** は、ユーザー資格情報の接続文字列を保持します。 Azure SQL DB に対するユーザー資格情報の一般的な接続文字列を次に示します。 

```
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

管理者の資格情報と同じように、"username@server" という形式の値は使わないでください。 代わりに "username" のみを使用します。  接続文字列にはサーバー名とデータベース名が含まれていないことにも注意してください。 これは、**OpenConnectionForKey** の呼び出しによって接続がキーに基づいた正しいシャードに自動的に転送されるためです。 そのため、データベース名とサーバー名を指定しないでください。 

## <a name="see-also"></a>「
[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)

[SQL Database の保護](sql-database-security-overview.md)

[Elastic Database ジョブの概要](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

