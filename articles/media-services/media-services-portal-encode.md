<properties
	pageTitle="Azure ポータルで Media Encoder Standard を使用した資産のエンコード | Microsoft Azure"
	description="このチュートリアルでは、Azure ポータルで Media Encoder Standard を使用して資産をエンコードする手順について説明します。"
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
	ms.date="08/29/2016"
	ms.author="juliako"/>


# Azure ポータルで Media Encoder Standard を使用した資産のエンコード

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。アダプティブ ビットレート ストリーミング用にビデオを準備するには、ソース ビデオをマルチビットレートのファイルにエンコードする必要があります。ビデオのエンコードには **Media Encoder Standard** エンコーダーを使用する必要があります。

Media Services には動的パッケージ化機能もあり、マルチビットレート MP4 でエンコードされたコンテンツを、MPEG DASH、HLS、スムーズ ストリーミング、HDS のストリーミング形式でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- ソース ファイルを一連のマルチビットレート MP4 ファイルにエンコードする (エンコードの手順はこのセクションで後ほど説明します)。
- コンテンツ配信元となるストリーミング エンドポイントのストリーミング ユニットを少なくとも 1 つ取得する。詳細については、「[ストリーミング エンドポイントの構成](media-services-portal-vod-get-started.md#configure-streaming-endpoints)」セクションを参照してください。

メディア処理をスケール調整する場合は、[こちら](media-services-portal-scale-media-processing.md)のトピックをご覧ください。

## Azure ポータルでのエンコード

ここでは、Media Encoder Standard でコンテンツをエンコードする手順について説明します。

1.  **[設定]** ウィンドウで、**[資産]** を選択します。
2.  **[資産]** ウィンドウで、エンコードする資産を選択します。
3.  **[エンコード]** ボタンをクリックします。
4.  **[Encode an asset (資産のエンコード)]** ウィンドウで、"Media Encoder Standard" プロセッサとプリセットを選択します。たとえば、入力ビデオの解像度が 1920 x 1080 ピクセルであるとわかっている場合は、"H264 Multiple Bitrate 1080p" のプリセットを使用できます。プリセットの詳細については、[こちら](https://msdn.microsoft.com/library/azure/mt269960.aspx)の記事を参照してください。重要なのは、入力ビデオに最適なプリセットを選択することです。低解像度 (640 x 360) のビデオの場合は、既定の "H264 Multiple Bitrate 1080p" のプリセットは使用しないでください。
	
	出力資産とジョブの名前を編集するオプションを利用すると、効率よく管理を行えます。
		
	![Encode assets](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **[作成]** をクリックします。


##次のステップ

[こちら](media-services-portal-check-job-progress.md)の記事で説明するように、Azure ポータルを使用してエンコード ジョブの進行状況を監視できます。

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->