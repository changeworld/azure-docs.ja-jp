<properties 
	pageTitle="DocumentDB リソースの RESTful な操作 | Azure" 
	description="HTTP 動詞を使用して Microsoft Azure DocumentDB リソースの RESTful な操作を行う方法について説明します。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#DocumentDB リソースの RESTful な操作 

DocumentDB では、HTTP メソッドを使用して、DocumentDB リソースの作成、読み取り、置換、取得、および削除を行うことができます。

この記事を読むと、次の質問の答えを見つけることができます。

- 標準の HTTP メソッドでどのように DocumentDB リソースを操作するか。
- POST を使用してどのように新しいリソースを作成するか。
- POST を使用してどのようにストアド プロシージャを登録するか。
- DocumentDB でどのように同時実行制御がサポートされるか。
- HTTPS と TCP にはどのような接続オプションがあるか。

##HTTP 動詞の概要
DocumentDB リソースでサポートされる HTTP 動詞とその標準的な解釈を次に示します。

1.	POST: 新しいアイテム リソースを作成します。 
2.	GET: 既存のアイテム リソースまたはフィード リソースを読み取ります。 
3.	PUT: 既存のアイテム リソースを置き換えます。 
4.	DELETE: 既存のアイテム リソースを削除します。
5.	HEAD: GET から応答ペイロードを除外したものです (返されるのはヘッダーのみ)。 

>[AZURE.NOTE] 将来的には、PATCH による選択的更新機能を追加する予定です。  

下の図に示したように、POST の発行対象はフィード リソースに限られ、また、PUT と DELETE の発行対象はアイテム リソースに限られます。GET と HEAD は、フィード リソースとアイテム リソースのどちらに対しても発行することができます。 

![][1]  

**標準の HTTP メソッドを使用した対話モデル**

##POST を使用した新しいリソースの作成 
対話モデルに関する理解を深めるために、新しいリソースの作成 (INSERT) を例に考えてみましょう。新しいリソースを作成するためには、そのリソースの従属先コンテナー フィードの URI に対し、HTTP POST 要求を発行する必要があります。要求本文に、作成するリソースの表現を含めます。この要求に必要なプロパティは、リソースの id だけです。  

たとえば、新しいデータベースを作成するには、/dbs に対し、(id プロパティを一意の名前で設定することによって) データベース リソースを POST します。同様に、新しいコレクションを作成するときも、/dbs/_rid/colls/ に対してコレクション リソースを POST します。応答には、完全にコミットされたリソースが、システムによって生成されたプロパティと共に格納されます。たとえば、返されたリソースの _self リンクを使用すれば、他のリソースにナビゲートすることができます。ここで、単純な HTTP ベースの対話モデルの例を挙げます。クライアントは、以下のような HTTP 要求を発行することで、アカウント内に新しいデータベースを作成することができます。  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

DocumentDB サービスは、さまざまな情報を含んだ応答とデータベースの作成に成功したことを示すステータス コードを返します。  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": ""00002100-0000-0000-0000-50e71fda0000"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
##POST を使用したストアド プロシージャの登録
リソースの作成と実行の例をもう 1 つ紹介します。次のストアド プロシージャを考えてみましょう。すべて JavaScript で記述しています。   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

MyDb 下のコレクションにストアド プロシージャを登録するには、/dbs/_rid-db/colls/_rid-coll/sprocs に対して POST を発行します。 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
DocumentDB サービスは、さまざまな情報を含んだ応答とストアド プロシージャの登録に成功したことを示すステータス コードを返します。  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": ""00002100-0000-0000-0000-50f71fda0000"",
	       ...
	}

##POST を使用したストアド プロシージャの実行
上の例のストアド プロシージャを実行するには、ストアド プロシージャ リソースの URI (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1) に対して POST を発行する必要があります。次のコードに例を示します。  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

DocumentDB サービスから、次のような応答が返されます。  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

新しいリソースの作成とストアド プロシージャの実行だけでなく、SQL クエリの発行にも、POST 動詞が使用されます。SQL クエリの実行例を示したのが、以下のコードです。  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

サービスからは、SQL クエリの実行結果が返されます。   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


##PUT、GET、および DELETE の使用
リソースの置き換えと読み取りはそれぞれ、リソースの _self リンクに対し、PUT 動詞 (と適切な要求本文) と GET 動詞を発行することによって行います。同様に、リソースを削除するときは、その _self リンクに対して DELETE 動詞を発行します。DocumentDB のリソース モデルでは、リソースが階層状に編成されていることから、連鎖削除が必然的にサポートされます。連鎖削除においては、上位のリソースを削除すると、それに従属するリソースが削除されます。従属リソースは、その上位のリソースとは別のノードに分散している場合があるため、削除されるまでに時間差が生じることがあります。ガベージ コレクションの働きに関係なく、リソースを削除するとすぐにクォータが解放され、利用できる状態になります。参照整合性はシステムによって維持されます。たとえば、削除 (または置換) されたデータベースにコレクションを挿入したり、存在しないコレクション内のドキュメントを照会したりすることはできません。  
 
リソースのフィードに対して GET を発行した場合やコレクションを照会した場合、膨大な数のアイテムが結果として得られる可能性があります。それらすべてのアイテムを 1 回のラウンドトリップ (要求と応答のやり取り) で返し、利用することは、サーバーにとっても、クライアントにとっても現実的ではありません。この問題に対処するために、DocumentDB では、大きなフィードをクライアントがページ単位で読み込めるようになっています。クライアントは、[x-ms-continuationToken] という応答ヘッダーをカーソルとして使用し、後続のページにナビゲートすることができます。   

##オプティミスティック同時実行制御
ほとんどの Web アプリケーションは、エンティティ タグ ベースのオプティミスティック同時実行制御によって、"Lost Update (失われた更新)" や "Lost Delete (失われた削除)" と呼ばれる悩ましい問題に対処しています。エンティティ タグは、リソースに関連付けられる論理上のタイムスタンプで、HTTP で手軽に使うことができます。DocumentDB は、特定のリソースに関連付けられたバージョンを絶えずすべての HTTP 応答に含めることでオプティミスティック同時実行制御をネイティブにサポートします。次のケースでは、同時実行制御の競合が正しく検出されます。  

1.	2 つのクライアントが 1 つのリソースに対し、最新 ([if-match] 要求ヘッダーで指定) バージョンのリソースを使った (PUT/DELETE 動詞による) 変更要求を同時に発行した場合、DocumentDB データベース エンジンがそれらに対し、トランザクション型の同時実行制御を適用します。
2.	クライアントから提示されたリソースのバージョンが、[if-match] 要求ヘッダーに指定されたバージョンよりも古い場合、その要求は拒否されます。  

##接続オプション
DocumentDB に採用されている論理アドレス モデルでは、すべてのリソースが、その _self リンクで識別される不変の論理 URI を持ちます。分散ストレージ システムは複数のリージョンにまたがって存在するため、DocumentDB では、各データベース アカウント下に置かれたリソースをパーティション分割して多数のコンピューターに振り分け、各パーティションをレプリケートすることで、高可用性を実現しています。物理的なアドレスは、特定のパーティションのリソースを管理するレプリカによって登録されます。物理的なアドレスは、障害の発生によって、時間の経過と共に変化しますが、論理上のアドレスは一定で変化しません。論理アドレスから物理アドレスへの変換は、ルーティング テーブルで管理されます。ルーティング テーブルも内部的にはリソースとして存在します。DocumentDB では、次の 2 つの接続モードが公開されています。  

1.	**ゲートウェイ モード:** クライアントは、論理アドレスと物理アドレスの間の翻訳やルーティング処理から保護されます。クライアントは、単に論理 URI を操作して、RESTful にリソース モデルをナビゲートします。クライアントが論理 URI を使用して要求を発行すると、エッジ コンピューターがこの論理 URI をリソースを管理するレプリカの物理アドレスに翻訳して要求を転送します。エッジ コンピューターによってルーティング テーブルがキャッシュに格納 (および定期的に更新) されるため、ルーティングが非常に効率的に処理されます。 
2.	**直接接続モード:** クライアントは、プロセス空間内でルーティング テーブルを直接管理し、定期的にルーティング テーブルを更新します。クライアントは、レプリカに直接接続してエッジ コンピューターをバイパスできます。   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>接続モード</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>プロトコル</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>詳細</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>DocumentDB SDK</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    ゲートウェイ
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    アプリケーションは、論理 URI を使用してエッジ ノードに直接接続します。そのためネットワーク ホップが余分に発生します。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    REST API
                </p>
                <p>
                    .NET、JavaScript、Node.js、Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    直接接続
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS および
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    アプリケーションは、ルーティング テーブルに直接アクセスし、クライアント側でルーティングを実行してレプリカに直接接続できます。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##次のステップ
REST API を使用してリソースを操作する方法については、[Azure DocumentDB REST API リファレンス](https://msdn.microsoft.com/library/azure/dn781481.aspx)を参照してください。

##参照
-   [Azure DocumentDB REST API リファレンス](https://msdn.microsoft.com/library/azure/dn781481.aspx)  
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	JSON 仕様  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	HTTP 仕様 [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	エンティティ タグ [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[DocumentDB のクエリ](documentdb-sql-query.md)
-	[DocumentDB SQL リファレンス](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[DocumentDB のプログラミングストアド プロシージャ、トリガー、UDF](documentdb-programming.md)
-	[DocumentDB リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49-->