<properties
	pageTitle="Azure クラシック ポータルを使用してメディア処理の規模を設定する方法"
	description="アカウントにプロビジョニングする [オンデマンド ストリーミング予約ユニット] および [エンコード予約ユニット] の数を指定することで Media Services の規模を設定する方法について説明します。"
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="juliako"/>


# Azure クラシック ポータルを使用してメディア処理の規模を設定する方法

## その他のテクノロジを使用してこのタスクを実行する

このページでは、メディア処理の規模を設定する方法について概要を説明すると共に、それを Azure クラシック ポータルを使用して実行する方法について示します。また、その他のテクノロジを使用してこのタスクを実行することもできます。

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [ポータル](media-services-portal-encoding-units.md)
- [REST ()](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## 概要

Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。予約ユニットの種類は、**S1**、**S2**、**S3** から選択できます。たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。詳細については、[予約ユニットの種類](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)に関するページを参照してください。

予約ユニットの種類を指定するだけでなく、予約ユニットを使用したアカウントのプロビジョニングを指定することもできます。プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

## さまざまな予約ユニットの種類の選択

次の表は、さまざまなエンコーディングの速度を選択して決定するときに役立ちます。ベンチマーク ケースや SAS URL も記載されています。これらの SAS URL を使用してビデオをダウンロードして、独自のテストを実行できます。

|**S1**|**S2**|**S3**|
----------|------------|----------|------------
目的のユース ケース| シングル ビットレート エンコード。<br/>解像度が SD 以下であり、時間的制約がなく、低コストなファイル。|シングル ビットレート エンコードとマルチビットレート エンコード。<br/>通常は SD と HD 両方のエンコードに使用されます。 |シングル ビットレート エンコードとマルチビットレート エンコード。<br/>解像度がフル HD および 4K であるビデオ。時間に依存しない高速ターンアラウンド エンコード。 
ベンチマーク|[入力ファイル: 640x360p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>同じ解像度でシングル ビットレート MP4 ファイルにエンコードするには約 11 分かかります。|[入力ファイル: 1280x720p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>[H264 Single Bitrate 720p] プリセットでのエンコードは約 5 分かかります。<br/><br/>[H264 Multiple Bitrate 720p] プリセットでのエンコードは約 11.5 分かかります。|[入力ファイル: 1920x1080p を 29.97 フレーム/秒で 5 分間](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>[H264 Single Bitrate 1080p] プリセットでのエンコードは約 2.7 分かかります。<br/><br/>[H264 Multiple Bitrate 1080p] プリセットでのエンコードは約 5.7 分かかります。

##考慮事項

>[AZURE.IMPORTANT] 次の考慮事項が適用されます。

- 予約ユニットは、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットで高速処理は行いません。

- 共有プールを使用する (すなわち、予約ユニットを使用しない) 場合のエンコード タスクのパフォーマンスは S1 予約ユニットを使用したときと同等になります。ただし、タスクがキューに登録された状態である時間の上限はなく、同時に実行されるタスクの数は最大で 1 つです。

- ブラジル南部、インド西部、インド中部、およびインド南部のデータ センターでは、予約ユニットの種類 **S3** は提供されません。

- コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。

## 予約ユニットの種類の変更

予約ユニットの種類と予約ユニットの数を変更するには、以下の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、**[Media Services]** をクリックします。次に、メディア サービスの名前をクリックします。

2. **[エンコード]** ページを選択します。

	**[予約ユニットの種類]** を変更するには、[S1]、[S2]、または [S3] をクリックします。

	選択した占有ユニットの種類の占有ユニット数を変更するには、**[エンコード]** スライダーを使用します。


	![[プロセッサ] ページ](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

3. [保存] ボタンを押して、変更を保存します。

	[保存] をクリックすると、新しい予約ユニットがすぐに割り当てられます。
 

##クォータと制限

クォータと制限の詳細、サポート チケットを開く方法については、「[Quotas and limitations (クォータと制限)](media-services-quotas-and-limitations.md)」をご覧ください



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0518_2016-->