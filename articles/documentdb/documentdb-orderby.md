<properties 
	pageTitle="Order By を使用した DocumentDB データの並べ替え | Microsoft Azure" 
	description="LINQ および SQL の DocumentDB クエリで ORDER BY を使用する方法、ORDER BY クエリにインデックス作成ポリシーを指定する方法について説明します。" 
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
	ms.date="07/07/2016" 
	ms.author="arramac"/>

# Order By を使用した DocumentDB の並べ替え
Microsoft Azure DocumentDB は、JSON ドキュメントに対する、SQL を使ったドキュメント クエリをサポートしています。SQL クエリ ステートメントで ORDER BY を使って、クエリの結果を並べ替えることができます。

この記事を読むと、次の質問に回答できるようになります。

- Order By でクエリを実行する方法を教えてください。
- Order By のインデックス作成ポリシーの構成方法を教えてください。
- 今後予定された機能

[サンプル](#samples)と [FAQ](#faq) も掲載されています。

SQL クエリの完全なリファレンスについては、[DocumentDB クエリのチュートリアル](documentdb-sql-query.md)を参照してください。

## Order By を使用したクエリの実行方法
ANSI SQL 同様、DocumentDB のクエリで、SQL ステートメントにオプションで Order By 句を含められるようになりました。句に ASC/DESC 引数 (オプション) を含めて、結果を取得する順番を指定できます。

### SQL を使用した順序付け
タイトルの降順で上位 10 ブックを取得するクエリの例を次に示します。

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### SQL とフィルターを使用した順序付け
Books.ShippingDetails.Weight などのドキュメント内の入れ子になったプロパティを使って並べ替えたり、次の例のように、WHERE 句と Order By を組み合わせて追加のフィルターを指定したりできます。

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### .NET の LINQ プロバイダーを使用した順序付け
.NET SDK バージョン 1.2.0 以降を使用して、次の例のように、LINQ クエリ内で OrderBy() または OrderByDescending() 句を使用することもできます。

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB では、クエリあたり、1 つの数値プロパティ、文字列プロパティ、またはブール プロパティでの順序付けがサポートされています。これに近日公開予定のクエリの種類が加わります。詳細については、[今後予定された機能](#Whats_coming_next)を参照してください。

## Order By のインデックス作成ポリシーの構成

DocumentDB では 2 種類のインデックス (ハッシュと範囲) がサポートされていることを思い出してください。この 2 つのインデックスは、特定のパス/プロパティおよびデータ型 (文字列/数値) に対して、また異なる種類の有効桁数値 (最大有効桁数値または固定有効桁数値のいずれか) で設定できるということでした。DocumentDB は既定ではハッシュ インデックス作成を使用するようになっているため、範囲の場合、数値または文字列、あるいその両方に対して Order By を使用するためには、カスタム インデックス作成ポリシーを使用して新しいコレクションを作成する必要があります。

>[AZURE.NOTE] 文字列の範囲インデックスは、REST API のバージョン2015-06-03 により、2015 年 7 月 7 日に導入されました。文字列に対して Order By のポリシーを作成するには、.NET SDK の SDK バージョン 1.2.0、あるいは Python、Node.js、または Java SDK のバージョン 1.1.0 を使用する必要があります。
>
>REST API の 2015-06-03 より前のバージョンでは、文字列と数値の両方において既定のコレクションのインデックス作成ポリシーはハッシュとなっていました。これが、文字列についてはハッシュ、数値については範囲というように変更されました。

詳細については、「[DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)」を参照してください。

### すべてのプロパティに対する Order By のインデックスを作成する
コレクション内の JSON ドキュメント内に表示される任意のまたはすべての数値プロパティまたは文字列プロパティに対する Order By に必要な「すべての範囲」インデックス作成を使用してコレクションを作成する方法を示します。ここでは、文字列値の既定のインデックスの種類を範囲に、最大有効桁数 (-1) でオーバーライドします。
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Order By では、RangeIndex でインデックスが作成されるデータ型 (文字列と数値) の結果しか返さないので注意してください。たとえば、既定のインデックスポリシーに、数値に対する RangeIndex しか含まれていない場合、文字列値を使用したパスに対する Order By はドキュメントを返しません。
>
> コレクションに対してパーティション キーを定義してある場合、Order By は 1 つのパーティション キーに対してフィルター処理を行うクエリ内でのみサポートされることに注意してください。

### 1 つのプロパティに対する Order By のインデックス作成
Title プロパティ (文字列) のみに対する Order By のインデックス作成でコレクションを作成する方法を次に示します。2 つのパスがあります。1 つは範囲インデックス作成を使用した Title プロパティ ("/Title/?") のパスです。もう 1 つは既定インデックス作成スキーム (文字列の場合はハッシュ、数字の場合は範囲) を使用したその他のすべてのプロパティのパスです。
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## サンプル
この [Github サンプル プロジェクト](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)は、Order By を使用したインデックス作成ポリシーの作成やページングを含む、Order By の使用方法を示しています。サンプルはオープン ソースです。他の DocumentDB 開発者にも役立つような投稿でプル リクエストを送信することをお勧めします。投稿方法のガイダンスについては、[投稿に関するガイドライン](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)のページを参照してください。

## FAQ

**Order By クエリの予測される要求単位 (RU) の消費はどのくらいですか?**

Order By では参照に DocumentDB インデックスを利用するため、Order By クエリが消費する要求単位の数は Order By を使用しない場合と同程度のクエリ数になります。DocumentDB での他の操作と同様、要求単位の数は、ドキュメントのサイズまたは形態およびクエリの複雑さによって異なります。


**Order By で予測されるインデックス作成のオーバーヘッドはどのくらいですか?**

インデックス作成ストレージのオーバーヘッドは、プロパティの数に比例します。最悪のシナリオでは、インデックスのオーバーヘッドはデータの 100% です。Range/Order By インデックスと既定のハッシュ インデックス間のスループット (要求単位) オーバーヘッドに違いはありません。

**Order By を使用して DocumentDB の既存のデータにクエリを実行する方法を教えてください。**

Order By を使用してクエリ結果を並べ替えるには、並べ替えに使用されるプロパティに対してインデックスの種類として範囲を使用するように、コレクションのインデックス作成ポリシーを変更する必要があります。[インデックス作成ポリシーの変更](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection)に関する記述を参照してください。

**Order By の現在の制限を教えてください。**

プロパティが最大有効桁数 (-1) でインデックス付けされる範囲である場合、Order By は数値プロパティまたは文字列プロパティに対してのみ指定できます。

次の操作は実行できません。
 
- 内部文字列プロパティ id、\_rid、\_self (近日提供予定) に対する Order By の使用。
- 内部ドキュメントの結合の結果から取得されたプロパティに対する Order By の使用 (近日提供予定)。
- 複数のプロパティに対する Order By の使用 (近日提供予定)。
- データベース、コレクション、ユーザー、アクセス許可、または添付ファイルに対するクエリでの　Order By　の使用 (近日対応予定)。
- 計算されたプロパティ (式または UDF/組み込み関数の結果など) に対する Order By の使用。

## 次のステップ

[Github サンプル プロジェクト](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)をフォークして、データの並べ替えを始めましょう。

## 参照
* [DocumentDB クエリのリファレンス](documentdb-sql-query.md)
* [DocumentDB インデックス作成ポリシー リファレンス](documentdb-indexing-policies.md)
* [DocumentDB SQL リファレンス](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By のサンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

<!---HONumber=AcomDC_0713_2016-->