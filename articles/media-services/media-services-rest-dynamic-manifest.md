<properties 
	pageTitle="Azure Media Services REST API を使用したフィルターの作成" 
	description="このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。"
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Azure Media Services REST API を使用したフィルターの作成

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)


Media Services のリリース 2.11 以降では、資産にフィルターを定義できます。これらのフィルターは、ビデオの 1 つのセクションのみの再生や (ビデオ全体を再生するのではなく)、顧客のデバイスが処理できるオーディオ サブセットとビデオ演奏のみの再生 (資産に関連付けられているすべての演奏ではなく) などを顧客が選択できるようにする、サーバー側のルールです。この資産のフィルター処理は**動的マニフェスト**によってアーカイブされます。これは、指定したフィルターに基づいてビデオをストリームする必要がある顧客のリクエストに応じて作成されます。

フィルターと動的マニフェストに関連する詳細については、「[動的マニフェストの概要](media-services-dynamic-manifest-overview.md)」をご覧ください。

このトピックでは、REST API を使用してフィルターを作成、更新、削除する方法を説明します。

##フィルターの作成に使用する種類

次の種類の REST API を使用してフィルターを作成します。

- [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- [FilterTrackSelect と FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)



>[AZURE.NOTE] Media Services REST API を使用する場合は、次のことに考慮します。
>
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。詳細については、「[Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

>https://media.windows.net に正常に接続すると、別の Media Services URI が指定された 301 リダイレクトが表示されます。「[Media Services REST API を使用して Media Services アカウントに接続する](media-services-rest-connect_programmatically.md)」で説明するとおり、続けて新しい URI を呼び出す必要があります。


##フィルターの作成

###グローバル フィルターの作成

グローバル フィルターを作成するには、次の HTTP 要求を使用します。

####HTTP 要求

要求ヘッダー

	POST https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host:media.windows.net 

Request body

	{  
	   "Name":"GlobalFilter",
	   "PresentationTimeRange":{  
	      "StartTimestamp":"0",
	      "EndTimestamp":"9223372036854775807",
	      "PresentationWindowDuration":"12000000000",
	      "LiveBackoffDuration":"0",
	      "Timescale":"10000000"
	   },
	   "Tracks":[  
	      {  
	         "PropertyConditions":
	              [  
	            {  
	               "Property":"Type",
	               "Value":"audio",
	               "Operator":"Equal"
	            },
	            {  
	               "Property":"Bitrate",
	               "Value":"0-2147483647",
	               "Operator":"Equal"
	            }
	         ]
	      }
	   ]
	}




####HTTP 応答
	
	HTTP/1.1 201 Created 

###ローカル AssetFilter の作成

ローカル AssetFilter を作成するには、次の HTTP 要求を使用します。

####HTTP 要求

要求ヘッダー

	POST https://media.windows.net/API/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net  

Request body

	{   
	   "Name":"AssetFilter", 
	   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
	   "PresentationTimeRange":{   
	      "StartTimestamp":"0", 
	      "EndTimestamp":"9223372036854775807", 
	      "PresentationWindowDuration":"12000000000", 
	      "LiveBackoffDuration":"0", 
	      "Timescale":"10000000" 
	   }, 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	              [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

####HTTP 応答 

	HTTP/1.1 201 Created 
	. . . 

##フィルターの一覧

###すべてのグローバル **フィルター**を AMS アカウントで取得する

フィルターの一覧を表示するには、次の HTTP 要求を使用します。

####HTTP 要求
	 
	GET https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 
	
### 資産に関連付けられている **AssetFilter** を取得する

####HTTP 要求

	GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 

###ID に基づいた **AssetFilter** を取得する

####HTTP 要求

	GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000


##フィルターの更新
 
新しいプロパティ値でフィルターを更新するには、PATCH、PUT、MERGE を使用します。これらの操作の詳細については、「[PATCH、PUT、MERGE](http://msdn.microsoft.com/library/dd541276.aspx)」をご覧ください。
 
フィルターを更新する場合、ストリーミング エンドポイントでルールを更新するのに最大 2 分かかります。このフィルターを使用してコンテンツが処理された場合 (また、プロキシと CDN にキャッシュされている場合)、このフィルターを更新するとプレイヤーでエラーが発生します。フィルターを更新した後にキャッシュをクリアすることをお勧めします。このオプションが利用できない場合は、別のフィルターを使用することを検討してください。
 
###グローバル フィルターの更新

グローバル フィルターを更新するには、次の HTTP 要求を使用します。

####HTTP 要求
 
要求ヘッダー:

	MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	Content-Length: 384
	
要求本文:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

###ローカル AssetFilter の更新

ローカル フィルターを更新するには、次の HTTP 要求を使用します。

####HTTP 要求

要求ヘッダー:

	MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	
要求本文:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 


##フィルターの削除


###グローバル フィルターの削除

グローバル フィルターを削除するには、次の HTTP 要求を使用します。
	
####HTTP 要求

	DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 


###ローカル AssetFilter の削除

ローカル AssetFilter を削除するには、次の HTTP 要求を使用します。

####HTTP 要求

	DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 

##フィルターを使用するストリーミング URL の構築

資産の発行と配信方法については、「[顧客へのコンテンツの配信に関する概要](media-services-deliver-content-overview.md)」をご覧ください。


次の例では、ストリーミング URL にフィルターを追加する方法を示します。

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##関連項目 

[動的マニフェストの概要](media-services-dynamic-manifest-overview.md)
 

 

<!---HONumber=AcomDC_0211_2016-->