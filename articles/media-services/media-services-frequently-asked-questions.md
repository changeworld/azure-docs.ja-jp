<properties 
	pageTitle="よく寄せられる質問" 
	description="よく寄せられる質問 (FAQ)" 
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
	ms.date="03/15/2015" 
	ms.author="juliako"/>


#よく寄せられる質問  

##概要

Q:インデックス作成の規模はどのように設定できますか?

A:占有ユニットは、エンコード タスクでもインデックス作成タスクでも同じです。詳細については、「[メディア サービスの規模の設定方法](media-services-how-to-scale.md)」をご覧ください. ****Indexer のパフォーマンスは、占有ユニットの種類に左右されないことに注意してください。

Q:ビデオをアップロード、エンコード、公開しました。ビデオをストリームしようとしても再生できない場合、どんな原因が考えられますか。 

A:可能性が高い原因は、再生しようとしているストリーミング エンドポイントに、1 つ以上の予約済みストリーミング エンドポイント ユニットが割り当てられていないことです。[メディア サービスの規模の設定方法](media-services-how-to-scale.md)の手順をご覧ください。.

Q:ライブ ストリームで合成はできますか。 

A:現時点では、Azure Media Services でのライブ ストリームの合成はできないため、コンピューター上で事前に作成する必要があります。

Q:ライブ ストリーミングを Azure CDN で使用できますか。 

A:Media Services では Azure CDN との統合をサポートしています (詳細については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)」をご覧ください。).  CDN でライブ ストリーミングを使用できます。Azure Media Services は、スムーズ ストリーミング、HLS、MPEG-DASH の出力を提供します。これらのすべての形式では、データの転送に HTTP を使用し、HTTP キャッシュを活用します。ライブ ストリーミングでは実際のビデオ/オーディオ データがフラグメントに分割され、この個別のフラグメントが CDN にキャッシュされます。更新する必要があるデータは、マニフェスト データのみです。CDN はマニフェスト データを定期的に更新します。

Q:Azure Media services では画像の保存はサポートされますか。

A:JPEG や PNG 画像を保存するだけの場合は、Azure Blob ストレージを使用してください。これらをビデオやオーディオ アセットに関連付けることがない限り、 Media Services アカウントに保存するメリットはありません。または、ビデオ エンコーダーで画像をオーバーレイとして使用する必要がある場合、Media Services エンコーダーではビデオの上に画像をオーバーレイさせることができ、JPEG や PNG が入力形式としてサポートされています。詳細については、「[オーバーレイの作成](https://msdn.microsoft.com/library/azure/dn640496.aspx)」をご覧ください。


<!--HONumber=52--> 