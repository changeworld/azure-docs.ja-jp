<properties 
	pageTitle="DocumentDB リソースの RESTful な操作 | Microsoft Azure" 
	description="この RESTful Web サービス チュートリアルで HTTP メソッドを確認してください。HTTP 動詞を使用して Microsoft Azure DocumentDB リソースの RESTful な操作を行う方法について説明します。"
	keywords="http メソッド, restful サービス チュートリアル, restful web サービス チュートリアル, http 動詞, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="h0n"/>

# RESTful Web サービス チュートリアル: DocumentDB リソースの RESTful な操作 

DocumentDB では、HTTP メソッドを使用して、DocumentDB リソースの作成、読み取り、置換、取得、および削除を行うことができます。

この RESTful Web サービス チュートリアルを読むと、次の質問に回答できるようになります。

- 標準の HTTP メソッドでどのように DocumentDB リソースを操作するか。
- POST を使用してどのように新しいリソースを作成するか。
- POST を使用してどのようにストアド プロシージャを登録するか。
- DocumentDB でどのように同時実行制御がサポートされるか。
- HTTPS と TCP にはどのような接続オプションがあるか。

## HTTP 動詞の概要
DocumentDB リソースでサポートされる HTTP 動詞とその標準的な解釈を次に示します。

1.	POST: 新しいアイテム リソースを作成します。 
2.	GET: 既存のアイテム リソースまたはフィード リソースを読み取ります。 
3.	PUT: 既存のアイテム リソースを置き換えます。 
4.	DELETE: 既存のアイテム リソースを削除します。
5.	HEAD: GET から応答ペイロードを除外したものです (返されるのはヘッダーのみ)。 

>[AZURE.NOTE]将来的には、PATCH による選択的更新機能を追加する予定です。

下の HTTP 動詞に関する図に示したように、POST の発行対象はフィード リソースに限られ、また、PUT と DELETE の発行対象はアイテム リソースに限られます。GET と HEAD は、フィード リソースとアイテム リソースのどちらに対しても発行することができます。

![この RESTful Web サービス チュートリアルにおける HTTP 動詞の概要][1]

**標準の HTTP メソッドを使用した対話モデル**

## POST HTTP メソッドを使用した新しいリソースの作成 
対話モデルに関する理解を深めるために、新しいリソースの作成 (INSERT) を例に考えてみましょう。新しいリソースを作成するためには、そのリソースの従属先コンテナー フィードの URI に対し、HTTP POST 要求を発行する必要があります。要求本文に、作成するリソースの表現を含めます。この要求に必要なプロパティは、リソースの id だけです。

例として、新規のデータベースを作成するためには (id プロパティに一意の名前を設定することで) /dbs に対してデータベース リソースを POST します。同様に、新しいコレクションを作成するためには */dbs/\_rid/colls/* へコレクション リソースを POST するという具合です。応答には、完全にコミットされたリソースが、システムによって生成されたプロパティと共に格納されます。たとえば、返されたリソースの *\_self* リンクを使用すれば、他のリソースにナビゲートすることができます。ここで、単純な HTTP ベースの対話モデルの例を挙げます。クライアントは、以下のような HTTP 要求を発行することで、アカウント内に新しいデータベースを作成することができます。

```
	POST https://fabrikam.documents.azure.com/dbs
	{
	      "id":"MyDb"
	}

The DocumentDB service responds with a successful response and a status code indicating successful creation of the database.  

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/UoEi5w==/",
	      "_ts": 1407370063,
	      "_etag": "00000100-0000-0000-0000-54b636600000",
	      "_colls": "colls/",
	      "_users": "users/"
	}
```
  
## POST HTTP メソッドを使用したストアド プロシージャの登録
リソースの作成と実行の例をもう 1 つ紹介します。すべて JavaScript で記述されている簡単な "HelloWorld" ストアド プロシージャを考えてみましょう。

```
 	function HelloWorld() {
 	var context = getContext();
 	var response = context.getResponse();
 	
        response.setBody("Hello, World");
     }
```

MyDb 下のコレクションにストアド プロシージャを登録するには、*/dbs/\_rid-db/colls/\_rid-coll/sprocs* に対して POST を発行します。

```
	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs HTTP/1.1
	
	{
	  "id": "HelloWorld",
	  "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }"
	}
```

DocumentDB サービスは、さまざまな情報を含んだ応答とストアド プロシージャの登録に成功したことを示す状態コードを返します。

```
	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	       "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }",
	      "id": "HelloWorld"
	      "_rid": "UoEi5w+upwABAAAAAAAAgA==",
	      "_ts" :  1421227641
	      "_self": "dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/",
	      "_etag": "00002100-0000-0000-0000-50f71fda0000"
	}
```

## POST HTTP メソッドを使用したストアド プロシージャの実行
上の例のストアド プロシージャを実行するには、下図のようにストアド プロシージャ リソースの URI (/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/) に対して POST を発行する必要があります。

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA== HTTP/1.1
	
DocumentDB サービスから、次のような応答が返されます。

	HTTP/1.1 200 OK
	
	"Hello World"

新しいリソースの作成とストアド プロシージャの実行だけでなく、SQL クエリの発行にも、POST HTTP 動詞が使用されます。SQL クエリの実行例を示したのが、以下のコードです。

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	x-ms-documentdb-query-enable-scan: True
	Content-Type: application/query+json
	...
	
	{"query":"SELECT f.LastName AS Name, f.Address.City AS City FROM Families f WHERE f.id='AndersenFamily' OR f.Address.City='NY'"}

サービスからは、SQL クエリの実行結果が返されます。

```
	HTTP/1.1 200 Ok
	...
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 4
	x-ms-request-charge: 3.1
	Content-Type: application/json1
	...
	{"_rid":"UoEi5w+upwA=","Documents":[{"Name":"Andersen","City":"Seattle"},{"Name":"Wakefield","City":"NY"}],"_count":2}
```


## PUT、GET、および DELETE HTTP 動詞の使用
リソースの置き換えと読み取りはそれぞれ、リソースの *\_self* リンクに対し、PUT 動詞 (と適切な要求本文) と GET 動詞を発行することによって行います。同様に、リソースを削除するときは、その *\_self* リンクに対して DELETE 動詞を発行します。DocumentDB のリソース モデルでは、リソースが階層状に編成されていることから、連鎖削除が必然的にサポートされます。連鎖削除においては、上位のリソースを削除すると、それに従属するリソースが削除されます。従属リソースは、その上位のリソースとは別のノードに分散している場合があるため、削除されるまでに時間差が生じることがあります。ガベージ コレクションの働きに関係なく、リソースを削除するとすぐにクォータが解放され、利用できる状態になります。参照整合性はシステムによって維持されます。たとえば、削除 (または置換) されたデータベースにコレクションを挿入したり、存在しないコレクション内のドキュメントを照会したりすることはできません。
 
リソースのフィードに対して GET を発行した場合やコレクションを照会した場合、膨大な数のアイテムが結果として得られる可能性があります。それらすべてのアイテムを 1 回のラウンドトリップ (要求と応答のやり取り) で返し、利用することは、サーバーにとっても、クライアントにとっても現実的ではありません。この問題に対処するために、DocumentDB では、大きなフィードをクライアントがページ単位で読み込めるようになっています。クライアントは、[x-ms-continuation] という応答ヘッダーをカーソルとして使用し、後続のページにナビゲートすることができます。

## オプティミスティック同時実行制御
ほとんどの Web アプリケーションは、エンティティ タグ ベースのオプティミスティック同時実行制御によって、"Lost Update (失われた更新)" や "Lost Delete (失われた削除)" と呼ばれる悩ましい問題に対処しています。エンティティ タグは、リソースに関連付けられる論理上のタイムスタンプで、HTTP で手軽に使うことができます。DocumentDB は、特定のリソースに関連付けられたバージョンを絶えずすべての HTTP 応答に含めることでオプティミスティック同時実行制御をネイティブにサポートします。次のケースでは、同時実行制御の競合が正しく検出されます。

1.	2 つのクライアントが 1 つのリソースに対し、最新 ([if-match] 要求ヘッダーで指定) バージョンのリソースを使った (PUT/ DELETE 動詞による) 変更要求を同時に発行した場合、DocumentDB データベース エンジンがそれらに対し、トランザクション型の同時実行制御を適用します。
2.	クライアントから提示されたリソースのバージョンが、[if-match] 要求ヘッダーに指定されたバージョンよりも古い場合、その要求は拒否されます。  

## 接続オプション
DocumentDB に採用されている論理アドレス モデルでは、すべてのリソースが、その *\_self* リンクで識別される不変の論理 URI を持ちます。分散ストレージ システムは複数のリージョンにまたがって存在するため、DocumentDB では、各データベース アカウント下に置かれたリソースをパーティション分割して多数のコンピューターに振り分け、各パーティションをレプリケートすることで、高可用性を実現しています。物理的なアドレスは、特定のパーティションのリソースを管理するレプリカによって登録されます。物理的なアドレスは、障害の発生によって、時間の経過と共に変化しますが、論理上のアドレスは一定で変化しません。論理アドレスから物理アドレスへの変換は、ルーティング テーブルで管理されます。ルーティング テーブルも内部的にはリソースとして存在します。DocumentDB では、次の 2 つの接続モードが公開されています。

1.	**ゲートウェイ モード:** 論理アドレスから物理アドレスへの変換やルーティングの細部は、クライアントからは見えません。クライアントは単純に論理 URI を操作し、RESTful にリソース モデルをナビゲートします。クライアントは、論理 URI を使用して要求を発行すると、エッジ コンピューターが論理 URI をレプリカの物理アドレスに変換します。リソースの管理と要求の転送はレプリカによって行われます。ルーティング テーブルはエッジ コンピューターによってキャッシュされる (定期的に更新される) ため、ルーティングはきわめて効率的です。 
2.	**直接接続モード:** クライアントが直接そのプロセス空間でルーティング テーブルを管理し、定期的に更新します。クライアントは直接レプリカに接続し、エッジ コンピューターをバイパスできます。   


接続モード|プロトコル|詳細|DocumentDB SDK
---|---|---|---
ゲートウェイ|HTTPS|アプリケーションは、論理 URI を使用してエッジ ノードに直接接続します。そのためネットワーク ホップが余分に発生します。|REST API、.NET、Node.js、Java、Python、JavaScript
直接接続|HTTPS および TCP|アプリケーションは、ルーティング テーブルに直接アクセスし、クライアント側でルーティングを実行してレプリカに直接接続することができます。|.NET


## 次のステップ
REST API を使用してリソースを操作する方法については、「[Azure DocumentDB REST API リファレンス](https://msdn.microsoft.com/library/azure/dn781481.aspx)」を参照してください。

## 参照
- [Azure DocumentDB REST API リファレンス](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
- [DocumentDB のクエリ](../documentdb-sql-query/)
- [DocumentDB SQL リファレンス](https://msdn.microsoft.com/library/azure/dn782250.aspx)
- [DocumentDB のプログラミング: ストアド プロシージャ、トリガー、UDF](../documentdb-programming/)
- [Azure DocumentDB リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- REST [http://en.wikipedia.org/wiki/Representational\_state\_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
- JSON 仕様 [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
- HTTP 仕様 [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
- エンティティ タグ [http://en.wikipedia.org/wiki/HTTP\_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
 

<!---HONumber=Oct15_HO4-->