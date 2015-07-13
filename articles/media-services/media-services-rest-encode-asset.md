<properties 
	pageTitle="Azure メディア エンコーダーを使用してアセットをエンコードする方法" 
	description="Azure メディア エンコーダーを使用して Media Services でメディア コンテンツをエンコードする方法について説明します。コード サンプルでは REST API を使用しています。" 
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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Azure メディア エンコーダーを使用してアセットをエンコードする方法

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」シリーズの一部です。

##概要
インターネット経由でデジタル ビデオを配信するには、メディアを圧縮する必要があります。デジタル ビデオ ファイルはサイズがとても大きく、インターネット経由で配信したり、顧客の端末でうまく表示したりできない可能性があります。エンコードはビデオやオーディオを圧縮するプロセスです。圧縮することでメディア コンテンツが見やすくなります。

エンコード ジョブは Media Services で最も一般的な処理の 1 つです。エンコード ジョブを作成することで、メディア ファイルをあるエンコードから別のエンコードに変換できるようになります。エンコードには、Media Services の組み込みメディア エンコーダーを使用できます。Media Services のパートナーから提供されているエンコーダーを使うこともできます。サードパーティのエンコーダーは Azure Marketplace から入手できます。エンコーディング タスクの詳細を指定するには、エンコーダー用に定義されたプリセット文字列を使用するか、プリセット構成ファイルを使用します。使用できるプリセットの種類を表示するには、「[Azure Media Services のタスク プリセット](https://msdn.microsoft.com/library/azure/dn619392.aspx)」をご覧ください。サード パーティのエンコーダーを使用した場合は、[ファイルを検証](https://msdn.microsoft.com/library/azure/dn750842.aspx)する必要があります。

各ジョブは実行する処理の種類に応じて 1 つまたは複数のタスクを持つことができます。REST API を使って、2 つの方法のいずれかでジョブとそれに関連するタスクを作成できます。タスクは、Job エンティティのタスク ナビゲーション プロパティまたは OData バッチ処理を使用して、インラインで定義できます。Media Services SDK はバッチ処理を使用します。ただし、このトピックのコード例では、読みやすくするためにタスクをインラインで定義します。バッチ処理の詳細については、[Open Data Protocol (OData) のバッチ処理](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)に関するページを参照してください。またバッチ処理の例は、[ジョブ](https://msdn.microsoft.com/library/azure/hh974289.aspx)に関するトピックでご覧いただけます。

中間ファイルは常にアダプティブ ビットレート MP4 セットにエンコードして、その後[動的パッケージ](https://msdn.microsoft.com/library/azure/jj889436.aspx)を使用して目的の形式に変換することをお勧めします。動的パッケージを活用するには、コンテンツの配信元となるストリーミング エンドポイントのオンデマンド ストリーミング ユニットを 1 つ以上取得する 必要があります。詳細については、「[Media Services の規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」を参照してください。

出力アセットがストレージで暗号化されている場合は、アセット配信のポリシーを構成する必要があります。詳細については、「[アセット配信ポリシーの構成](media-services-rest-configure-asset-delivery-policy.md)」をご覧ください。



##1 つのエンコード タスクを持つジョブの作成 

>[AZURE.NOTE]Media Services REST API を使用する場合は、次のことに考慮します。
>
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。詳細については、「[Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

>https://media.windows.net に正常に接続すると、別の Media Services URI が指定された 301 リダイレクトが表示されます。「[Media Services REST API を使用して Media Services アカウントに接続する](media-services-rest-connect_programmatically.md)」で説明するとおり、続けて新しい URI を呼び出す必要があります。


次の例では、1 つのタスクが設定されたジョブを作成して公開し、特定の解像度と質でビデオをエンコードする方法について説明します。Azure メディア エンコーダーでエンコードするときは、[こちら](https://msdn.microsoft.com/library/azure/dn619389.aspx)で指定されているタスク構成のプリセットを使うことができます。
	
要求:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

応答:
	
	HTTP/1.1 201 Created

	. . . 

##考慮事項

- TaskBody プロパティでは、リテラル XML を使用して、タスクが使用するアセットの入力または出力数を定義する必要があります。タスクのトピックには、XML のための XML スキーマ定義が含まれます。
- TaskBody の定義には各 <inputAsset> の内部値と <outputAsset> を JobInputAsset(value) または JobOutputAsset(value) として設定する必要があります。
- タスクは、複数の出力アセットを持つことができます。1 つの JobOutputAsset(x) はジョブ内のタスクの出力として一度だけ使用できます。
- タスクの入力アセットとして、JobInputAsset または JobOutputAsset を指定できます。
- タスクは、サイクルを形成することはできません。
- JobInputAsset または JobOutputAsset に渡す値パラメーターは、Asset のインデックス値を表します。実際の Asset は、Job エンティティ定義の InputMediaAssets および OutputMediaAssets ナビゲーション プロパティで定義されます。 
- Media Services は OData v3 上に構築されるため、 InputMediaAssets および OutputMediaAssets ナビゲーション プロパティ コレクション内の個々のアセットは、"__metadata : uri" の名前と値のペアによって参照されます。
- InputMediaAssets は、Media Services で作成した1 つまたは複数のアセットにマップされます。OutputMediaAssets はシステムによって作成されます。既存のアセットは参照しません。
- OutputMediaAssets は、assetName 属性を使用して名前を付けることができます。この属性が存在しない場合、OutputMediaAsset の名前は、ジョブ名の値またはジョブ ID の値 (Name プロパティが定義されていない場合) のいずれかのサフィックスを持つ <outputAsset> 要素の内部テキストの値になります。たとえば、"Sample"に assetName の値を設定する場合は、OutputMediaAsset Name プロパティは "Sample" に指定されます。ただし、assetName の値を設定せずジョブ名を "NewJob" に設定した場合は、OutputMediaAsset Name は "JobOutputAsset(value)_NewJob" になります。 

次の例では、assetName 属性を設定する方法を示します。

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##チェーン タスクによるジョブの作成

アプリケーション シナリオの多くで、一連のタスク処理が開発者によって作成されます。Media Services では、一連のチェーン タスクを作成できます。各タスクはそれぞれ異なる処理手順を実行し、異なるメディア プロセッサを使うことができます。チェーン タスクでは、あるタスクから別のタスクにアセットを渡しながら一連のタスクを順番に実行できます。ただし、ジョブで実行されるタスクが必ず順番に実行されなければならないということではありません。チェーン タスクを作成するときは、一連の **ITask** オブジェクトは 1 つの **IJob** オブジェクト内で作成されます。

>[AZURE.NOTE]現時点では、ジョブごとに設定できるタスクは 30 までです。30 以上のタスクをつなげる必要がある場合、別のジョブを作成してタスクを設定します。


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###考慮事項

タスクのチェーンを有効にするには、

- ジョブに少なくとも 2 つのタスクがある必要があります。
- タスクの入力がジョブ内の別のタスクの出力である、1 つ以上のタスクがある必要があります。


## JobTemplate を使用したジョブの作成


共通する一連のタスクを使って複数のアセットを処理するときは、JobTemplates を使用して、既定のタスク プリセットやタスクの順序などを指定すると便利です。

次の例では、TaskTemplate をインラインで定義して JobTemplate を作成しています。この TaskTemplate では、Azure メディア エンコーダーを MediaProcessor として、アセット ファイルをエンコードしていますが、他の MediaProcessors を使用してもかまいません。


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]GUID 識別子は、他の Media Services エンティティとは異なり、TaskTemplate ごとに新たに定義し、要求本文の taskTemplateId と Id プロパティに設定する必要があります。コンテンツ ID スキームについては、「Azure Media Services エンティティの識別」で説明されているスキームに従ってください。また、JobTemplates を更新することはできません。最新の変更内容を基に新たに作成する必要があります。
 

成功した場合、次の応答が返されます。
	
	HTTP/1.1 201 Created
	
	. . .


次の例では、JobTemplate ID を参照してジョブを作成する方法を示します。

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

成功した場合、次の応答が返されます。
	
	HTTP/1.1 201 Created
	
	. . . 


##Media Services Encoder の出力ファイル名の制御 

Azure メディア エンコーダーの出力ファイル名は、エンコーディング プロセスと入力アセットの各種属性を組み合わせて自動的に作成されます。各属性の識別には、以下に示したマクロが使用されます。

出力ファイルの命名に使用できるすべてのマクロを以下に示します。Audio Bitrate - オーディオをエンコードするときに使用されるビットレート (kbps で指定)

- Audio Codec - オーディオのエンコードに使用するコーデック。有効な値は AAC、WMA、DDP です。
- Channel Count - エンコードするオーディオ チャンネルの数。有効な値は 1、2、6 です。
- Default extension – 既定のファイル拡張子。 
- Language - オーディオに使用される言語を表す BCP-47 言語コード。現在は、"und" が既定で使用されます。 
- Original File Name - Azure Storage にアップロードされたファイルの名前。
- StreamId – プリセット ファイルの <StreamInfo> 要素の streamID 属性で定義されたストリーム ID。 
- Video Codec - エンコードに使用するコーデック。有効な値は H264 と VC1 です。
- Video Bitrate - ビデオをエンコードするときに使用されるビットレート (kbps で指定)

Media Services Encoder によって生成されるファイルの名前は、以上のマクロを任意の順序で組み合わせることによって制御できます。たとえば、既定の名前付け規則は次のようになっています。

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

ファイルの名前付け規則を指定するには、[Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx) 要素の DefaultMediaOutputFileName 属性を使用します。次に例を示します。

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

マクロ間にはアンダースコアが挿入されます。たとえば、この構成で生成されるファイル名は、MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4 のようになります。

##次のステップ
これで、ジョブを作成してアセットをエンコードする方法を学習できました。次は、[メディア サービスでジョブの進行状況をチェックする方法](media-services-rest-check-job-progress.md)に関するトピックに進みます。

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->