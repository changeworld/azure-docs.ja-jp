<properties
	pageTitle="CORS を利用した Azure CDN の使用"
	description="クロス オリジン リソース共有 (CORS) を利用して Azure Content Delivery Network (CDN) を使用する方法について説明します。"
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="casoper"/>
    
# CORS を利用した Azure CDN の使用     

## CORS とは

CORS (クロス オリジン リソース共有) は、あるドメインで実行されている Web アプリケーションが別のドメイン内にあるリソースにアクセスできるようにする HTTP 機能です。クロスサイト スクリプティング攻撃の可能性を低減させるために、すべての最新の Web ブラウザーには[同一オリジン ポリシー](http://www.w3.org/Security/wiki/Same_Origin_Policy)と呼ばれるセキュリティ制限が実装されています。これにより、Web ページは他のドメイン内の API を呼び出すことができません。CORS を使用すれば、あるドメイン (オリジン ドメイン) から他のドメイン内の API を安全に呼び出すことができます。
 
## 動作のしくみ
1.	ブラウザーが **Origin** HTTP ヘッダーを含んだ OPTIONS 要求を送信します。このヘッダーの値は、親ページを提供したドメインです。https://www.contoso.com のページが fabrikam.com ドメイン内のユーザーのデータにアクセスしようとすると、fabrikam.com に次の要求ヘッダーが送信されます。 
    
    `Origin: https://www.contoso.com`
 
2.	サーバーからは次のような応答が返される場合があります。
    - 許可されるオリジン サイトを示す、応答の **Access-Control-Allow-Origin** ヘッダー。次に例を示します。
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - エラー ページ (サーバーによってクロス オリジン要求が許可されなかった場合)
    - すべてのドメインを許可するワイルドカードが含まれた **Access-Control-Allow-Origin** ヘッダー。
        
        `Access-Control-Allow-Origin: *`
 
HTTP 要求が複雑な場合、完全な要求を送信する前に、アクセス許可が付与されているかどうかを判断するために、まず "プレフライト" 要求が行われます。
 
## ワイルドカードまたは単一のオリジンのシナリオ

Azure CDN の CORS は、**Access-Control-Allow-Origin** ヘッダーがワイルドカード (*) または単一のオリジンに設定されている場合、そのままの構成で自動的に動作します。最初の応答が CDN によってキャッシュされ、後続の要求で同じヘッダーが使用されます。
 
CORS でオリジンが設定される前に CDN に対し要求が行われている場合、エンドポイント コンテンツのコンテンツを消去して、**Access-Control-Allow-Origin** ヘッダーと共にそのコンテンツを再度読み込む必要があります。
 
## 複数のオリジンのシナリオ

複数のオリジンを CORS で許可する必要がある場合は、若干複雑になります。最初の CORS オリジンの **Access-Control-Allow-Origin** ヘッダーが CDN にキャッシュされるときに問題が発生します。異なる CORS オリジンが後続の要求を行った場合、CDN によってキャッシュされた **Access-Control-Allow-Origin** ヘッダーが提供されても、オリジンが一致しません。これを修正するにはいくつかの方法があります。
 
### Azure CDN Premium from Verizon

その方法として最もお勧めできるのが、高度な機能が複数公開されている **Azure CDN Premium from Verizon** を使用する方法です。
 
要求の **Origin** ヘッダーを確認するための[ルールを作成](cdn-rules-engine.md)する必要があります。オリジンが有効である場合、ルールによって、要求で指定されたオリジンが **Access-Control-Allow-Origin** ヘッダーに設定されます。**Origin** ヘッダーで指定されたオリジンが許可されない場合、ルールによって **Access-Control-Allow-Origin** ヘッダーが省略されます。その結果、ブラウザーは要求を拒否します。
 
ルール エンジンを使用してこれを行う方法は 2 つあります。どちらの場合でも、ファイルの配信元サーバーからの **Access-Control-Allow-Origin** ヘッダーは完全に無視されます。CDN のルール エンジンが、許可される CORS オリジンを完全に管理します。

#### 有効なオリジンがすべて含まれる 1 つの正規表現
 
この場合、許可するオリジンがすべて含まれた正規表現を作成します。

	https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN from Verizon** では、正規表現を作成するエンジンとして [Perl Compatible Regular Expressions](http://pcre.org/) を使用します。正規表現を検証するには、[Regular Expressions 101](https://regex101.com/) などのツールを使用できます。スラッシュ (/) は正規表現で有効であり、エスケープする必要はありません。ただし、この文字のエスケープはベスト プラクティスだと考えられており、一部の正規表現検証ツールではエスケープするよう想定されていることに注意してください。

正規表現に一致すると、CDN エッジのルールが、(もしあれば) オリジンからの **Access-Control-Allow-Origin** ヘッダーを要求の送信元のオリジンに置き換えます。**Access-Control-Allow-Methods** などのその他の CORS ヘッダーを追加することもできます。

![Rules example with regular expression](./media/cdn-cors/cdn-cors-regex.png)
 
#### オリジンごとの要求ヘッダー ルール

正規表現を使用するのではなく、許可するオリジンごとに個別のルールを作成することもできます。この場合、**Request Header Wildcard (要求ヘッダーのワイルドカード)** [一致条件](cdn-rules-engine-details.md#match-conditions)を使用します。正規表現の方法と同様に、ルール エンジンは単独で CORS ヘッダーを設定します。
  
![Rules example without regular expression](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] 上記の例では、ワイルドカード文字 * を使うことで、HTTP と HTTPS の両方を照合するようにルール エンジンに指示しています。
 
### Azure CDN Standard

Azure CDN Standard プロファイルでは、ワイルドカード オリジンを使用せずに複数のオリジンを許可するメカニズムは、[クエリ文字列のキャッシュ](cdn-query-string.md)を使用する方法だけです。CDN エンドポイントのクエリ文字列設定を有効にしたうえで、許可される各ドメインからの要求について一意のクエリ文字列を使用する必要があります。これを行うと、CDN で一意のクエリ文字列ごとに個別のオブジェクトがキャッシュされるようになります。この手法は最適ではありませんが、CDN でキャッシュされた同じファイルのコピーが複数得られるようになります。

<!---HONumber=AcomDC_0525_2016-->