<properties 
	pageTitle="Media Services REST API の概要 - Azure" 
	description="Media Services REST API の概要" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="juliako"/>


# Media Services REST API の概要 

Microsoft Azure Media Services は OData ベースの HTTP 要求を受け付けるサービスであり、詳細 JSON または atom+pub で応答が可能です。Media Services は、Azure 設計ガイドラインに準拠しているため、Media Services に接続するときに各クライアントが使用する必要がある必須 HTTP ヘッダーのセットと、使用できる省略可能なヘッダーのセットがあります。次のセクションでは、要求を作成したり Media Services から応答を受信したりするときに使用できるヘッダーと HTTP 動詞について説明します。


## Media Services でサポートされている標準の HTTP 要求ヘッダー

Media Services に対して行うそれぞれの呼び出しについて、要求に含める必要がある必須のヘッダーのセットと、含める可能性がある省略可能なヘッダーのセットがあります。次の表に、それらの必須のヘッダーを示します。


<table border="1"> <tr><th>ヘッダー</th><th>型</th><th>値</th></tr> <tr><td>Authorization</td><td>Bearer</td><td>Bearer は、許容される唯一の承認メカニズムです。値には ACS によって提供されるアクセス トークンも含める必要があります。</td></tr> <tr><td>x-ms-version</td><td>小数</td><td>2.11</td></tr> <tr><td>DataServiceVersion</td><td>小数</td><td>3.0</td></tr> <tr><td>MaxDataServiceVersion</td><td>小数</td><td>3.0</td></tr> </table><br/>


>[AZURE.NOTE]Media Services は、OData を使用して REST　APIを介して管理しているアセットメタデータ レポジトリを公開するため、DataServiceVersion および MaxDataServiceVersion ヘッダーをどの要求にも含める必要があります。含めない場合、Media Services は使用中の DataServiceVersion の値を3.0 と見なします。

省略可能なヘッダーのセットを次に示します。

<table border="1"> <tr><th>ヘッダー</th><th>型</th><th>値</th></tr> <tr><td>Date</td><td>RFC 1123 の日付</td><td>要求のタイムスタンプ</td></tr> <tr><td>Accept</td><td>コンテンツの種類</td><td>次のような応答に対して要求されるコンテンツの種類があります。<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br> 応答には、blob フェッチのように、さまざまなコンテンツの種類があります。正常な応答にはペイロードなどの blob ストリームが含まれます。</td></tr> <tr><td>Accept-Encoding</td><td>Gzip、deflate</td><td>GZIP および DEFLATE エンコーディング (適用できる場合)。注: 大きなリソースでは、Media Services はこのヘッダーを無視し、圧縮されていないデータを返す場合があります。</td></tr> <tr><td>Accept-Language</td><td>"en"、"es" など。</td><td>応答の優先言語を指定します。</td></tr> <tr><td>Accept-Charset</td><td>“UTF-8” などの文字セット</td><td>既定値は UTF-8 です。</td></tr> <tr><td>X-HTTP-Method</td><td>HTTP メソッド</td><td>PUT や DELETE などの HTTP メソッドをサポートしないクライアントまたはファイアウォールが、GET 呼び出しを経由してこれらのメソッドを使用できるようにします。</td></tr> <tr><td>Content-Type</td><td>Content type</td><td>PUT または POST 要求の要求本文のコンテンツの種類。</td></tr> <tr><td>client-request-id</td><td>文字列</td><td>特定の要求を識別する、呼び出し元で定義された値。指定されている場合、この値は、要求をマッピングする方法として、応答メッセージに含まれます。<br/><br/> <b>重要</b><br/> 値は 2096b (2k) に制限する必要があります。</td></tr> </table><br/>


## Media Services でサポートされている標準の HTTP 応答ヘッダー

要求したリソースと実行を意図した操作によって返されるヘッダーのセットを次に示します。


<table border="1"> <tr><th>ヘッダー</th><th>型</th><th>値</th></tr> <tr><td>request-id</td><td>文字列</td><td>サービスで生成された、現在の操作の一意の識別子。</td></tr> <tr><td>client-request-id</td><td>文字列</td><td>呼び出し元によって指定された元の要求の識別子 (存在する場合)。</td></tr> <tr><td>Date</td><td>RFC 1123 の日付</td><td>要求が処理された日付。</td></tr> <tr><td>Content-Type</td><td>可変</td><td>応答本文のコンテンツの種類。</td></tr> <tr><td>Content-Encoding</td><td>可変</td><td>必要に応じて Gzip または deflate。</td></tr> </table><br/>

## Media Services でサポートされている標準の HTTP 動詞

HTTP 要求を行うときに使用できる HTTP 動詞の完全な一覧を次に示します。


<table border="1"> <tr><th>動詞</th><th>説明</th></tr> <tr><td>GET</td><td>オブジェクトの現在の値を返します。</td></tr> <tr><td>POST</td><td>提供されるデータに基づいてオブジェクトを作成するか、コマンドを送信します。</td></tr> <tr><td>PUT</td><td>オブジェクトを置き換えるか、名前付きのオブジェクトを作成します (適用できる場合)。</td></tr> <tr><td>DELETE</td><td>オブジェクトを削除します。</td></tr> <tr><td>MERGE</td><td>名前付きプロパティを変更して、既存のオブジェクトを更新します。</td></tr> <tr><td>HEAD</td><td>GET 応答に対するオブジェクトのメタデータを返します。</td></tr> </table><br/>

## Media Services のモデルの検出

Media Services のエンティティを見つけやすくするには、$metadata 操作を使用できます。これによって、すべての有効なエンティティの種類、エンティティのプロパティ、関連付け、関数、アクションなどを取得できます。次の例は、URI を作成する方法を示しています。https://media.windows.net/API/$metadata

ブラウザーにメタデータを表示する場合は　URI の末尾に "?api-version=2.x" を追加してください。または、要求に　x-ms-version　を含めないでください。


<!-- Anchors. -->


<!-- URLs. -->
  [Management Portal]: http://manage.windowsazure.com/



 

<!---HONumber=July15_HO4-->