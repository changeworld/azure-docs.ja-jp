<properties 
	pageTitle="Media Services REST API を使用したビデオ オン デマンド配信" 
	description="このチュートリアルでは REST API を使用した Azure Media Services でのビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順を説明します。" 
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
	ms.date="04/14/2015" 
	ms.author="juliako"/>

# クイック スタート:Media Services REST API を使用したビデオ オン デマンド配信 

[AZURE.INCLUDE [media-services-selector-get-started](../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料評価版サイト</a>をご覧ください

このクイックスタートでは Azure Media Services (AMS) の REST API を使用するビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装について手順を追って説明します。 

チュートリアルでは Media Services の基本的なワークフローや、Media Services 開発に必要となる一般的なプログラミング オブジェクトやタスクについて紹介します。このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、ダウンロードして、ストリーミングやプログレッシブ ダウンロードを実行できます。  

## 前提条件
REST API を使用して Media Services での開発を始めるには、次の前提条件が必要です。

- Media Services REST API を使用して開発する方法を理解します。詳細については、「[media-services-rest-overview](http://msdn.microsoft.com/library/azure/hh973616.aspx)」をご覧ください。
- HTTP 要求と応答を送信できる任意のアプリケーション。このチュートリアルでは、[Fiddler](http://www.telerik.com/download/fiddler) を使用します。 

このクイック スタートでは、次のタスクが表示されます。

1.  ポータルを使用した Media Services アカウントの作成
1.  REST API で Media Services アカウントに接続する
1.  新しいアセットを作成し、REST API を使用してビデオのファイルをアップロードする
1.  REST API でストリーミング ユニットを構成する
2.  REST API で一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする
1.  REST API でエンコードされたアセットの配信ポリシーの構成
1.  アセットを発行して REST API によるストリーミングとプログレッシブ ダウンロード用 URL を取得する 
1.  コンテンツの再生 


## <a id="create_ams"></a>ポータルを使用した Media Services アカウントの作成

1. [管理ポータル][]で、**[新規]**、**[Media Services]**、**[簡易作成]** の順にクリックします。
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. **[名前]** ボックスに新しいアカウントの名前を入力します。Media Services アカウント名は、使用できる文字が小文字または数字だけで、空白を含めることはできず、長さは 3 ～ 24 文字です。 

3. **[リージョン]** で、Media Services アカウントのメタデータ レコードを保存するリージョンを選択します。ドロップダウン リストには利用可能な Media Services リージョンだけが表示されます。 

4. **[ストレージ アカウント]** で、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。新しいストレージ アカウントは同じリージョンに作成されます。 

5. 新しいストレージ アカウントを作成した場合は、**[新しいストレージ アカウント名]** ボックスにストレージ アカウントの名前を入力します。ストレージ アカウントの命名規則は、Media Services アカウントと同じです。

6. フォームの下部にある **[簡易作成]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	アカウントの作成に成功すると、ステータスが [アクティブ] に変化します。 
	
	ページ下部に **[キーの管理]** ボタンが表示されます。このボタンをクリックすると、Media Services アカウント名、プライマリ キー、セカンダリ キーがダイアログに表示されます。Media Services アカウントにプログラムからアクセスするには、アカウント名とプライマリ キーの情報が必要です。 

	
	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	アカウント名をダブルクリックすると、既定で [クイック スタート] ページが表示されます。このページでは、ポータルの別のページでも実行できる管理タスクをいくつか実行できます。たとえば、ビデオ ファイルのアップロードは、このページから実行したり、[コンテンツ] ページから実行したりできます。


## <a id="connect"></a>REST API で Media Services アカウントに接続する

Azure Media Services にアクセスする際には、Azure アクセス制御サービス (ACS) から提供されるアクセス トークン、および Media Services 自体の URI の 2 つが必要です。Media Services にコールする際、正しいヘッダー値を指定して適切にアクセス トークンに渡す限り、どのような方法でもこれらのリクエストを作成できます。

次の手順では、Media Services REST API を使用して Media Services に接続するときの最も一般的なワークフローについて説明します。

1. アクセス トークンを取得します。 
2. Media Services URI に接続します。 
	
	>[AZURE.NOTE]
	Https://media.windows.net に正常に接続されると、別の Media Services の URI を指定する 301 リダイレクトを受け取ります。その新しい URI に再度コールする必要があります。
	> 
	> ODATA API メタデータの説明が含まれる HTTP/1.1 200 応答が表示される場合もあります。
3. 新しい URL に後続の API 呼び出しを投稿します。 
	
	たとえば、接続しようとした後に次のようなメッセージが表示されます。
		
		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	この場合、続けて、このリンクへ API コールを行う必要があります: https://wamsbayclus001rest-hs.cloudapp.net/api/.

### アクセス トークンの取得

REST API から直接 Media Services にアクセスするには、ACS からアクセス トークンを取得して、Media Services に行う各 HTTP 要求で使用します。このトークンは、OAuth v2 プロトコルを使用して、HTTP 要求のヘッダーで提供されるアクセス要求に基づいて ACS で提供されるその他のトークンに似ています。Media Services に直接接続するための前提条件は、これ以外にはありません。

次の例は、トークンを取得するために使用される HTTP 要求のヘッダーと本文を示します。

**ヘッダー**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**本文**: 

この要求の本文で、client_id と client_secret の値を指定する必要があります。client_id と client_secret はそれぞれ、AccountName と AccountKey の値に対応します。Media Services では、アカウントを設定したときにこれらの値が提供されます。 

Media Services アカウントの AccountKey は、アクセス トークン要求で client_secret 値として使用されるときは URL エンコードされている必要があります。

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


次に例を示します。 

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


次の例は、本文にアクセス トークンを含む HTTP 応答を示します。

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]
"access_token " と "expires_in " の値は外部ストレージに保存することをお勧めします。後でそのストレージからトークンのデータを取り出し、Media Services REST API コールで再利用できます。これはトークンが複数のプロセスやコンピューターで安全に共有される場合に、特に便利です。

アクセス トークンの "expires_in" 値を確認し、必要に応じて新しいトークンで REST API コールを更新してください。

### Media Services URI への接続

Media Services のルート URI は https://media.windows.net/ です。まず、この URI に接続して、301 リダイレクトが返された場合は続けて新しい URI にコールする必要があります。加えて、要求に auto-redirect/follow ロジックを使うことはできません。HTTP 動詞や応答本文はその新しい URI に転送されません。

アセット ファイルをアップロードしたりダウンロードしたりするルート URI は https://yourstorageaccount.blob.core.windows.net/ です。ストレージ アカウント名には、Media Services アカウントで設定したものと同じものが使われます。

次の例は、Media Services ルート URI (https://media.windows.net/) への HTTP 要求を示します。要求に対して 301 リダイレクトが返されています。その後、新しい URI (https://wamsbayclus001rest-hs.cloudapp.net/api/) に続けて要求を行っています。     

**HTTP 要求**: 
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: media.windows.net


**HTTP 応答**: 
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**HTTP 要求** (新しい URI を使用): 
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 応答**: 
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE] このチュートリアルではこれ以降、新しい URI を使用します。

## <a id="upload"></a>新しいアセットを作成し、REST API を使用してビデオのファイルをアップロードする

Media Services で、デジタル ファイルをアセットにアップロードします。**Asset** エンティティには、ビデオ、オーディオ、画像、縮小表示のコレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルをアセットにアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。 

アセット作成時に指定する必要がある値の 1 つに、アセット作成オプションがあります。**オプション**プロパティは、アセットの作成に使用できる暗号化オプションを示す列挙値です。有効な値は以下の一覧の値のいずれかですが、この一覧の値を組み合わせて使用することはできません。

 
- **None** = **0** - 暗号化は使用されません。このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。

	プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。 
- **StorageEncrypted** = **1** - ローカルで AES-256 ビット暗号化を使用し、平文のコンテンツを暗号化したうえで、それを Azure Storage にアップロードします。アップロードされたデータは、暗号化された状態で保存されます。StorageEncrypted で保護されたアセットは、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力アセットとして再びアップロードできます。StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
- **CommonEncryption** = **2** - 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
- **EnvelopeEncrypted** = **4** - AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

### アセットを作成する

アセットは、ビデオ、オーディオ、イメージ、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイルなど、Media Services 内の多様な種類やセットのオブジェクトのためのコンテナーです。REST API でアセットを作成するには、Media Services に POST 要求を送信し、要求本文に、アセットに関するプロパティ情報を配置する必要があります。

次の例では、アセットを作成する方法を示します。

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 45
	
	{"Name":"BigBuckBunny.mp4", "Options":"0"}
	

**HTTP 応答**

成功した場合は、次の内容が返されます。
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny2.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
### AssetFile を作成する

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) エンティティは、BLOB コンテナーに格納されているビデオまたはオーディオ ファイルを表します。アセット ファイルは常にアセットに関連付けられており、アセットには 1 つまたは複数の Assetfile を含む場合があります。アセット　ファイル オブジェクトが blob コンテナー内のデジタル ファイルに関連付けられていないと、Media Services のエンコーダー タスクは失敗します。

デジタル メディア ファイルを blob コンテナーにアップロードした後、**MERGE** HTTP 要求を使用して、メディア ファイル (トピックの後半に表示) に関する情報とともに AssetFile をアップデートします。 

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 応答**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


### 書き込みのアクセス許可を持つ AccessPolicy を作成する 

すべてのファイルを blob ストレージにアップロードする前に、アセットに書き込むためのアクセス ポリシーの権限を設定します。そのためには、AccessPolicies エンティティ セットへの HTTP 要求を投稿します。作成時に DurationInMinutes 値を定義します。定義していないと、500 Internal Server エラー メッセージが返されます。AccessPolicies　の詳細については、[AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx)をご覧ください。

次の例は、AccessPolicy を作成する方法を示します。
		
**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 74
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP 要求**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

### アップロード URL を取得する

実際のアップロード URL を受信するには、SAS Locator　を作成します。Locator は、アセット内のファイルにアクセスするクライアントの開始時刻と接続エンドポイントの種類を定義します。特定の AccessPolicy と Asset ペアに対して複数の　Locator　エンティティを作成して、別のクライアントの要求およびニーズを処理できます。これらの各 Locator は、AccessPolicy の StartTime 値と DurationInMinutes 値を使用して、URL を使用できる時間の長さを決定します。詳細については、「[Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx)」をご覧ください。


SAS URL には次の形式があります。

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

いくつかの考慮事項が適用されます。

- 特定のアセットに関連付けられている 5 つの一意のLocator を同時に使用することはできません。詳細については、「Locator」をご覧ください。
- すぐにファイルをアップロードする必要がある場合は、StartTime 値を現在の時刻の 5 分前に設定する必要があります。これは、クライアント コンピューターと Media Services の間にクロック スキューがある可能性があるためです。また、StartTime 値は DateTime 形式のYYYY-MM-DDTHH:mm:ssZ である必要があります (たとえば、"2014-05-23T17:53:50Z")。	
- Locator を作成した後に使用可能になるまで 30 ～ 40 秒の遅延が発生する場合があります。この問題は、SAS URL と Origin Locator の両方に当てはまります。

次の例は、要求本文の Type プロパティで定義されているように、SAS URL Locator を作成する方法を示しています　(SAS ロケータ―の場合は "１"、オンデマンド配信元ロケーターの場合は "２")。返される **Path** プロパティには、ファイルのアップロードに使用する必要がある URL が含まれています。
	
**HTTP 要求**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 178
	
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**HTTP 応答**

成功した場合、次の応答が返されます。
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

### Blob ストレージ コンテナーにファイルをアップロードする
	
AccessPolicy と Locator を設定すると、実際のファイルは、Azure Storage REST API を使用して Azure Blob ストレージ コンテナーにアップロードされます。ページにアップロードするか、blob をブロックできます。 

>[AZURE.NOTE] 前のセクションで受信した Locator の **Path** 値にアップロードするファイルのファイル名を追加する必要があります。たとえば、https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . のように指定します。 

Azure ストレージ BLOB の使用の詳細については、[BLOB サービス REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)をご覧ください。


### AssetFile を更新する 

ファイルをアップロードしたので、FileAsset サイズ (およびその他) の情報を更新します。次に例を示します。
	
	MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 応答**

成功した場合は、次の内容が返されます。
	
	HTTP/1.1 204 No Content

## Locator と AccessPolicy　を削除します。 

**HTTP 要求**


	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

	
**HTTP 応答**

成功した場合は、次の内容が返されます。

	HTTP/1.1 204 No Content 
	...

**HTTP 要求**

	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 応答**

成功した場合は、次の内容が返されます。

	HTTP/1.1 204 No Content 
	...

 
## <a id="configure_streaming_units"></a>REST API でストリーミング ユニットを構成する

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services では、次のアダプティブ ビットレート ストリーミング テクノロジがサポートされています。HTTP Live Streaming (HLS)、スムーズ ストリーミング、MPEG DASH、および HDS (Adobe Primetime/Access のライセンス所有者のみ対象)。 

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信できます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- コンテンツの配信元となる**ストリーミング エンドポイント**のストリーミング ユニットを 1 つ以上取得する (このセクションで説明)。
- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする (エンコーディングの手順は後述)。  

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。 


>[AZURE.NOTE] 料金設定の詳細については、[Media Services の料金詳細](http://go.microsoft.com/fwlink/?LinkId=275107)に関するページをご覧ください。

ストリーミング占有ユニットの数を変更するには、以下の手順を実行します。
	
### 更新するストリーミング エンドポイントを取得する

たとえば、自分のアカウントの最初のストリーミング エンドポイントを取得します。 (同時に最大 2 つの実行状態のストリーミング エンドポイントを持つことができます)。

**HTTP 要求**: 

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 応答**
	
成功した場合は、次の内容が返されます。
	
	HTTP/1.1 200 OK
	. . . 
	
### ストリーミング エンドポイントのスケールを設定する
 
**HTTP 要求**: 

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
	Content-Type: application/json;odata=verbose
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{"scaleUnits":1}

**HTTP 応答**

	HTTP/1.1 202 Accepted
	Cache-Control: no-cache
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:16:43 GMT
	Content-Length: 0

	
### <a id="long_running_op_status"></a>実行時間の長い操作の状態を確認する

新しいユニットの割り当ては完了するまでに約 20 分かかります。操作の状態を確認するには、 **Operations**メソッドを使用し、操作の Id を指定します。**Scale** 要求への応答で操作 Id が返されます。

	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**HTTP 要求**: 
	
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
**HTTP 応答**
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 515
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
	request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:57:39 GMT
	
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
	      },
	      "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
	      "State":"Succeeded",
	      "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
	      "ErrorCode":null,
	      "ErrorMessage":null
	   }
	}


## <a id="encode"></a>一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする

Media Services に取り込んだアセットには、メディアのエンコード、再パッケージ化、透かしの追加などをクライアントへの配信前に適用できます。高いパフォーマンスと可用性を確保するために、これらの作業は、複数のバックグラウンド ロール インスタンスに対してスケジューリングされて実行されます。これらのアクティビティはジョブと呼ばれ、各[ジョブ](http://msdn.microsoft.com/library/azure/hh974289.aspx)は、アセット ファイルの実際の作業を実行するアトミック タスクで構成されます。 

冒頭で述べたように、Azure Media Services の代表的な用途の 1 つは、クライアントに対するアダプティブ ビットレート ストリーミング配信です。Media Services では、複数のアダプティブ ビットレート MP4 ファイルを、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンス所有者のみ使用可能) の各アダプティブ ビットレート ストリーミング技術がサポートされています。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする。  
- コンテンツ配信元となるストリーミング エンドポイントのストリーミング ユニットを少なくとも 1 つ取得する。 

次のセクションでは、1 つのエンコード タスクを含むジョブを作成する方法を示します。タスクは、**Azure Media Encoder** を使用して一連のアダプティブ ビットレート mp4 にトランス コードする mezzanine ファイルを指定します。ジョブの処理の進行状況を監視する方法についても示します。ジョブが完了すると、アセットにアクセスするために必要なロケーターを作成できます。 

### メディア プロセッサを取得する

Media Services では、メディア プロセッサは、メディア コンテンツのエンコード、形式変換、暗号化、または復号化などの特定の処理タスクを扱うコンポーネントです。このチュートリアルで示したエンコード タスクでは、Azure Media Encoder を使用します。

次のコードでは、エンコーダーの Id を要求します。 

**HTTP 要求**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP 応答**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 07:54:09 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "Description":"Azure Media Encoder",
	         "Name":"Azure Media Encoder",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"4.4"
	      }
	   ]
	}

### ジョブを作成する

各ジョブは実行する処理の種類に応じて 1 つまたは複数のタスクを持つことができます。REST API を使って、2 つの方法のいずれかでジョブおよびジョブに関連するタスクを作成できます。タスクは、Job エンティティのタスク ナビゲーション プロパティまたは OData バッチ処理を使用して、インラインで定義できます。Media Services SDK はバッチ処理を使用します。ただし、このトピックのコード例では、読みやすくするためにタスクをインラインで定義します。バッチ処理の詳細については、[Open Data Protocol (OData) の「Batch Processing (バッチ処理)」](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)をご覧ください。

次の例では、1 つのタスクが設定されたジョブを作成して公開し、特定の解像度と質でビデオをエンコードする方法について説明します。次のドキュメント セクションには、Azure のメディア プロセッサがサポートしているすべての[タスク プリセット](http://msdn.microsoft.com/library/azure/dn619392.aspx)の一覧が含まれています。  

**HTTP 要求**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: application/json
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 482
	
	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
				<outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      }
	   ]
	}

**HTTP 応答**

成功した場合、次の応答が返されます。

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1215
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
	Server: Microsoft-IIS/8.5
	request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:04:35 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
	      },
	      "Tasks":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
	         }
	      },
	      "OutputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
	         }
	      },
	      "InputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
	         }
	      },
	      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "Name":"NewTestJob",
	      "Created":"2015-01-19T08:04:34.3287057Z",
	      "LastModified":"2015-01-19T08:04:34.3287057Z",
	      "EndTime":null,
	      "Priority":0,
	      "RunningDuration":0,
	      "StartTime":null,
	      "State":0,
	      "TemplateId":null,
	      "JobNotificationSubscriptions":{  
	         "__metadata":{  
	            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
	         },
	         "results":[  
	
	         ]
	      }
	   }
	}


ジョブ要求について注意が必要ないくつかの重要事項があります。

- TaskBody プロパティでは、リテラル XML を使用して、タスクが使用するアセットの入力または出力数を定義する必要があります。タスクのトピックには、XML のための XML スキーマ定義が含まれます。
- TaskBody の定義には各 <inputAsset> の内部値と <outputAsset> を JobInputAsset(value) または JobOutputAsset(value) として設定する必要があります。
- タスクは、複数の出力アセットを持つことができます。1 つの JobOutputAsset(x) はジョブ内のタスクの出力として一度だけ使用できます。
- タスクの入力アセットとして、JobInputAsset または JobOutputAsset を指定できます。
- タスクは、サイクルを形成することはできません。
- JobInputAsset または JobOutputAsset に渡す値パラメーターは、Asset のインデックス値を表します。実際の Asset は、Job エンティティ定義の InputMediaAssets および OutputMediaAssets ナビゲーション プロパティで定義されます。 

>[AZURE.NOTE] Media Services は OData v3 上に構築されるため、 InputMediaAssets および OutputMediaAssets ナビゲーション プロパティ コレクション内の個々のアセットは、"__metadata : uri" の名前と値のペアによって参照されます。 

- InputMediaAssets は、Media Services で作成した1 つまたは複数のアセットにマップされます。OutputMediaAssets はシステムによって作成されます。既存のアセットは参照しません。
- OutputMediaAssets は、assetName 属性を使用して名前を付けることができます。この属性が存在しない場合、OutputMediaAsset の名前は、ジョブ名の値またはジョブ ID の値 (Name プロパティが定義されていない場合) のいずれかのサフィックスを持つ <outputAsset> 要素の内部テキストの値になります。たとえば、"Sample"に assetName の値を設定する場合は、OutputMediaAsset Name プロパティは "Sample" に指定されます。ただし、assetName の値を設定せずジョブ名を "NewJob" に設定した場合は、OutputMediaAsset Name は "JobOutputAsset(value)_NewJob" になります。 

	次の例では、assetName 属性を設定する方法を示します。
	
		"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- タスクのチェーンを有効にするには、

	- ジョブに少なくとも 2 つのタスクがある必要があります。
	- タスクの入力がジョブ内の別のタスクの出力である、1 つ以上のタスクがある必要があります。

詳細については、「[Media Services REST API を使用したエンコード ジョブの作成](http://msdn.microsoft.com/library/azure/jj129574.aspx)」をご覧ください。

### 処理の進行状況を監視する

次の例に示すように、State プロパティを使用して、ジョブの状態を取得できます。 

**HTTP 要求**

	GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 0


**HTTP 応答**

成功した場合、次の応答が返されます。

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 17
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Sun, 13 May 2012 05:16:53 GMT
	
	{"d":{"State":2}}


### ジョブを取り消す

Media Services では、CancelJob 関数を使用して実行中のジョブを取り消すことができます。この呼び出しは、ジョブの状態が取り消されている、取り消し中、エラーまたは終了している時にジョブをキャンセルしようとすると 400 エラー コードを返します。

次の例は、CancelJob を呼び出す方法を示します。


**HTTP 要求**


	GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.2
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
	Host: wamsbayclus001rest-hs.net


成功した場合、メッセージ本文なしで応答コード 204 が返されます。

>[AZURE.NOTE] ジョブ id (通常は nb:jid:UUID: somevalue)は、CancelJob へのパラメーターとして渡すときは URL エンコードする必要があります。


### 出力アセットを取得する 

次のセクションでは、ジョブの出力アセット (エンコード済みアセット) の配信ポリシーを構成します。次のコードは、出力アセット Id を要求する方法を示しています。 


**HTTP 要求**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP Response**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 445
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:28:13 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
	   "value":[  
	      {  
	         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "State":0,
	         "Created":"2015-01-19T07:52:15.603",
	         "LastModified":"2015-01-19T07:52:15.603",
	         "AlternateId":null,
	         "Name":"Multibitrate MP4s",
	         "Options":0,
	         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "StorageAccountName":"storagetestaccount001"
	      }
	   ]
	}


## <a id="configure_delivery_method"></a>エンコードされたアセットの配信ポリシーの構成

Media Services のコンテンツを配信するための作業の一環として、アセットの配信ポリシーを構成します。たとえば、アセットの配信にどのようなプロトコルを使用できるか (MPEG DASH、HLS、HDS、スムーズ ストリーミング、またはそのすべてなど) や、アセットを動的に暗号化するかどうか、またどのように暗号化するか (エンベロープ、Common Encryption など) は、アセット配信ポリシーで構成することになります。 

次の **AssetDeliveryPolicies** HTTP 要求 は、動的な暗号化を適用せずに (AssetDeliveryPolicyType は次の値のいずれかを指定できます。None = 0、Blocked = 1、NoDynamicEncryption = 2、DynamicEnvelopeEncryption = 3、DynamicCommonEncryption = 4)、次のプロトコルのいずれかでストリームを配信することを指定します。MPEG DASH、HLS、およびスムーズ ストリーミング プロトコル (AssetDeliveryProtocol は、次の値の組み合わせにすることができます。None = 0、SmoothStreaming = 1、Dash = 2、HLS = 4、Hds = 8、All = 65535)。 


### AssetDeliveryPolicies を作成する


**HTTP 要求**
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 83
	
	{"Name":"Clear Policy", "AssetDeliveryPolicyType":"2","AssetDeliveryProtocol":"7"} 


**HTTP 応答**
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 361
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')
	Server: Microsoft-IIS/8.5
	request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	x-ms-request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 09:13:18 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AssetDeliveryPolicies/@Element",
	   "Id":"nb:adpid:UUID:ef97ae36-b898-4b8a-b427-7819ee726276",
	   "Name":"Clear Policy",
	   "AssetDeliveryProtocol":7,
	   "AssetDeliveryPolicyType":2,
	   "AssetDeliveryConfiguration":null,
	   "Created":"2015-01-19T09:13:18.911615Z",
	   "LastModified":"2015-01-19T09:13:18.911615Z"
	}
    

### アセットの配信ポリシーをアセットとリンクする

次の HTTP 要求は、指定された配信ポリシーを指定されたアセットに関連付けます。  

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 140
	
	{ "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')" }


**HTTP 応答**

	HTTP/1.1 204 No Content
    . . . 


## <a id="publish_get_urls"></a>REST API でアセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する

アセットをストリーミングまたはダウンロードするにはまず、ロケーターを作成してアセットを "発行" する必要があります。アセットに含まれているファイルには、ロケーターを通じてアクセスできます。Media Services では、2 種類のロケーターがサポートされています。メディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) に使用される OnDemandOrigin ロケーターと、メディア ファイルのダウンロードに使用される Access Signature (SAS) ロケーターの 2 種類のロケーターがサポートされています。

ロケーターを作成したら、対象ファイルのストリーミングやダウンロードに使用する URL を作成します。 


スムーズ ストリーミングのストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS のストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH のストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


ファイルをダウンロードするための SAS URL の形式は次のとおりです。

	{blob container name}/{asset name}/{file name}/{SAS signature}

このセクションでは、アセットを"発行"するために必要な次のタスクを実行する方法を示します。  

- 読み取りアクセス許可を持つ AccessPolicy を作成する 
- コンテンツをダウンロードするための SAS URL を作成する 
- ストリーミング コンテンツの配信元 URL を作成する 

### 読み取りアクセス許可を持つ AccessPolicy を作成する

メディア コンテンツをダウンロードおよびストリーミングする前に、まず読み取りアクセス許可を持つ AccessPolicy を定義し、クライアントのために有効にする配信メカニズムの種類を指定する適切な Locator エンティティを作成します。使用できるプロパティの詳細については、「[AccessPolicy エンティティ プロパティ](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties)」をご覧ください。

次の例では、指定された Asset の読み取りアクセス許可のために AccessPolicy を指定する方法を示します。

	POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 74
	Expect: 100-continue
	
	{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

成功した場合、作成した AccessPolicy エンティティについて記述した 201 サクセス コードが返されます。これで、配信して Locator エンティティを作成するファイルが含まれているアセット (出力アセットなど) の Asset Id と AccessPolicy Id を使用できます。

>[AZURE.NOTE]
この基本的なワークフローは (このトピックで既に説明した) アセットの取り込み時にファイルをアップロードするのと同じです。また、ファイルのアップロードのように、ファイルにすぐにアクセスする必要がある場合は、StartTime 値を現在の時刻より 5 分前に設定します。この操作は、クライアントと Media Services との間でクロック スキューがある可能性があるために必要です。StartTime の値は、次のような DateTime 形式でする必要があります。YYYY-MM-DDTHH:mm:ssZ例: "2014-05-23T17:53:50Z"


### コンテンツをダウンロードするための SAS URL を作成する 

次のコードは、以前に作成されてアップロードされたメディア ファイルをダウンロードするために使用する URL を取得する方法を示します。AccessPolicy には読み取りアクセス許可が設定され、ロケーター パスは SAS ダウンロード URL を参照します。

	POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

成功した場合、次の応答が返されます。

	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1150
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:32 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
	      "ExpirationDateTime":"\/Date(1337049393000)\/",
	      "Type":1,
	      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "StartTime":"\/Date(1337031393000)\/"
	   }
	}


返された **Path** プロパティには、SAS URL が含まれています。

>[AZURE.NOTE]
ストレージ暗号化コンテンツをダウンロードする場合は、表示する前に手動で暗号化を解除するか、または処理タスク内の Storage Decryption MediaProcessor を使用して、処理されたファイルを OutputAsset にクリア テキストで出力し、その Asset からダウンロードする必要があります。処理の詳細については、「Media Services REST API によるエンコード ジョブの作成」をご覧ください。また、作成した後は SAS URL ロケーターを更新できません。たとえば、更新された StartTime 値を持つ同じロケーターを再利用することはできません。これは、SAS URL の作成方法による制限です。ロケーターの有効期限が切れた後のアセットにダウンロードのためにアクセスする場合、新しい StartTime で新しくロケーターを作成する必要があります。

### ファイルをダウンロードする

AccessPolicy と Locator を設定したら、Azure Storage REST API を使用してファイルをダウンロードできます。  

>[AZURE.NOTE] 前のセクションで受信したロケーター **パス**の値にダウンロードするファイルのファイル名を追加する必要があります。例: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? をご覧ください。」をご覧ください。」をご覧ください。 

Azure ストレージ BLOB の使用の詳細については、[BLOB サービス REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)をご覧ください。

以前 (エンコード アダプティブ MP4 セットに) を実行して、エンコード ジョブでの結果としてある複数の MP4 ファイルを段階的にダウンロードできます。次に例を示します。    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### ストリーミング コンテンツのストリーミング URL を作成する


次のコードは、ストリーミング URL ロケーターを作成する方法を示します。

	POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

成功した場合、次の応答が返されます。

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 981
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:39 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
	      "ExpirationDateTime":"\/Date(1337049395000)\/",
	      "Type":2,
	      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
	      "StartTime":"\/Date(1337031395000)\/"
	   }
	}

ストリーミング メディア プレーヤーでスムーズ ストリーミングの配信元 URL をストリームするには、後ろに "/manifest" が続くスムーズ ストリーミング マニフェスト ファイルの名前を Path プロパティに追加する必要があります。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS をストリーミングするには、"/manifest" の後に (format=m3u8-aapl) を追加します。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH をストリーミングするには、"/manifest" の後に (format=mpd-time-csf) を追加します。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>コンテンツの再生  

SAS URL を取得したら、ブラウザー (IE、Chrome、Safari など) に URL を貼り付けることによって、MP4 ファイルをプログレッシブにダウンロードできます。

アダプティブ ストリーミング URL をテストするには、次のプレーヤーを使用します。

スムーズ ストリーミングをテストするには、[http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) または [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) を使用してください。

MPEG DASH をテストするには、[http://dashif.org](http://dashif.org/reference/players/javascript/) を使用してください。

HLS をテストするには、iOS デバイス、Safari デバイス、[3ivx-hls-player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614) を使用してください。 



## その他のリソース
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Azure Media Services 101 - 今すぐビデオをオンラインにしましょう!)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Azure Media Services 102 - 動的パッケージ化機能とモバイル デバイス)</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理ポータル]: http://manage.windowsazure.com/



<!--HONumber=52-->