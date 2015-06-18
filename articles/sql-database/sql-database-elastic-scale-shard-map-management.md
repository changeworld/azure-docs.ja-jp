<properties 
	pageTitle="シャード マップの管理" 
	description=".NET 向けの Elastic Scale API である ShardMapManager の使用方法" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# シャード マップの管理 
シャード化データベース環境では、アプリケーションが**シャーディング キー**の値に基づいて適切なデータベースに接続するための情報が**シャード マップ**に保持されます。Elastic Scale プレビューでシャードを管理するうえで、このマップがどのように構築されているかを理解することが非常に重要です。

## シャード マップとシャードのマッピング 
### シャーディング キーでサポートされる .Net 型
Elastic Scale では、シャーディング キーとして次の .NET Framework 型がサポートされます。

* integer
* long
* guid
* byte[]  

### リスト シャード マップと範囲シャード マップ
シャード マップは、**個々のシャーディング キー値のリスト**または**シャーディング キー値の範囲**を使用して作成できます。 

### リスト シャード マップ
**シャード**には、**シャードレット**が含まれます。シャードレットとシャードの間のマッピングは、シャード マップによって管理されます。**リスト シャード マップ**は、シャードレットを識別する個々のキー値と、シャードとして動作するデータベースとの間の関連付けです。**リストのマッピング**は明示的であり (たとえば、キー 1 をデータベース A にマップする)、異なるキー値を同じデータベースにマップできます (たとえば、キー値 3 と 6 の両方がデータベース B を参照する)。
<table>
   <tr>
    <td>キー</td>
     <td>シャードの場所</td>
   </tr>
   <tr>
    <td>1</td>
     <td>データベース A</td>
   </tr>
  <tr>
    <td>3</td>
     <td>データベース B</td>
   </tr>
  <tr>
    <td>4</td>
     <td>データベース C</td>
   </tr>
  <tr>
    <td>6</td>
     <td>データベース B</td>
   </tr>
  <tr>
    <td>...</td>
     <td>...</td>
   </tr>
</table> 

### 範囲シャード マップ 
**範囲シャード マップ**では、キーの範囲がペア **[Low Value, High Value)** によって記述されます。 *Low Value* は範囲内の最小キー、 *High Value* は範囲を超える最初の値です。 

たとえば、**[0, 100)** には、0 以上 100 未満のすべての整数が含まれます。複数の範囲が同じデータベースをポイントでき、隣接していない範囲もサポートされます (たとえば、次の例では、[100,200) と [400,600) は、両方ともデータベース C をポイントしています)。
<table>
   <tr>
    <td><b>キー範囲</b></td>
     <td><b>シャードの場所</b></td>
   </tr>
   <tr>
    <td>[1, 50)</td>
     <td>データベース A</td>
   </tr>
  <tr>
    <td>[50, 100)</td>
     <td>データベース B</td>
   </tr>
  <tr>
    <td>[100, 200)</td>
     <td>データベース C</td>
   </tr>
  <tr>
    <td>[400, 600)</td>
     <td>データベース C</td>
   </tr>
  <tr>
    <td>...</td>
     <td>...</td>
   </tr>
</table> 

上に示す各テーブルは、**ShardMap** オブジェクトの概念上の例です。各行は、個々の **PointMapping** (リスト シャード マップ) オブジェクトまたは **RangeMapping** (範囲シャード マップ) オブジェクトの簡単な例です。

## シャード マップ マネージャー 

Elastic Scale API では、シャード マップ マネージャーはシャード マップのコレクションです。**ShardMapManager** .Net オブジェクトによって管理されるデータは、次の 3 つの場所に保持されます。 

1. **グローバル シャード マップ (GSM)**:**ShardMapManager** を作成するときは、そのすべてのシャード マップとマッピングのリポジトリとして機能するデータベースを指定します。この情報を管理するために、特殊なテーブルとストアド プロシージャが自動的に作成されます。通常、これは小さなデータベースで簡単にアクセスできますが、アプリケーションの他の目的には使用されません。このテーブルは、**__ShardManagement** という名前の特殊なスキーマに含まれます。 

2. **ローカル シャード マップ (LSM)**:シャード マップ内でシャードとして指定されたすべてのデータベースは、シャードに固有のシャード マップ情報を格納と管理するためのいくつかの小さなテーブルと特殊なストアド プロシージャを含むように変更されます。この情報は GSM 内の情報を冗長化したものになりますが、これにより、アプリケーションは、GSM に負荷をかけることなくキャッシュされたシャード マップ情報を検証できます。アプリケーションは、LSM を使用して、キャッシュされたマッピングがまだ有効であるかどうかを判定できます。各シャードの LSM に対応するテーブルは、スキーマ **__ShardManagement** に含まれます。

3. **アプリケーション キャッシュ**:**ShardMapManager** オブジェクトにアクセスする各アプリケーション インスタンスは、そのマッピングのローカル メモリ内キャッシュを保持します。ここには、最近取得されたルーティング情報が格納されます。 

## ShardMapManager の作成
アプリケーションの **ShardMapManager** は、ファクトリ パターンを使用してインスタンス化されます。**ShardMapManagerFactory.GetSqlShardMapManager** メソッドは、(サーバー名と、GSM を保持しているデータベースの名前を含む) **ConnectionString** 形式の資格情報を受け取り、**ShardMapManager** のインスタンスを返します。  

**ShardMapManager** は、アプリケーションの初期化コード内でアプリケーション ドメインごとに 1 回だけインスタンス化する必要があります。**ShardMapManager** には、任意の数のシャード マップを含めることができます。多くのアプリケーションでは、シャード マップは 1 つあれば十分です。ただし、異なるスキーマ用や一意性を確保する目的のためにデータベースの異なるセットを使用する場合は、複数のシャード マップを使用することをお勧めします。 

次のコードでは、アプリケーションは、既存の **ShardMapManager** を開こうとします。グローバル **ShardMapManager** (GSM) を表すオブジェクトがデータベース内に存在しない場合、クライアント ライブラリはこれを作成します。

    // Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 

### シャード マップ管理資格情報

通常、シャード マップを管理と操作するアプリケーションは、シャード マップを使用して接続をルーティングするアプリケーションとは異なります。 

シャード マップを管理するアプリケーション (シャード、シャード マップ、シャード マッピングの追加または変更などを実行するアプリケーション) では、**ShardMapManager** をインスタンス化する必要があります。そのためには、**GSM データベースとシャードとして機能する各データベースに対する読み取り/書き込み特権を持つ**資格情報を使用します。この資格情報により、新しいシャードに LSM テーブルが作成されるときだけでなく、シャード マップ情報が入力または変更されたときに GSM と LSM の両方のテーブルに対する書き込みが許可される必要があります。  

### 影響を受けるのはメタデータのみ 

**ShardMapManager** データを設定または変更するために使用されるメソッドは、シャード自体に格納されているユーザー データを変更しません。たとえば、**CreateShard**、**DeleteShard**、**UpdateMapping** などのメソッドは、シャード マップ メタデータのみに作用し、シャードに含まれるユーザー データを削除、追加、変更することはありません。代わりに、これらのメソッドは、実際のデータベースを作成または削除するために実行される別の操作や、シャード化環境のバランスを再調整するためにシャードで行を移動する操作と組み合わせて使用するように設計されています(Elastic Scale プレビューに含まれる **Split/Merge** サービスでは、シャード間の実際のデータ移動の調整と共にこれらの API を利用しています)。 

## シャード マップの設定:例 
特定のシャード マップを設定する場合の処理手順を次に示します。このコードは、次の手順を実行します。 

1. シャード マップ マネージャー内に新しいシャード マップが作成されます。 
2. 2 つの異なるシャードのメタデータがシャード マップに追加されます。 
3. さまざまなキー範囲マッピングが追加され、シャード マップ全体の内容が表示されます。 

このコードでは、予期しないエラーが発生した場合に備えてメソッド全体を安全に再実行できるようになっています。つまり、各要求は、シャードまたはマッピングの作成を試みる前に、シャードまたはマッピングが既に存在するかどうかを確認します。次のコードでは、**sample_shard_0**、**sample_shard_1**、**sample_shard_2** という名前のデータベースが文字列 **shardServer** によって参照されるサーバーに既に作成されているものと想定しています。 

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_0"), out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_1"), out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(0, 50), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(50, 100), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(100, 150), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(150, 200), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                   (new Range<long>(200, 300), shard0, MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                                    .OrderBy(s => s.Location.DataSource)
                                    .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
別の方法として、PowerShell スクリプトを使用して同じ結果を得ることができます。     

シャード マップを設定した後は、データ アクセス アプリケーションを作成するか、またはマップに適合させることができます。**マップのレイアウト**を変更する必要があるまで、マップの設定または操作を再度行う必要はありません。  

## データ依存ルーティング 

シャード マップ マネージャーを使用するのは、ほとんどの場合、アプリに固有のデータ操作を実行するためのデータベース接続を必要とするアプリケーションです。シャード化されたアプリケーションでは、これらの接続を適切なターゲット データベースに関連付けする必要があります。これを**データ依存ルーティング**といいます。このようなアプリケーションの場合は、GSM データベースに対する読み取り専用アクセス権限を持つ資格情報を使用してファクトリからシャード マップ マネージャー オブジェクトをインスタンス化します。後で個々の接続要求により、適切なシャード データベースに接続するために必要な資格情報が提供されます。

読み取り専用の資格情報を使用して開かれた **ShardMapManager** を使用するこれらのアプリケーションはマップまたはマッピングを変更できないことに注意してください。このような操作を行うには、既に説明したように、高度な特権の資格情報を提供する管理操作専用のアプリケーションまたは PowerShell スクリプトを作成します。   

詳細については、「[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)」をご覧ください。 

## シャード マップの変更 

シャード マップはさまざまな方法で変更できます。次に示すどのメソッドを使用しでもシャードとそのマッピングを変更できますが、これらによってシャード内のデータが物理的に変更されたり、実際のデータベースが作成または削除されたりすることはありません。次に示すシャード マップの管理操作の中には、物理的にデータを移動したり、シャードとして機能するデータベースを追加または削除する管理操作と組み合わせて使用したりすることが必要になる操作もあります。

これらのメソッドは、シャード化データベース環境内のデータの全体的な分散状況を変更するために使用できるビルド ブロックとして連携します。  

* シャードを追加または削除するには: **CreateShard** と **DeleteShard** を使用します。 
    
    これらの操作を実行するには、サーバーとターゲット シャードを表すデータベースが既に存在している必要があります。これらのメソッドは、データベース自体には作用せず、シャード マップ内のメタデータのみに作用します。

* シャードにマップされるポイントまたは範囲を作成または削除するには: **CreateRangeMapping**、**DeleteMapping**、**CreatePointMapping** を使用します。 
    
    多くの異なるポイントまたは範囲を同じシャードにマップできます。これらのメソッドは、メタデータにのみ作用し、シャードに既に存在するデータには作用しません。**DeleteMapping** 操作に合わせてデータをデータベースから削除する必要がある場合は、これらのメソッドを組み合わせてこれらの操作を別個に実行する必要があります。  

* 既存の範囲を 2 つに分割する、または隣接する範囲を 1 つにマージするには: **SplitMapping** と **MergeMappings** を使用します。  

    分割操作とマージ操作を行っても、**キー値がマップされているシャードは変更されない**ことに注意してください。分割操作を実行すると、既存の範囲が 2 つの部分に分割されます。このとき、どちらの部分も同じシャードにマップされたままになります。マージ操作を実行すると、同じシャードに既にマップされている 2 つの隣接する範囲が 1 つの範囲に結合されます。シャード間でのポイントまたは範囲自体の移動は、実際のデータの移動と組み合わせて **UpdateMapping** を使用して調整する必要があります。Elastic Scale プレビューの一部である **Split/Merge** サービスを使用すると、データの移動が必要な場合にシャード マップの変更をデータの移動と連携させることができます。 

* 個々のポイントまたは範囲を別のシャードに再マップ (移動) するには: **UpdateMapping** を使用します。  

    データは **UpdateMapping** 操作に合わせてシャード間で移動することが必要になる場合があるため、これらのメソッドを使いながら移動操作を別個に実行する必要があります。

* マッピングをオンラインまたはオフラインにするには: **MarkMappingOffline** と **MarkMappingOnline** を使用して、マッピングのオンライン ステータスを制御します。 

    マッピングが "オフライン" 状態のときは、シャード マッピングに対して特定の操作のみを実行できます (UpdateMapping、DeleteMapping など)。マッピングがオフラインのとき、そのマッピングに含まれるキーに基づくデータに依存する要求はエラーを返します。さらに、範囲が初めてオフラインになったときは、変更が加えられている範囲に対するクエリによって一貫性のない結果または不完全な結果が生成されるのを防ぐために、影響を受けるシャードへのすべての接続が自動的に強制終了されます。 

## シャードの追加 

多くの場合、アプリケーションは、既に存在しているシャード マップに対し、新しいキーまたはキー範囲から期待されるデータを処理するために新しいシャードを単に追加する必要があります。たとえば、テナント ID によってシャード化されるアプリケーションの場合、新しいテナントに対して新しいシャードをプロビジョニングすることが必要になる場合があります。また、毎月シャード化されるデータの場合、新しい月が始まる前に新しいシャードをプロビジョニングすることが必要になる場合があります。 

キー値の新しい範囲が既に既存のマッピングの一部ではなく、データの移動が不要な場合は、新しいシャードを追加して、新しいキーまたは範囲をそのシャードに関連付けるようにすると簡単に済みます。新しいシャードを追加する方法の詳細については、「[Adding a new Shard (新しいシャードの追加)](sql-database-elastic-scale-add-a-shard.md)」をご覧ください。

ただし、データの移動を必要とするシナリオでは、必要なシャード マップの更新と共にシャード間でのデータの移動を調整するために Split-Merge サービスが必要になります。Split-Merge サービスの使用法の詳細については、「[Overview of Split-Merge (Split-Merge の概要)](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--HONumber=47-->
 