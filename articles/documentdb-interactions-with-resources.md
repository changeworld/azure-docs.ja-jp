<properties title="Interact with DocumentDB resources" pageTitle="Interact with DocumentDB resources | Azure" description="DocumentDB manages resources--uniquely identified by logical URIs--that developers can interact with using HTTP verbs, request/response headers, and status codes." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# DocumentDB リソースとの対話

DocumentDB は、HTTP を介したシンプルで開かれた RESTful なプログラミング モデルを可能にします。DocumentDB のプレビュー リリースには、.NET、Python、Node.js、JavaScript 向けのクライアント SDK が用意されています。いずれも基になる REST API を包み込み、それぞれの違いを吸収しながら簡単に利用できるようになっています。今後のリリースでは、C++ と Java の SDK も提供する予定です。マイクロソフトと同様、多くの企業が、その固有のプログラミング環境に対応した独自の SDK を作成し、コミュニティでシェアすることが期待されます。

> [AZURE.NOTE] RESTful なプログラミング モデルに加え、DocumentDB にはきわめて効率的な TCP プロトコルが用意されています。やはり RESTful な通信モデルを特徴としており、.NET クライアント SDK を通じて利用することができます。

## リソース

DocumentDB によって管理されるエンティティは "**リソース**" と呼ばれ、リソースはその論理 URI によって一意に識別されます。開発者がリソースを対話的に扱う際は、標準の HTTP 動詞、要求/応答ヘッダー、ステータス コードを使用します。次の図に示したように、DocumentDB の**リソース モデル**は、データベース アカウントに従属する一連のリソースから成り、個々のリソースは、不変の論理 URI アドレスで参照することができます。このドキュメントでは、そうした一連のリソースを "**フィード**" と呼ぶことにします。

![][0]

## データベース アカウント下の階層型リソース モデル

DocumentDB の利用者はまず、ご利用の Azure サブスクリプションを使用して、DocumentDB の**データベース アカウント**をプロビジョニングする必要があります。データベース アカウントは、一連の**データベース**から成ります。それぞれのデータベースには、複数の**コレクション**が含まれており、さらに、それぞれのコレクションに、**ストアド プロシージャ、トリガー、UDF、ドキュメント**および関連する**添付ファイル**が含まれています。また、データベースには**ユーザー**が関連付けられ、それぞれのユーザーには、他のさまざまなコレクション、ストアド プロシージャ、トリガー、UDF、ドキュメント、添付ファイルにアクセスするための一連の**アクセス許可**が関連付けられます。データベース、ユーザー、アクセス許可、コレクションが、既知のスキーマを持ったシステム定義のリソースであるのに対し、ドキュメントと添付ファイルは、ユーザーが自由に定義できる JSON コンテンツを格納します。

| リソース                | 説明                                                                                                                                                                                                                                                                                       |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| データベース アカウント | データベース アカウントには、容量単位が少なくとも 1 つ関連付けられます。プロビジョニングされているドキュメント ストレージとスループット (一連のデータベースと BLOB ストレージ) は、容量単位によって表されます。データベース アカウントは、Azure サブスクリプションを使用して作成できます。 |
| データベース            | データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。ユーザーのコンテナーとしても使用されます。                                                                                                                                                        |
| ユーザー                | アクセス許可の範囲や区分を決める論理上の名前空間です。                                                                                                                                                                                                                                     |
| アクセス許可            | 特定のリソースへのアクセスを許可するためにユーザーに関連付けられる承認トークンです。                                                                                                                                                                                                       |
| コレクション            | コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。                                                                                                                                                                                   |
| ストアド プロシージャ   | JavaScript で記述されたアプリケーション ロジックです。コレクションに登録され、データベース エンジン内でひとまとまりの処理として実行されます。                                                                                                                                              |
| トリガー                | 挿入、置換、削除の各操作に伴って副次的に生じる作用をモデル化し、JavaScript で記述したアプリケーション ロジックです。                                                                                                                                                                       |
| UDF                     | 副次的な作用を持たないアプリケーション ロジックです。JavaScript で記述されます。UDF を使用してクエリ演算子をカスタマイズすることにより、DocumentDB のコア クエリ言語を拡張することができます。                                                                                             |
| ドキュメント            | ユーザー定義 (ユーザーが自由に定義できる) JSON コンテンツです。既定では、スキーマを定義する必要がなく、また、コレクションに追加されたすべてのドキュメントに対してセカンダリ インデックスを用意する必要もありません。                                                                       |
| 添付ファイル            | 添付ファイルは、外部の BLOB/メディアに対する参照や関連するメタデータを含んだ特殊なドキュメントです。開発者は、BLOB を DocumentDB の管理下におくか、外部の BLOB サービス プロバイダー (OneDrive、Dropbox など) を使って保存するかを選択できます。                                           |

### システム定義リソースとユーザー定義リソース

データベース アカウント、データベース、コレクション、ユーザー、アクセス許可、ストアド プロシージャ、トリガー、UDF などのリソースには、いずれも一定のスキーマが存在し、それらを "*システム リソース*" といいます。これに対し、ドキュメントや添付ファイルなどのリソースには、スキーマに関する制約がありません。このようなリソースを "*ユーザー定義リソース*" といいます。DocumentDB では、システム定義リソースとユーザー定義リソースがどちらも、標準準拠の JSON として表現されて管理されます。システム定義であるかユーザー定義であるかに関係なく、すべてのリソースには共通するプロパティがあります。それらのプロパティを以下に示します。

> [AZURE.NOTE] リソースに対してシステムが生成するすべてのプロパティは、そのリソースの JSON 表現の中では、先頭にアンダースコア (\_) が付きます。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>プロパティ</strong></p></td>
<td align="left"><p><strong>ユーザー設定可能/システム生成</strong></p></td>
<td align="left"><p><strong>目的</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>_rid</p></td>
<td align="left"><p>システム生成</p></td>
<td align="left"><p>リソースに対してシステムが生成する一意かつ階層型の識別子</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_etag</p></td>
<td align="left"><p>システム生成</p></td>
<td align="left"><p>オプティミスティック同時実行制御に必要なリソースの ETag。</p></td>
</tr>
<tr class="even">
<td align="left"><p>_ts</p></td>
<td align="left"><p>システム生成</p></td>
<td align="left"><p>リソースの最終更新時のタイムスタンプ</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_self</p></td>
<td align="left"><p>システム生成</p></td>
<td align="left"><p>リソースに対するアドレス指定可能な一意の URI</p></td>
</tr>
<tr class="even">
<td align="left"><p>id</p></td>
<td align="left"><p>ユーザー設定可能</p></td>
<td align="left"><p>リソースに対するユーザー定義の一意の名前</p></td>
</tr>
</tbody>
</table>

### リソースの電子的表現

DocumentDB は、JSON 標準に対する独自の拡張や特殊なエンコードを必要としません。標準準拠の JSON ドキュメントで動作します。

### リソースのアドレス指定

すべてのリソースは URI でアドレス指定されます。リソースの相対 URI は、その \*\*\_self\*\* プロパティの値で表されます。URI の形式は、 /<feed>/{\_rid} パス セグメントから成ります。

|\_self の値|説明
|-------------------|-----------
|/dbs |データベース アカウント下のデータベースのフィード
|/dbs/{\_rid-db} |{\_rid-db} という値の一意の id プロパティを持つデータベース
|/dbs/{\_rid-db}/colls/ |データベース下のコレクションのフィード
|/dbs/{\_rid-db}/colls/{\_rid-coll} |{\_rid-coll} という値の一意の id プロパティを持つコレクション
|/dbs/{\_rid-db}/users/ |データベース下に存在するユーザーのフィード
|/dbs/{\_rid-db}/users/{\_rid-user} |{\_rid-user} という値の一意の id プロパティを持つユーザー
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions |データベース下に存在するアクセス許可のフィード
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions/{\_rid-permission} |{\_rid-permission} という値の一意の id プロパティを持つアクセス許可

リソースには、ユーザーによって定義される一意の名前も存在します。その名前は、リソースの id プロパティによって公開されます。特定の親リソースのコンテキストで一意となる最大 256 文字の id をユーザーが定義します。たとえば、特定のコレクションに含まれるいずれのドキュメントもその id プロパティの値が重複することはありませんが、その一意性が複数のコレクションにわたって保証されるわけではありません。同様に、アクセス許可の id プロパティの値が 1 人のユーザーの範囲内で重複することはありませんが、複数のユーザー間で重複する可能性はあります。\_rid プロパティは、アドレス指定可能なリソースの \_self link を構築する目的で使用されます。

すべてのリソースには、システムによって生成される階層型のリソース識別子 (RID) も存在し、\_rid プロパティでそれらにアクセスすることができます。RID は、特定のリソースの階層全体をコード化したものであり、分散型の手法で参照整合性を適用するための内部的な表現としてきわめて高い利便性を発揮します。RID は、データベース アカウント内で一意となっています。DocumentDB は、内部的に RID を使用することで、パーティションの境界を越えてルックアップする必要のない、効率的なルーティングを可能にしています。

リソースの \_self と \_rid の値は、いずれも代替表現を持った正規のプロパティとして存在しています。

## 基本的な対話モデル

以下に示したのは、リソースでサポートされる HTTP 動詞とその標準的な解釈です。

> [AZURE.NOTE] 将来的には、PATCH による選択的更新機能を追加する予定です。

1.  POST: 新しいアイテム リソースを作成します。
2.  GET: 既存のアイテム リソースまたはフィード リソースを読み取ります。
3.  PUT: 既存のアイテム リソースを置き換えます。
4.  DELETE: 既存のアイテム リソースを削除します。
5.  HEAD: GET から応答ペイロードを除外したものです (返されるのはヘッダーのみ)。

今後のリリースでは、アイテム リソースで PATCH 動詞もサポートする予定です。

下の図に示したように、POST の発行対象はフィード リソースに限られ、また、PUT と DELETE の発行対象はアイテム リソースに限られます。GET と HEAD は、フィード リソースとアイテム リソースのどちらに対しても発行することができます。

![][1]

**標準の HTTP 動詞を使用した対話モデル**

対話モデルに関する理解を深めるために、新しいリソースの作成 (INSERT) を例に考えてみましょう。新しいリソースを作成するためには、そのリソースの従属先 (コンテナー) フィードの URI に対し、HTTP POST 要求を発行する必要があります。要求本文に、作成するリソースの表現を含めます。この要求に必要なプロパティは、リソースの id だけです。

たとえば、新しいデータベースを作成するには、/dbs に対し、(id プロパティを一意の名前で設定することによって) データベース リソースを POST します。同様に、新しいコレクションを作成するときも、/dbs/\_rid/colls/ に対してコレクション リソースを POST します。応答には、完全にコミットされたリソースが、システムによって生成されたプロパティと共に格納されます。たとえば、返されたリソースの \_self リンクを使用すれば、他のリソースにナビゲートすることができます。ここで、単純な HTTP ベースの対話モデルの例を挙げます。クライアントは、以下のような HTTP 要求を発行することで、アカウント内に新しいデータベースを作成することができます。

    POST http://fabrikam.documents.azure.net/dbs
    {
          "id":"MyDb"
    }

これに対し、DocumentDB は、さまざまな情報を含んだ応答とデータベースの作成に成功したことを示すステータス コードを返します。

    [201 Created]
    {
          "id": "MyDb",
          "_rid": "UoEi5w==",
          "_self": "dbs/MyDb/",
          "_ts": 1407370063,
          "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
          "_colls": "colls/",
          "_users": "users/"
    }

DocumentDB サービスは、さまざまな情報を含んだ応答とデータベースの作成に成功したことを示すステータス コードを返します。

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

MyDb 下のコレクションにストアド プロシージャを登録するには、/dbs/\_rid-db/colls/\_rid-coll/sprocs に対して POST を発行します。

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
          "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
           ...
    }

上の例のストアド プロシージャを実行するには、ストアド プロシージャ リソースの URI (/dbs/\_rid-db/colls/\_rid-coll/sprocs/sproc1) に対して POST を発行する必要があります。その例を次に示します。

    POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
     [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]

この場合、DocumentDB サービスからは、次のような応答が返されます。

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

リソースの置き換えと読み取りはそれぞれ、リソースの \_self リンクに対し、PUT 動詞 (と適切な要求本文) と GET 動詞を発行することによって行います。同様に、リソースを削除するときは、その \_self リンクに対して DELETE 動詞を発行します。DocumentDB のリソース モデルでは、リソースが階層状に編成されていることから、連鎖削除が必然的にサポートされます。連鎖削除においては、上位のリソースを削除すると、それに従属するリソースが削除されます。従属リソースは、その上位のリソースとは別のノードに分散している場合があるため、削除されるまでに時間差が生じることがあります。ガベージ コレクションの働きに関係なく、リソースを削除するとすぐにクォータが解放され、利用できる状態になります。参照整合性はシステムによって維持されます。たとえば、削除 (または置換) されたデータベースにコレクションを挿入したり、存在しないコレクション内のドキュメントを照会したりすることはできません。

フィード リソースに対して GET を発行した場合やコレクションを照会した場合、膨大な数のアイテムが結果として得られる可能性があります。それらすべてのアイテムを 1 回のラウンドトリップ (要求と応答のやり取り) で返し、利用することは、サーバーにとっても、クライアントにとっても現実的ではありません。この問題に対処するために、DocumentDB では、大きなフィードをクライアントがページ単位で読み込めるようになっています。クライアントは、[x-ms-continuationToken] という応答ヘッダーをカーソルとして使用し、後続のページにナビゲートすることができます。

## オプティミスティック同時実行制御

ほとんどの Web アプリケーションは、エンティティ タグ ベースのオプティミスティック同時実行制御によって、"Lost Update (失われた更新)" や "Lost Delete (失われた削除)" と呼ばれる悩ましい問題に対処しています。エンティティ タグは、リソースに関連付けられる論理上のタイムスタンプで、HTTP で手軽に使うことができます。DocumentDB は、特定のリソースに関連付けられたバージョンを絶えずすべての HTTP 応答に含めることでオプティミスティック同時実行制御をネイティブにサポートします。次のケースでは、同時実行制御の競合が正しく検出されます。

1.  2 つのクライアントが 1 つのリソースに対し、最新 ([if-match] 要求ヘッダーで指定) バージョンのリソースを使った (PUT/ DELETE 動詞による) 変更要求を同時に発行した場合、DocumentDB データベース エンジンがそれらに対し、トランザクション型の同時実行制御を適用します。
2.  クライアントから提示されたリソースのバージョンが、[if-match] 要求ヘッダーに指定されたバージョンよりも古い場合、その要求は拒否されます。

## 接続オプション

DocumentDB に採用されている論理アドレス モデルでは、すべてのリソースが、その \_self リンクで識別される不変の論理 URI を持ちます。分散ストレージ システムは複数のリージョンにまたがって存在するため、DocumentDB では、各データベース アカウント下に置かれたリソースをパーティション分割して多数のコンピューターに振り分け、各パーティションをレプリケートすることで、高可用性を実現しています。物理的なアドレスは、特定のパーティションのリソースを管理するレプリカによって登録されます。物理的なアドレスは、障害の発生によって、時間の経過と共に変化しますが、論理上のアドレスは一定で変化しません。論理アドレスから物理アドレスへの変換は、ルーティング テーブルで管理されます。ルーティング テーブルも内部的にはリソースとして存在します。DocumentDB では、次の 2 つの接続モードが公開されています。

1.  **ゲートウェイ モード:** 論理アドレスから物理アドレスへの変換やルーティングの細部は、クライアントからは見えません。クライアントが扱うのは論理 URI だけであり、リソース モデルのナビゲーションは RESTful に行われます。クライアントは、論理 URI を使用して要求を発行すると、エッジ コンピューターが論理 URI をレプリカの物理アドレスに変換します。リソースの管理と要求の転送はレプリカによって行われます。ルーティング テーブルはエッジ コンピューターによってキャッシュされ (定期的に更新され) るため、ルーティングはきわめて効率的です。
2.  **直接接続モード:** クライアントが直接そのプロセス空間でルーティング テーブルを管理し、定期的に更新します。クライアントは直接レプリカに接続し、エッジ コンピューターをバイパスすることができます。

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>接続モード</strong></p></td>
<td align="left"><p><strong>プロトコル</strong></p></td>
<td align="left"><p><strong>詳細</strong></p></td>
<td align="left"><p><strong>DocumentDB SDK</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>ゲートウェイ</p></td>
<td align="left"><p>HTTPS</p></td>
<td align="left"><p>アプリケーションは、論理 URI を使用してエッジ ノードに直接接続します。そのためネットワーク ホップが余分に発生します。</p></td>
<td align="left"><p>REST API</p>
<p>.NET、JavaScript、Node.js、Python</p></td>
</tr>
<tr class="odd">
<td align="left"><p>直接接続</p></td>
<td align="left"><p>HTTPS および</p>
<p>TCP</p></td>
<td align="left"><p>アプリケーションは、ルーティング テーブルに直接アクセスし、クライアント側でルーティングを実行してレプリカに直接接続することができます。</p></td>
<td align="left"><p>.NET</p></td>
</tr>
</tbody>
</table>

## 参照

-   REST ([][]<http://en.wikipedia.org/wiki/Representational_state_transfer></a>)
-   JSON 仕様 ([][2]<http://-www.ietf.org/rfc/rfc4627.txt></a>)
-   HTTP 仕様 ([][3]<http://www.w3.org/Protocols/rfc2616/rfc2616.html></a>)
-   エンティティ タグ ([][4]<http://en.wikipedia.org/wiki/HTTP_ETag></a>)
-   [DocumentDB のクエリ][DocumentDB のクエリ]
-   [DocumentDB SQL リファレンス][DocumentDB SQL リファレンス]
-   [DocumentDB のプログラミング: ストアド プロシージャ、トリガー、UDF][DocumentDB のプログラミング: ストアド プロシージャ、トリガー、UDF]
-   [DocumentDB Reference Documentation (DocumentDB リファレンス ドキュメント)][DocumentDB Reference Documentation (DocumentDB リファレンス ドキュメント)]

  [0]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
  [1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
  [0]: http://en.wikipedia.org/wiki/Representational_state_transfer
  [2]: http://-www.ietf.org/rfc/rfc4627.txt
  [3]: http://www.w3.org/Protocols/rfc2616/rfc2616.html
  [4]: http://en.wikipedia.org/wiki/HTTP_ETag
  [DocumentDB のクエリ]: /documentation/articles/documentdb-sql-query/
  [DocumentDB SQL リファレンス]: http://go.microsoft.com/fwlink/p/?LinkID=510612
  [DocumentDB のプログラミング: ストアド プロシージャ、トリガー、UDF]: /documentation/articles/documentdb-programming/
  [DocumentDB Reference Documentation (DocumentDB リファレンス ドキュメント)]: http://go.microsoft.com/fwlink/p/?LinkID=402384
