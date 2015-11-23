<properties 
	pageTitle="Azure Search Indexer のカスタマイズ | Microsoft Azure | ホスト型クラウド検索サービス" 
	description="Microsoft Azure のホスト型クラウド検索サービスである Azure Search のインデクサーの設定とポリシーをカスタマイズする方法について説明します。" 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="eugenesh"/>

#Azure Search Indexer のカスタマイズ

Azure Search でのインデクサーの構成では、データソースとターゲット インデックス間でフィールド名を変更すること、データベース テーブルの文字列を文字列コレクションに変換すること、データソースの変更検出ポリシーを切り替えること、URL として安全でない文字が含まれているドキュメント キーを URL エンコードすること、一部のドキュメントに対するインデックス エラーを許容することを実行できます。

Azure Search インデクサーに慣れていない場合は、まず次の記事をご覧ください。

- [インデクサーを使用した Azure Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [インデクサーを使用した DocumentDB と Azure Search の接続](../documentdb/documentdb-search-indexer.md)
- [インデクサーのサポートを含む .NET SDK)](https://msdn.microsoft.com/library/dn951165.aspx) または 
- [インデクサー操作 (Azure Search サービス REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##データソースとターゲット インデックス間のフィールド名を変更する##

**フィールド マッピング**は、フィールド定義の違いを調整するプロパティです。最も一般的な例では、Azure Search の名前付け規則に違反しているフィールド名が記載されています。アンダースコアで始まる 1 つ以上のフィールド名のあるソース テーブルがあるとします (`_id` など)。Azure Search ではアンダースコアで始まるフィールド名は許可されません。そのため、このフィールドの名前を変更する必要があります。

次の例では、インデクサーを更新して、データソースの `_id` フィールドをターゲット インデックスの `id` フィールドに "名前を変更する" フィールド マッピングを含めることについて説明します。

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**注:** フィールド マッピングを使用するには、バージョン 2015-02-28-Preview のプレビュー API を使用する必要があります。

複数のフィールド マッピングを指定できます。

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

ソースとターゲットの両方のフィールド名で大文字と小文字が区別されます。

##データベース テーブルの文字列を文字列のコレクションに変換する##

フィールド マッピングでは、*マッピング関数*を利用し、ソースのフィールド値を変換することもできます。

このような関数の 1 つとして、`jsonArrayToStringCollection` は JSON 配列として書式設定されている文字列を含むフィールドを解析してターゲット インデックスの Collection(Edm.String) フィールドにします。特に Azure SQL インデクサーで使用することを意図しています。SQL にはネイティブのコレクション データ型がないためです。次のように使用できます。

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

たとえば、ソース フィールドに文字列 `["red", "white", "blue"]` が含まれている場合、`Collection(Edm.String)` 型のターゲット フィールドに `"red"`、`"white"`、`"blue"` の 3 つの値が入力されます。

`targetFieldName` プロパティは任意です。省略した場合、`sourceFieldName` 値が使用されます。

##データソースの変更検出ポリシーを切り換える##
  
Azure Search では、採用する変更検出ポリシーの決定は、データソースとデータ スキーマでサポートされている内容によって主に決定されます。時間の経過と共に、特にデータベースをアップグレードまたは移行する場合は、変更検出ポリシーの別の種類への切り替えが必要になることがあります。たとえば、Azure SQL Database を統合された変更の追跡をサポートする新しいバージョンに更新したばかりの場合、高基準ポリシーから、統合された変更追跡ポリシーへの切り替えが必要になることがあります。または、自分の高基準とは異なる列を使用することもあります。

PUT データソース REST APIを呼び出してデータソースを更新する場合、次のようなエラー メッセージを含む応答 400 を受け取る可能性があります。


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 おそらく、この応答の意味と、回避する方法について疑問が生じます。このエラーは、Azure Search が変更検出ポリシーと関連付けられている内部状態を維持するために発生します。ポリシーが変更されると、新しいポリシーには適用されないため、既存の状態は無効になります。つまり、インデクサーは新しいポリシーを使用して最初からデータ ソースのインデックス化を始める必要があります。これにより、データベースの追加の負荷や追加のネットワーク帯域幅料金などの影響が考えられます。[Reset Indexer API](https://msdn.microsoft.com/library/azure/dn946897.aspx) を呼び出して現在の変更検出ポリシーと関連付けられた状態をリセットするよう Azure Search が要求するのはこのためです。その後、ポリシーを通常の PUT データソースの呼び出しで変更できます。もちろん、Azure Search で自動的にリセットできますが、マイクロソフトは Reset API を呼び出すことによる影響を明確にご理解いただくことが重要だと考えています。

##URL の安全でない文字が含まれている、URL エンコードのドキュメント キー##

Azure Search ではユーザーがキーを使用してドキュメントを参照する必要があるため、ドキュメントのキー フィールド内の文字を URL の安全な文字に制限しています。インデックスを作成するドキュメントのキー フィールドにそのような文字が含まれている場合はどうなりますか。 クライアント SDK または REST API を使用しているドキュメントのインデックスを作成している場合は、キーを URL エンコードできます。インデクサーを使用して、インデクサーを作成または更新するとき、**base64EncodeKeys** パラメーターを `true` に設定して、Azure Search にキーを URL エンコードするよう知らせることができます。

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

エンコードの詳細については、こちらの「[MSDN の記事](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)」をご覧ください。

注: キーの値を検索またはフィルターする必要がある場合、要求で使用されるキーを同じようにエンコードし、要求が検索インデックスに保存されているエンコードされた値と一致するようにする必要があります。


##いくつかのドキュメントのインデックスを作成するようエラーを許容する##

既定では、1 つのドキュメントがインデックス作成に失敗するとすぐに、Azure Search インデクサーが停止します。たとえば、データソース全体に繰り返しインデックスを作成しなおす場合など、シナリオによってはいくつかのエラーに対応できます。Azure Search は、この動作を微調整する 2 つのインデクサー パラメーターを提供します。

- **maxFailedItems**: インデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であると見なされます。既定値は 0 です。
- **maxFailedItemsPerBatch**: 1 つのバッチでのインデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であると見なされます。既定値は 0 です。

インデクサーを作成または更新するとき、これらのパラメーターのいずれかまたは両方を指定して、いつでもこの値を変更できます。

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

いくつかのエラーを許容する場合でも、失敗したドキュメントについての情報が [Get Indexer Status API](https://msdn.microsoft.com/library/azure/dn946884.aspx) によって返されます。

以上です。今後のコンテンツについてアイデアやご意見をお持ちの場合、内容をハッシュタグ #AzureSearch を付けてツイートするか、[UserVoice ページ](http://feedback.azure.com/forums/263029-azure-search)で送信できます。
 

<!---HONumber=Nov15_HO3-->