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
	ms.date="02/02/2015" 
	ms.author="juliako"/>


# Media Services REST API の概要 

Microsoft Azure Media Services は OData ベースの HTTP 要求を受け付けるサービスであり、詳細 JSON または atom+pub で応答が可能です。Media Services は、Azure 設計ガイドラインに準拠しているため、Media Services に接続するときに各クライアントが使用する必要がある必須 HTTP ヘッダーのセットと、使用できる省略可能なヘッダーのセットがあります。次のセクションでは、要求を作成したり Media Services から応答を受信したりするときに使用できるヘッダーと HTTP 動詞について説明します。


## Media Services でサポートされている標準の HTTP 要求ヘッダー

Media Services に対して行うそれぞれの呼び出しについて、要求に含める必要がある必須のヘッダーのセットと、含める可能性がある省略可能なヘッダーのセットがあります。次の表に、それらの必須のヘッダーを示します。


<table border="1">
<tr><th>ヘッダー</th><th>種類</th><th>値</th></tr>
<tr><td>Authorization</td><td>ベアラ</td><td>ベアラは、唯一許容される承認のメカニズムです。値には、ACS によって提供されるアクセス トークンを含める必要もあります。</td></tr>
<tr><td>x-ms-version</td><td>小数点</td><td>2.9</td></tr>
<tr><td>DataServiceVersion</td><td>小数点</td><td>3.0</td></tr>
<tr><td>MaxDataServiceVersion</td><td>小数点</td><td>3.0</td></tr>
</table><br/>


>[AZURE.NOTE] Media Services は、OData を使用して REST　APIを介して管理しているアセットメタデータ レポジトリを公開するため、DataServiceVersion および MaxDataServiceVersion ヘッダーをどの要求にも含める必要があります。含めない場合、Media Services は使用中の DataServiceVersion の値を3.0 と見なします。

省略可能なヘッダーのセットを次に示します。

<table border="1">
<tr><th>ヘッダー</th><th>種類</th><th>値</th></tr>
<tr><td>Date</td><td>RFC 1123 の日付</td><td>要求のタイムスタンプ</td></tr>
<tr><td>Accept</td><td>コンテンツの種類</td><td>次のような応答に対する要求のコンテンツの種類:
<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br> 応答には、blob フェッチのように、さまざまなコンテンツの種類があります。正常な応答にはペイロードなどの blob ストリームが含まれます。</td></tr>
<tr><td>Accept-Encoding</td><td>Gzip、デフレート</td><td>GZIP および DEFLATE エンコーディング　(該当する場合)。注:リソースが大きい場合、Media Services はこのヘッダーを無視し、圧縮されていないデータを返す可能性があります。
</td></tr>
<tr><td>Accept-Language</td><td>"en"、"es" など。</td><td>応答の優先言語を指定します。</td></tr>
<tr><td>Accept-Charset</td><td>"UTF-8"　などの文字の種類</td><td>既定値は UTF-8 です。</td></tr>
<tr><td>X-HTTP-Method</td><td>HTTP メソッド</td><td>PUT や DELETE などの HTTP メソッドをサポートしないクライアントやファイアウォールが、GET 呼び出しを通じてトンネリングされたこれらのメソッドを使用できます。</td></tr>
<tr><td>Content-Type</td><td>コンテンツの種類</td><td>PUT または POST 要求の本文のコンテンツの種類。</td></tr>
<tr><td>client-request-id</td><td>文字列</td><td>指定した要求を識別する、呼び出し元で定義された値。指定されている場合、この値は、要求をマッピングする方法として、応答メッセージに含まれます。 <br/><br/>
<b>重要</b>:<br/>
値は、2096b (2k)　に制限されます。</td></tr>
</table><br/>


## Media Services でサポートされている標準の HTTP 応答ヘッダー

要求したリソースと実行を意図した操作によって返されるヘッダーのセットを次に示します。


<table border="1">
<tr><th>ヘッダー</th><th>種類</th><th>値</th></tr>
<tr><td>request-id</td><td>文字列</td><td>現在の操作、生成されたサービスのための一意の識別子。</td></tr>
<tr><td>client-request-id</td><td>文字列</td><td>元の要求の呼び出し元によって指定された識別子 (存在する場合)。</td></tr>
<tr><td>Date</td><td>RFC 1123 の日付</td><td>要求が処理された日付。</td></tr>
<tr><td>Content-Type</td><td>多様</td><td>応答本文のコンテンツの種類。</td></tr>
<tr><td>Content-Encoding</td><td>多様</td><td>Gzip またはデフレート (必要に応じて)。</td></tr>
</table><br/>

## Media Services でサポートされている標準の HTTP 動詞

HTTP 要求を行うときに使用できる HTTP 動詞の完全な一覧を次に示します。


<table border="1">
<tr><th>動詞</th><th>説明</th></tr>
<tr><td>GET</td><td>オブジェクトの現在の値を返します。</td></tr>
<tr><td>POST</td><td>提供されるデータに基づくオブジェクトを作成、またはコマンドを送信します。</td></tr>
<tr><td>PUT</td><td>オブジェクトの置き換え、または名前付きのオブジェクトの作成を行います (使用している場合)。</td></tr>
<tr><td>削除</td><td>オブジェクトを削除します。</td></tr>
<tr><td>MERGE</td><td>名前付きプロパティを変更して、既存のオブジェクトを更新します。</td></tr>
<tr><td>HEAD</td><td>GET 応答に対するオブジェクトのメタデータを返します。</td></tr>
</table><br/>

## Media Services のモデルの検出

Media Services のエンティティを見つけやすくするには、$metadata 操作を使用できます。これによって、すべての有効なエンティティの種類、エンティティのプロパティ、関連付け、関数、アクションなどを取得できます。次の例は、URI を作成する方法を示しています。: https://media.windows.net/API/$metadata.

ブラウザーにメタデータを表示する場合は　URI の末尾に "?api-version=2.x" を追加してください。または、要求に　x-ms-version　を含めないでください。


<!-- Anchors. -->


<!-- URLs. -->
  
  [管理ポータル]: http://manage.windowsazure.com/




<!--HONumber=52-->