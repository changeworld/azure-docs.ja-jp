<properties 
	pageTitle="Order By を使用した DocumentDB データの並べ替え | Azure" 
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
	ms.date="06/04/2015" 
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
PublishTimestamp の降順でブックを取得するクエリの例を次に示します。

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

### SQL とフィルターを使用した順序付け
Books.ShippingDetails.Weight などのドキュメント内の入れ子になったプロパティを使って並べ替えたり、次の例のように、WHERE 句と Order By を組み合わせて追加のフィルターを指定したりできます。

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### .NET の LINQ プロバイダーを使用した順序付け
.NET SDK バージョン 1.2.0 以降を使用して、次の例のように、LINQ クエリ内で OrderBy() または OrderByDescending() 句を使用することもできます。

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### ページングと .NET SDK を使用した順序付け
DocumentDB SDK 内でネイティブ ページング サポートを使用して、次のような .NET コード スニペットで、一度に 1 ページの結果を取得できます。この例では、FeedOptions.MaxItemCount と IDocumentQuery インターフェイスを使用して一度に最大 10 個の結果を取得します。

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB では数値型 (文字列ではなく) に対する順序付けをサポートし、また機能のこのプレビューでは、クエリあたり 1 つの Order By プロパティのみをサポートしています。詳細については、[今後予定された機能](#Whats_coming_next)を参照してください。

## Order By のインデックス作成ポリシーの構成
Order By クエリを実行するには、次のいずれかの操作を行います。

- 最大有効桁数によりドキュメント内の特定のパスのインデックスを作成します。または、 
- 最大有効桁数によりコレクション全体の*すべて*のパスを再帰的にインデックス作成します。 

最大有効桁数 (JSON 構成で有効桁数 -1 として表される) は、インデックス作成される値に基づいて可変のバイト数を利用します。そのため、次のようになります。

- 数値の大きいプロパティ (エポック タイムスタンプなど) では、最大有効桁数のインデックス オーバーヘッドは高くなります。 
- 数値の小さいプロパティ (列挙値、ゼロ、郵便番号、年齢など) では、最大有効桁数のインデックス オーバーヘッドは低くなります。

詳細については、「[DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)」を参照してください。

### すべての数値プロパティに対する Order By のインデックスを作成する
任意の (数値) プロパティに対する Order By のインデックス作成でコレクションを作成する方法を次に示します。
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### 1 つのプロパティに対する Order By のインデックス作成
PublishTimestamp プロパティのみに対する Order By のインデックス作成でコレクションを作成する方法を次に示します。

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## サンプル
この [Github サンプル プロジェクト](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)は、Order By を使用したインデックス作成ポリシーの作成やページングを含む、Order By の使用方法を示しています。サンプルはオープン ソースです。他の DocumentDB 開発者にも役立つような投稿でプル リクエストを送信することをお勧めします。投稿方法のガイダンスについては、[投稿に関するガイドライン](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)のページを参照してください。

## 今後予定された機能

今回導入された Order By サポートは、今後のサービス更新で拡張される予定です。現在、次の追加機能に取り組んでおり、お客様からのフィードバックを基に、これらの機能強化のリリースを優先して行う予定です。

- 動的なインデックス作成ポリシー: コレクション作成後のインデックス作成ポリシーの変更のサポート
- 文字列の範囲インデックス: 文字列値に対する範囲クエリ (>、<、>=、<=) をサポートするインデックス作成。これをサポートするために、インデックス作成ポリシーに新しいより高度なスキーマを導入する予定です。
- DocumentDB クエリでの文字列の Order By のサポート。
- Azure プレビュー ポータルを使用してインデックス作成ポリシーを更新できるようにする。
- 複合インデックスのサポートによる、より効率的な Order By の実現と、複数のプロパティでの Order By のサポート

## FAQ

**並べ替えがサポートされた SDK のプラットフォームまたはバージョンを教えてください。**

Order By はサーバー側で更新されるため、この機能の使用にあたり SDK の新しいバージョンをダウンロードする必要はありません。サーバー側 JavaScript SDK を含むすべての SDK のプラットフォームとバージョンで、SQL クエリ文字列を使用して Order By を使用できます。LINQ を使用している場合は、Nuget から バージョン 1.2.0 以降をダウンロードする必要があります。

**Order By クエリの予測される要求単位 (RU) の消費はどのくらいですか?**

Order By では参照に DocumentDB インデックスを利用するため、Order By クエリが消費する要求単位の数は Order By を使用しない場合と同程度のクエリ数になります。DocumentDB での他の操作と同様、要求単位の数は、ドキュメントのサイズまたは形態およびクエリの複雑さによって異なります。


**Order By で予測されるインデックス作成のオーバーヘッドはどのくらいですか?**

インデックス作成ストレージのオーバーヘッドは、数値プロパティの数に比例します。最悪のシナリオでは、インデックスのオーバーヘッドはデータの 100% です。Range/Order By インデックスと既定のハッシュ インデックス間のスループット (要求単位) オーバーヘッドに違いはありません。

**今回の変更は Order By を使用しないクエリに影響しますか?**

Order By を使用しないクエリの動作に何の変更も導入されません。この機能のリリース以前は、すべての DocumentDB クエリで返される結果は ResourceId (_rid) 順でした。Order By を使用すると、クエリは指定した順番で返されます。Order By クエリでは、_同じ値を持つ複数のドキュメントが返された場合、2 番目の並べ替え順序として rid が使用されます。

**Order By を使用して DocumentDB の既存のデータにクエリを実行する方法を教えてください。**

これは、「[今後予定された機能](what's-coming-next)」で説明された動的なインデックス作成ポリシーの機能強化でサポートされる予定です。これを現時点で行うには、データをエクスポートし、Range/Order By インデックスを使用して作成した新しい DocumentDB コレクションにそれを再インポートする必要があります。DocumentDB インポート ツールを使用して、コレクション間でデータを移行できます。

**Order By の現在の制限を教えてください。**

Order By は数値プロパティに対してのみ指定でき、また最大有効桁数 (-1) のインデックスによる範囲インデックス作成の場合にのみ指定できます。Order By はドキュメント コレクションに対してのみサポートされています。

次の操作は実行できません。
 
- 文字列プロパティに対する Order By の使用 (近日提供予定)。
- 内部文字列プロパティ (id、_rid、_self (近日提供予定) に対する Order By の使用。- 内部ドキュメントの結合の結果から取得されたプロパティに対する Order By の使用 (近日提供予定)。
- 複数のプロパティに対する Order By の使用 (近日提供予定)。
- 計算されたプロパティ (式または UDF/組み込み関数の結果など) に対する Order By の使用。
- データベース、コレクション、ユーザー、アクセス許可、または添付ファイルに対するクエリでの　Order By　の使用。

## 次のステップ

[Github サンプル プロジェクト](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)をフォークして、データの並べ替えを始めましょう。

## 参照
* [DocumentDB クエリのリファレンス](documentdb-sql-query.md)
* [DocumentDB インデックス作成ポリシー リファレンス](documentdb-indexing-policies.md)
* [DocumentDB SQL リファレンス](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By のサンプル](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->