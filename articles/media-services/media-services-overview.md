<properties 
	pageTitle="Azure Media Services の概要と一般的なシナリオ" 
	description="このトピックでは、Azure Media Services の概要を説明します。" 
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
	ms.topic="hero-article" 
 	ms.date="04/26/2016"
	ms.author="juliako;anilmur"/>

#Azure Media Services の概要と一般的なシナリオ

Microsoft Azure Media Services は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。Media Services は、オンデマンド、また各種クライアント (TV、PC、モバイル デバイスなど) へのライブ ストリーム配信の両方でのビデオやオーディオの安全なアップロード、格納、エンコード、パッケージ化を可能にする REST API に基づいています。

Media Services を使いこなして、エンド ツー エンドのワークフローを構築できます。ワークフローの一部にサード パーティのコンポーネントを使用することもできます。たとえば、サード パーティのエンコーダーを使用してエンコードしてから、Media Services を使用してアップロード、保護、パッケージ化、配信などを行うことができます。

コンテンツをライブ ストリーム配信したり、オンデマンドで配信したりできます。このトピックでは、コンテンツの[ライブ](media-services-overview.md#live_scenarios)配信と[オンデマンド](media-services-overview.md#vod_scenarios)配信の共通シナリオを紹介します。このトピックでは、その他の関連トピックのリンクも提供します。

## SDK とツール 

Media Services ソリューションを構築するために、以下を使用できます。

- [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 使用可能な次のいずれかの SDK: 
	- [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services) 
	- [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java) 
	- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) 
	- [Azure Media Services for Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (これは、Node.js SDK の Microsoft 以外のバージョンです。コミュニティによって管理されており、現在 AMS API を 100% カバーしてはいません。) 
- 既存のツール: 
	- [Azure クラシック ポータル](http://manage.windowsazure.com/) 
	- [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) は Windows 用の Winforms/C# アプリケーションです)

##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##ポスター

[ここ](https://azure.microsoft.com/documentation/infographics/media-services/)で、メディアの作成から使用までの Azure Media Services のワークフローを示す AMS ポスターを確認できます。

##前提条件

Azure Media Services を使用するには、次が必要です。
 
3. Azure アカウント。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](azure.microsoft.com)を参照してください。
2. Azure Media Services アカウントを作成します。Azure Media Services アカウントを作成するには、Azure クラシック ポータル、.NET、REST API を使用します。詳細については、「[アカウントの作成](media-services-create-account.md)」を参照してください。
3. (省略可能) 開発環境をセットアップします。開発環境の .NET または REST API を選択します。詳細については、「[環境を設定する](media-services-dotnet-how-to-use.md)」を参照してください。 

	また、プログラミングによる[接続](media-services-dotnet-connect-programmatically.md)方法についても確認してください
4. (推奨) 1 つまたは複数のスケール単位を割り当てます。実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。詳細については、「[ストリーミング エンドポイントの管理](media-services-manage-origins.md)」を参照してください。

##概念と概要

Azure Media Services の概念については、「[概念](media-services-concepts.md)」を参照してください。

Azure Media Services のすべての主要コンポーネントを紹介する入門シリーズについては、[Azure Media Services のステップバイステップ チュートリアル](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series)に関するページを参照してください。このシリーズでは、概念の概要をわかりやすく示し、AMSE ツールを使用して AME タスクについて説明しています。AMSE ツールは Windows ツールです。このツールは、[AMS SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、または [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) を使用してプログラムで実行できるほとんどのタスクをサポートしています。

##<a id="vod_scenarios"></a>Azure Media Services によるメディアのオンデマンド配信: 共通のシナリオとタスク

このセクションでは、一般的なシナリオと関連するトピックへのリンクを提供します。次の図は、コンテンツのオンデマンド配信に関連する Media Services プラットフォームの主要な部分を示しています。

![VoD ワークフロー](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###ストレージのコンテンツを保護し、ストリーミング メディアを平文 (暗号化されていない) で配信する

1. 高品質な中間ファイルを資産にアップロードします。
	
	ストレージ暗号化オプションを資産に適用し、アップロード中のコンテンツとストレージ内のコンテンツを保護することをお勧めします。
 
1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。

	ストレージ暗号化オプションを出力資産に適用し、保存されているコンテンツを保護することをお勧めします。
	
1. 資産配信ポリシーを構成します (動的パッケージで使用)。
	
	アセットがストレージで暗号化されている場合は、アセット配信ポリシーを構成する**必要があります**。

1. OnDemand ロケーターを作成して資産を発行します。

	コンテンツをストリームするストリーミング エンドポイントに少なくとも 1 つのストリーミング予約ユニットがあることを確認します。

1. 公開済みコンテンツをストリーミングします。

###ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する  

動的暗号化を使用するには、暗号化されたコンテンツのストリーミング元となるストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットが必要です。

1. 高品質な中間ファイルを資産にアップロードします。ストレージ暗号化オプションを資産に適用します。
1. アダプティブ ビットレート MP4 ファイルのセットにエンコードする。ストレージ暗号化オプションを出力資産に適用します。
1. 再生中に動的に暗号化する資産の、暗号化コンテンツ キーを作成します。
2. コンテンツ キー承認ポリシーを構成します。
1. 資産配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
1. OnDemand ロケーターを作成して資産を発行します。
1. 公開済みコンテンツをストリーミングします。 

###Media Analytics を使用して、ビデオから実用的な洞察を得る 

Media Analytics は音声および視覚コンポーネントの集合体であり、組織や企業がこれを活用することで、ビデオ ファイルから実用的な洞察を簡単に引き出すことができます。詳細については、「[Azure Media Services Analytics の概要](media-services-analytics-overview.md)」を参照してください。

1. 高品質な中間ファイルを資産にアップロードします。
2. 以下の Media Analytics サービスを使用してビデオを処理します。
	
	- **Indexer** - [Azure Media Indexer 2 でビデオを処理する](media-services-process-content-with-indexer2.md)
	- **Hyperlapse** - [Azure Media Hyperlapse を使用する Hyperlapse メディア ファイル](media-services-hyperlapse-content.md)
	- **動作の検出** - [Azure Media Analytics での動作検出](media-services-motion-detection.md)
	- **顔の検出と顔の感情** - [Azure Media Analytics での顔および感情の検出](media-services-face-and-emotion-detection.md)
	- **ビデオ要約** - [Azure Media Video Thumbnails を使用してビデオ要約を作成する](media-services-video-summarization.md)
3. Media Analytics のメディア プロセッサによって MP4 ファイルまたは JSON ファイルが生成されます。メディア プロセッサによって MP4 ファイルが生成された場合は、そのファイルのプログレッシブ ダウンロードが可能です。メディア プロセッサによって JSON ファイルが生成された場合は、そのファイルを Azure Blob Storage からダウンロードできます。 


###プログレッシブ ダウンロードの提供 

1. 高品質な中間ファイルを資産にアップロードします。
1. 単一 MP4 ファイルにエンコードする。
1. OnDemand または SAS ロケーターを作成して資産を発行します。

	OnDemand ロケーターを使用する場合、コンテンツを徐々にダウンロードするストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットがあることをご確認ください。

	SAS ロケーターを使用する場合、コンテンツは Azure BLOB ストレージからダウンロードされます。この場合、ストリーミング予約ユニットは必要ありません。
  
1. コンテンツを徐々にダウンロードします。

###関連トピック

- [コンテンツのアップロード方法](media-services-manage-content.md#upload)
- [メディア プロセッサの取得方法](media-services-get-media-processor.md)
- [コンテンツのエンコード方法](media-services-manage-content.md#encode)
- [ジョブの監視方法](media-services-portal-check-job-progress.md)
- [Media Analytics の使用方法](media-services-analytics-overview.md)
- [コンテンツの保護方法](media-services-manage-content.md#encrypt)
- [公開の保護方法](media-services-manage-content.md#publish)
- [エンコードの規模の設定方法](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>Azure Media Services を使用してライブ ストリーミング イベントを配信する

ライブ ストリーミングを使用する場合は、通常、次のコンポーネントが関連しています。

- イベントのブロードキャストに使用されるカメラ。
- カメラからの信号をライブ ストリーミング サービスに送信されるストリームに変換するライブ ビデオ エンコーダー。 
  
	必要に応じて、複数のライブ エンコーダー。非常に高い可用性と高品質が要求される重要なライブ イベントでは、アクティブ/アクティブの冗長エンコーダーを使用して、データを失わないシームレスなフェールオーバーを実現することをお勧めします。
- 次の操作を実行できるライブ ストリーミング サービス。 
	- さまざまなライブ ストリーミング プロトコル (RTMP、スムーズ ストリーミングなど) を使用してライブ コンテンツを取り込む。 
	- ストリームをアダプティブ ビットレート ストリームにエンコードする。
	- ライブ ストリームをプレビューする。
	- 後でストリーミングするために、取り込んだコンテンツを保存する (ビデオ オン デマンド)。
	- コンテンツを一般的なストリーミング プロトコル (MPEG DASH、Smooth、HLS、HDS など) を使用して顧客に配信したり、再配布のための Content Delivery Network (CDN) に直接配信する。 
	
		
**Microsoft Azure Media Services** (AMS) は、ライブ ストリーミング コンテンツの取り込み、エンコード、プレビュー、保存、配信を行う機能を提供します。

コンテンツを顧客に配信する場合、その目標は、異なるネットワーク条件におけるさまざまなデバイスに高品質のビデオを配信することにあります。品質とネットワーク条件に対応するには、ライブ エンコーダーを使用して、ストリームをマルチビットレート (アダプティブ ビットレート) ビデオ ストリームにエンコードします。異なるデバイスでのストリーミングに対応するには、Media Services の[動的パッケージ](media-services-dynamic-packaging-overview.md)を使用して、ストリームをさまざまなプロトコルに動的に再パッケージ化します。Media Services で配信がサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Azure Media Services、**チャネル**、**プログラム**、**ストリーミング エンドポイント**で、インジェスト、書式設定、DVR、セキュリティ、スケーラビリティ、冗長性などのすべてのライブ ストリーミングの機能を処理します。

**チャネル**は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。現時点では、チャネルは次の方法でライブ入力ストリームを受信できます。


- オンプレミスのライブ エンコーダーは、RTP (MPEG-TS)、RTMP、スムーズ ストリーミング (Fragmented MP4) のいずれかの形式で、シングル ビットレート ストリームを Media Services による Live Encoding が有効なチャネルに送信します。次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードがチャネルで実行されます。Media Services は、要求に応じて、ストリームを顧客に配信します。

	Media Services でのライブ ストリームのエンコードは、**プレビュー**になっています。
- オンプレミスのライブ エンコーダーは、マルチ ビットレート **RTMP** または **スムーズ ストリーミング** (Fragmented MP4) をチャネルに送信します。マルチ ビットレートのスムーズ ストリーミングを出力するライブ エンコーダーとして、Elemental、Envivio、Cisco を使用できます。Adobe Flash Live、Telestream Wirecast、Tricaster トランスコーダーは、RTMP を出力するライブ エンコーダーです。取り込んだストリームは、追加の処理なしで**チャネル**を通過します。ライブ エンコーダーは、ライブ エンコードが有効になっていないチャネルにシングル ビットレート ストリームも送信できますが、これはお勧めしません。Media Services は、要求に応じて、ストリームを顧客に配信します。


###Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する


次の図は、Media Services による Live Encoding の実行が有効なチャネルのライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー][live-overview1]

詳細については、「[Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。


###オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する


次の図は、ライブ ストリーミング ワークフローに関連する AMS プラットフォームの主要な部分を示しています。

![ライブ ワークフロー][live-overview2]

詳細については、「[オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する](media-services-manage-channels-overview.md)」をご覧ください。

##コンテンツの使用

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。次のトピックには、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる、SDK とプレーヤー フレームワークへのリンクがあります。

[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)

##Azure CDN を有効にする

Media Services では、Azure CDN との統合をサポートしています。Azure CDN を有効にする方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)」をご覧ください。

##Media Services アカウントのスケーリング

**Media Services** は、アカウントのプロビジョニングに使用する **[ストリーミング占有ユニット]** と **[エンコード占有ユニット]** の数を指定することで規模を設定できます。

ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。各ストレージ アカウントの上限は 500 TB (テラバイト) です。既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一の Media Services アカウントにアタッチすることを選択できます。

[この](media-services-how-to-scale.md)トピックは、関連するトピックにリンクしています。

##サポート

[Azure サポート](https://azure.microsoft.com/support/options/)では、Media Services を含む Azure のオプションについてサポートを提供しています。

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##サービス レベル アグリーメント (SLA)

- Media Services Encoding では、REST API トランザクションの可用性が 99.9% 保証されます。
- ストリーミングでは、1 つ以上のストリーミング ユニットが購入された場合、既存のメディア コンテンツに対して 99.9% の可用性で要求が適切に処理されます。
- ライブ チャンネルでは、実行中のチャンネルが 99.9% 以上の時間において外部に接続されることが保証されます。
- Content Protection では、99.9% 以上の時間においてキー要求が適切に処理されることが保証されます。
- インデクサーでは、エンコード予約ユニットの 99.9% の時間においてインデクサー タスクの要求が適切に処理されます。

	詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」参照してください。

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0427_2016-->