<properties 
	pageTitle="よく寄せられる質問" 
	description="よく寄せられる質問 (FAQ)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="03/01/2016"  
	ms.author="juliako"/>


#よく寄せられる質問  

##概要

Q: インデックス作成の規模はどのように設定できますか?

A: 予約ユニットは、エンコード タスクでもインデックス作成タスクでも同じです。詳細については、「[Media Services の規模の設定方法](media-services-how-to-scale.md)」をご覧くださいIndexer のパフォーマンスは、予約ユニットの種類に左右されないことに**注意してください**。

Q: ビデオをアップロード、エンコード、公開しました。ビデオをストリームしようとしても再生できない場合、どんな原因が考えられますか。

可能性が高い原因は、再生しようとしているストリーミング エンドポイントに、1 つ以上の予約済みストリーミング エンドポイント ユニットが割り当てられていないことです。[Media Services の規模の設定方法](media-services-how-to-scale.md)の手順をご覧ください。

Q: ライブ ストリームで合成はできますか。

A: 現時点では、Azure Media Services でのライブ ストリームの合成はできないため、コンピューター上で事前に作成する必要があります。

Q: ライブ ストリーミングを Azure CDN で使用できますか。

A: Media Services では Azure CDN との統合をサポートしています (詳細については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)」をご覧ください)。CDN でライブ ストリーミングを使用できます。Azure Media Services は、スムーズ ストリーミング、HLS、MPEG-DASH の出力を提供します。これらのすべての形式では、データの転送に HTTP を使用し、HTTP キャッシュを活用します。ライブ ストリーミングでは実際のビデオ/オーディオ データがフラグメントに分割され、この個別のフラグメントが CDN にキャッシュされます。更新する必要があるデータは、マニフェスト データのみです。CDN はマニフェスト データを定期的に更新します。

Q: Azure Media services では画像の保存はサポートされますか。

A: JPEG や PNG 画像を保存するだけの場合は、Azure Blob Storage を使用してください。これらをビデオやオーディオ アセットに関連付けることがない限り、 Media Services アカウントに保存するメリットはありません。または、ビデオ エンコーダーで画像をオーバーレイとして使用する必要がある場合、Media Services エンコーダーではビデオの上に画像をオーバーレイさせることができ、JPEG や PNG が入力形式としてサポートされています。詳細については、「[オーバーレイの作成](https://msdn.microsoft.com/library/azure/dn640496.aspx)」を参照してください。

Q: 1 つの Media Services アカウントから他のアカウントにアセットをコピーする方法を教えてください。

A: 1 つの Media Services アカウントから他のアカウントにアセットをコピーするには、[Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) リポジトリで入手できる [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 拡張メソッドを使用します。詳細については、[こちらの](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices)フォーラムのスレッドをご覧ください。

Q: エンコーディング プロセスでビデオをローテーションする方法を教えてください。

A: [メディア エンコーダー スタンダード](media-services-dotnet-encode-with-media-encoder-standard.md)は、90/180/270 の角度によるローテーションをサポートしています。既定の動作 "自動" により、受信 MP4/MOV ファイルのローテーション メタデータの検出、およびこの補正が試行されます。[ここ](http://msdn.microsoft.com/library/azure/mt269960.aspx)で定義されたいずれかの JSON プリセットに対する次の**ソース**要素を含めます。
	
	"Version": 1.0,
	"Sources": [
	{
	  "Streams": [],
	  "Filters": {
	    "Rotation": "90"
	  }
	}
	],
	"Codecs": [
	
	...

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0302_2016-->