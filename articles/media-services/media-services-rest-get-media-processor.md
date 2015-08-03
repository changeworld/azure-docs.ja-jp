<properties 
	pageTitle="メディア プロセッサを作成する方法 - Azure" 
	description="メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="juliako"/>


#方法: メディア プロセッサ インスタンスを取得する

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」シリーズの一部です。


##概要

メディア プロセッサは、メディア サービスのコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>メディア プロセッサ名</th>
       <th>説明</th>
	<th>詳細</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure メディア エンコーダー</td>
       <td>Azure メディア エンコーダーを使用してエンコード タスクを実行できます。</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure メディア エンコーダー用のタスク プリセット文字列</a></td>
    </tr>
    <tr>
       <td>メディア エンコーダー プレミアム ワークフロー</td>
       <td>メディア エンコーダー プレミアム ワークフローを使用してエンコード タスクを実行できます。</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">メディア エンコーダー プレミアム ワークフローを使用したエンコード</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Azure Media Indexer によるメディア ファイルのインデックス作成</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>メディア アセットを .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア アセットを変換できます。</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager 用のタスクのプリセット</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>PlayReady Protection を使用してメディア アセットを暗号化できます。</td>
        <td><a href=" http://go.microsoft.com/fwlink/?LinkId=613274">Azure Media Packager 用のタスク プリセット文字列</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (プレビュー)</td>
		<td>ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。コンテンツをすばやく使用可能なクリップにすることもできます。</td>
		<td><a href="http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Storage Decryption</td>
        <td>ストレージ暗号化を使用して暗号化されたメディア アセットを復号化できます。</td>
		<td>該当なし</td>
    </tr>  </tbody>
</table>

<br />

##MediaProcessor の取得

>[AZURE.NOTE]Media Services REST API を使用する場合は、次のことに考慮します。
>
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。詳細については、「[Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

>https://media.windows.net に正常に接続すると、別の Media Services URI が指定された 301 リダイレクトが表示されます。「[Media Services REST API を使用して Media Services アカウントに接続する](media-services-rest-connect_programmatically.md)」で説明するとおり、続けて新しい URI を呼び出す必要があります。



次の REST コールでは、名前 (ここでは **Azure メディア エンコーダー**) でメディア プロセッサ インスタンスを取得する方法を示しています。

	
要求:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
応答:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


##次のステップ
これで、プロセッサ インスタンスの取得方法を学習できました。次は、[アセットをエンコードする方法][]に関するトピックに進み、Azure メディア エンコーダーを使用してアセットをエンコードする方法を学習します。

[アセットをエンコードする方法]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=July15_HO4-->