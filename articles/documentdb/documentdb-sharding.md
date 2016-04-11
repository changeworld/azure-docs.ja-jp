<properties 
	pageTitle=".NET SDK を使用して DocumentDB 内のデータをパーティション分割する方法 | Microsoft Azure" 
	description="Azure DocumentDB .NET SDK を使用して、データをパーティション分割 (シャード) したり、複数のコレクションに要求をルーティングしたりする方法について説明します。" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="arramac"/>

# .NET SDK を使用して DocumentDB 内のデータをパーティション分割する方法

Azure DocumentDB では、コレクションを[大容量のストレージとスループット](documentdb-partition-data.md)にスケールアップすることができます。ただし、パーティション分割の動作を細かく制御することにメリットがあるケースもあります。パーティション分割タスクに必要なボイラー プレート コードの量を減らすために、複数のコレクションにまたがってスケール アウトされるアプリケーションを構築しやすくする機能を .NET、Node.js、Java SDK に追加しました。

この記事では、.NET SDK のクラスとインターフェイスについて確認し、それらを使用してパーティション分割されたアプリケーションを開発する方法を見ていきます。

## DocumentDB SDK を使用したパーティション分割

パーティション分割を詳しく見る前に、パーティション分割に関連する基本的な DocumentDB の概念を確認しましょう。各 Azure DocumentDB データベース アカウントは、一連のデータベースで構成されます。それぞれのデータベースには複数のコレクションが含まれ、それぞれのコレクションにはストアド プロシージャ、トリガー、UDF、ドキュメント、および関連する添付ファイルが含まれています。コレクションは DocumentDB 内でパーティションとして扱うことができ、次のような性質を備えています。

- コレクションではパフォーマンスを分離することができます。そのため、同一コレクション内の類似したドキュメントを照合することには、パフォーマンス上のメリットがあります。たとえば、時系列データの場合、頻繁に照会する前月のデータはプロビジョニング済みの高スループットのコレクション内に配置し、古いデータはプロビジョニング済みの低スループットのコレクション内に配置することができます。
- ACID トランザクション (ストアド プロシージャやトリガー) は、コレクションをまたがることはできません。トランザクションのスコープは、コレクション内の単一のパーティション キー値内に設定されます。
- コレクションではスキーマを適用しないため、同じ種類だけでなく、異なる種類の複数の JSON ドキュメントに対しても使用できます。

[Azure DocumentDB .NET SDK のバージョン 1.1.0](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 以降では、ドキュメントの操作をデータベースに対して直接実行できます。内部では、ユーザーがデータベースに指定した PartitionResolver を使用して、[DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) が適切なコレクションに要求をルーティングします。

各 PartitionResolver クラスは、[GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx)、[ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx)、[ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx) の 3 つのメソッドを持つ [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) インターフェイスの具体的な実装です。LINQ クエリと ReadFeed 反復子では、ResolveForRead メソッドを内部的に使用して、要求のパーティション キーと一致するすべてのコレクションを反復処理します。同様に、作成操作では、ResolveForCreate メソッドを使用して、適切なパーティションに作成をルーティングします。置換、削除、および読み取りでは、使用するドキュメント内に当該コレクションへの参照が既に含まれているため、必要な変更はありません。

SDK には、正規のパーティション分割手法であるハッシュ参照と範囲参照をそれぞれサポートする [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) と [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) の 2 つのクラスも含まれています。これらのクラスを使用すれば、パーティション分割ロジックをアプリケーションに簡単に追加できます。

## パーティション分割ロジックの追加と PartitionResolver の登録 

以下は、[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) を作成し、データベースの DocumentClient に登録する方法を示したスニペットです。

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## パーティションでのドキュメントの作成  

PartitionResolver が登録されたら、以下に示すように、データベースに対して直接作成とクエリを実行できます。この例では、SDK は PartitionResolver を使用して、ユーザー ID を抽出してハッシュを生成し、その値を使用して適切なコレクションに作成操作をルーティングしています。

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## パーティションに対するクエリの作成  

[CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) メソッドに対象のデータベースとパーティション キーを渡して、クエリを実行できます。クエリは、データベース内の、パーティション キーにマップされたコレクションすべてに対して結果セットを 1 つ返します。

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## データベース内のすべてのコレクションに対するクエリの作成 

次に示すように、パーティション キーの引数を省略することで、データベース内のすべてのコレクションに対してクエリを実行し、結果を列挙できます。

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## ハッシュ パーティション リゾルバー
ハッシュ パーティション分割では、ハッシュ関数の値に基づいてパーティションが割り当てられるため、要求やデータを複数のパーティションに均等に分配できます。このアプローチは一般的に、多種多様なクライアントによって生成または消費されるデータのパーティション分割に使用され、ユーザー プロファイル、カタログ項目、IoT (Internet of Things: モノのインターネット) テレメトリ データなどを格納するのに役立ちます。

**ハッシュ パーティション分割:** ![ハッシュ パーティション分割で要求が複数のパーティションに均等に分配されることを示す図](media/documentdb-sharding/partition-hash.png)

*N* 個のコレクションに対するシンプルなハッシュ パーティション スキームでは、ドキュメントに対し、*hash(d) mod N* を計算して配置するコレクションを決定します。このシンプルな手法には、コレクションの追加や削除ではうまく機能しないという問題点があります。これらの操作では、ほぼすべてのデータを再配置する必要が生じるためです。[コンシステント ハッシュ](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738)というよく知られたアルゴリズムでは、この問題への対処として、コレクションの追加や削除の実行中に必要となるデータ移動の量を最小限に抑えるハッシュ スキームが実装されています。

[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) クラスには、[IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx) インターフェイスで指定されたハッシュ関数に対してコンシステント ハッシュ リングを構築するロジックが実装されています。HashPartitionResolver は既定で MD5 ハッシュ関数を使用しますが、独自のハッシュ実装に置き換えることができます。HashPartitionResolver の内部では、16 個のハッシュが "仮想ノード" のように各コレクションのハッシュ リング上に作成されます。これにより、複数のコレクションに対してドキュメントが均等に分配されます。ハッシュの個数を変更して、クライアント側の計算量とデータの偏りのバランスをとることもできます。

**HashPartitionResolver を使用したコンシステント ハッシュ:** ![HashPartitionResolver によるハッシュ リング作成を示す図](media/documentdb-sharding/HashPartitionResolver.JPG)

## 範囲パーティション リゾルバー

範囲パーティション分割では、パーティション キーが特定の範囲内にあるかどうかに基づいてパーティションが割り当てられます。この手法は、タイム スタンプ プロパティ (たとえば、2015 年 4 月 1 日から 2015 年 4 月 14 日までの eventTime) を用いたパーティション分割に使用されるのが一般的です。[RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) クラスを使用して、Range<T> とコレクションの自己リンクとのマッピングを維持できます。

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) は、文字列や数値などあらゆる型の範囲を管理するシンプルなクラスで、IComparable<T> と IEquatable<T> が実装されています。読み取りと作成では、任意の範囲を渡すことができ、要求された範囲とパーティションの範囲の交差がリゾルバーによって特定されて、候補となるすべてのコレクションが識別されます。この機能は、時系列データに対する範囲クエリを実行する際に役立ちます。

**範囲パーティション分割:**

![範囲パーティション分割で要求が複数のパーティションに均等に分配されることを示す図](media/documentdb-sharding/partition-range.png)

範囲パーティション分割の特殊なケースとして、範囲が単一の離散値のみの場合があります。このようなケースは "検索パーティション分割" とも呼ばれます。この手法は、リージョンごとのパーティション分割 (たとえば、ノルウェー、デンマーク、スウェーデンを含むスカンジナビア地域についてのパーティション) か、マルチ テナント アプリケーション内のテナントのパーティション分割に使用されるのが一般的です。

## サンプル 

[DocumentDB パーティション分割のサンプル GitHub プロジェクト](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)をご覧ください。このプロジェクトには、ここで説明した PartitionResolver を使用する方法と、これらを拡張し、以下のような特定のユース ケースに合わせて独自のリゾルバーを実装する方法について、コード スニペットが記載されています。

* GetPartitionKey に任意のラムダ式を指定し、これを使用して複合パーティション キーを実装したり、複数の種類のオブジェクトをそれぞれ異なる方法でパーティション分割したりする方法。
* 手動で作成したルックアップ テーブルを使用してパーティション分割を実行するシンプルな [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) を作成する方法。このパターンは一般的に、リージョン、テナント ID、アプリケーション名などの離散値に基づくパーティション分割に使用されます。
* テンプレートに基づいて自動的にコレクションを作成する [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) を作成する方法。テンプレートでは、新しいコレクションに対して登録する必要がある名前付けスキーム、IndexingPolicy、およびストアド プロシージャを定義します。
* 古いコレクションがいっぱいになったときにのみ新しいコレクションを作成する、スキームのない [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) を作成する方法。
* PartitionResolver の状態を JSON としてシリアル化および逆シリアル化する方法。これにより、状態をプロセス間で共有したり、シャットダウンをまたいで維持したりできます。状態は、構成ファイルだけでなく、DocumentDB コレクションにも保持できます。
* コンシステント ハッシュに基づいてパーティション分割されたデータベースに対してパーティションの動的な追加や削除を実行するための [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) クラス。内部的には [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) を使用し、移行時に 4 つのモードのいずれかを使用して読み取りや書き込みをルーティングします。4 つのモードとは、古いパーティション スキームからの読み取り (ReadCurrent)、新しいパーティション スキームからの読み取り (ReadNext)、両方の結果の結合 (ReadBoth)、移行中は使用不可 (None) です。

サンプルはオープン ソースです。他の DocumentDB 開発者にも役立つような投稿でプル リクエストを送信することをお勧めします。投稿方法のガイダンスについては、[投稿に関するガイドライン](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)のページを参照してください。

>[AZURE.NOTE] コレクションの作成は DocumentDB によって速度が制限されているため、ここに示したサンプル メソッドの一部では、処理が完了するまでに数分かかる場合があります。

##FAQ
** DocumentDB はサーバー側のパーティション分割をサポートしていますか。**

はい、DocumentDB は[サーバー側のパーティション分割](documentdb-partition-data.md)をサポートしています。より高度なユース ケースとしては、クライアント側のパーティション リゾルバーを介したクライアント側のパーティション分割もサポートしています。

** サーバー側のパーティション分割とクライアント側のパーティション分割はどのように使い分ければよいですか。** ほとんどのケースでサーバー側のパーティション分割をお勧めしています。データのパーティション分割とルーティング要求の管理タスクを扱えるためです。ただし、範囲パーティション分割が必要な場合や、パーティション キーの値に応じてパフォーマンスを分離するような特別なユースケースがある場合など、クライアント側のパーティション分割の方が適している場合もあります。

**パーティション スキームへのコレクションの追加や削除はどのようにすればよいですか。**

パーティション再分割を実装する方法の例については、サンプル プロジェクト内の DocumentClientHashPartitioningManager の実装を参照してください。

**パーティション分割構成を保持したり、他のクライアントと共有したりするにはどのようにすればよいですか。**

パーティションの状態を JSON としてシリアル化し、構成ファイルのほか DocumentDB コレクションにも格納できます。この例については、サンプル プロジェクト内の RunSerializeDeserializeSample メソッドを参照してください。

**さまざまなパーティション分割手法を連結するにはどのようにすればよいですか。**

複数の PartitionResolver を連結するには、1 つ以上の既存のリゾルバーを内部で使用する独自の IPartitionResolver を実装します。この例については、サンプル プロジェクト内の TransitionHashPartitionResolver を参照してください。

##参照
* [DocumentDB を使用したデータのパーティション分割](documentdb-partition-data.md)
* [DocumentDB のコレクションとパフォーマンス レベル](documentdb-performance-levels.md)
* [GitHub のパーティション分割のコード サンプル](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [MSDN の DocumentDB .NET SDK に関するドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET のサンプル](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB の制限](documentdb-limits.md)
* [パフォーマンスに関するヒントについての DocumentDB ブログ](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=AcomDC_0330_2016-->