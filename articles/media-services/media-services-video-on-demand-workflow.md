<properties 
	pageTitle="Azure Media Services を使用したメディアのオンデマンド配信"
	description="このトピックでは、Azure Media Services を使用したメディアのオンデマンド配信の一般的なシナリオについて説明します。"
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
	ms.date="08/11/2015"
	ms.author="juliako"/>


#Azure Media Services を使用したメディアのオンデマンド配信

##概要

このトピックでは、一般的な Azure Media Services (AMS) ビデオ オン デマンドのワークフローの手順を説明します。各手順は関連するトピックにリンクしています。さまざまなテクノロジを使用して達成できるタスクでは、任意のテクノロジへリンクするボタンがあります (たとえば、.NET または REST)。

Media Services は既存のツールとプロセスに統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にコード変換するために Media Services にアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。

次の図は、ビデオ オンデマンド ワークフローに関連する Media Services プラットフォームの主要な部分を示しています。![VoD ワークフロー][vod-overview]

##<a id="vod_scenarios"></a>一般的なシナリオ: メディアのオンデマンド配信

###ストレージのコンテンツを保護し、ストリーミング メディアを平文 (暗号化されていない) で配信する

1. 高品質な中間ファイルを資産にアップロードします。
	
	ストレージ暗号化オプションを資産に適用し、アップロード中のコンテンツとストレージ内のコンテンツを保護することをお勧めします。 
1. アダプティブ ビットレート MP4 セットにエンコードします。 

	ストレージ暗号化オプションを出力資産に適用し、保存されているコンテンツを保護することをお勧めします。
	
1. 資産配信ポリシーを構成します (動的パッケージで使用)。
	
	資産がストレージで暗号化されている場合は、資産配信ポリシーを構成する**必要があります**。

1. OnDemand ロケーターを作成して資産を発行します。

	コンテンツをストリームするストリーミング エンドポイントに少なくとも 1 つのストリーミング予約ユニットがあることを確認します。

1. 公開済みコンテンツをストリーミングします。

###ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する  

動的暗号化を使用するには、暗号化されたコンテンツのストリーミング元となるストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットが必要です。

1. 高品質な中間ファイルを資産にアップロードします。ストレージ暗号化オプションを資産に適用します。
1. アダプティブ ビットレート MP4 セットにエンコードします。ストレージ暗号化オプションを出力資産に適用します。
1. 再生中に動的に暗号化する資産の、暗号化コンテンツ キーを作成します。
2. コンテンツ キー承認ポリシーを構成します。
1. 資産配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
1. OnDemand ロケーターを作成して資産を発行します。
1. 公開済みコンテンツをストリーミングします。 

###コンテンツのインデックス作成

1. 高品質な中間ファイルを資産にアップロードします。
1. コンテンツのインデックスを作成します。

	インデックス作成ジョブは、ビデオの再生でクローズド キャプション (CC) として使用できるファイルを生成します。また、ビデオ内を検索したり、正確なビデオの位置へ移動したりできるようにするファイルを生成します。

1. インデックス付けされたコンテンツを使用します。


###プログレッシブ ダウンロードの提供 

1. 高品質な中間ファイルを資産にアップロードします。
1. アダプティブ ビットレート MP4 セットまたは単一の MP4 にエンコードします。
1. OnDemand または SAS ロケーターを作成して資産を発行します。

	OnDemand ロケーターを使用する場合、コンテンツを徐々にダウンロードするストリーミング エンドポイントに、少なくとも 1 つのストリーミング予約ユニットがあることをご確認ください。

	SAS ロケーターを使用する場合、コンテンツは Azure BLOB ストレージからダウンロードされます。この場合、ストリーミング予約ユニットは必要ありません。
  
1. コンテンツを徐々にダウンロードします。

この記事には、開発環境をセットアップする方法と、上記のタスクを実行する方法を説明するトピックへのリンクが含まれています。


##概念

オンデマンドのコンテンツ配信に関連する概念については、「[Media Services の概念](media-services-concepts.md)」をご覧ください。

##一般的なタスク: メディアのオンデマンド配信

###Media Services アカウントの作成

**Azure 管理ポータル**を使用して、[Azure Media Services アカウントを作成する](media-services-create-account.md)

###開発環境の設定  

開発環境に関して **.NET** または **REST API** を選択します。

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###プログラムによる接続  

プログラムを使用して Azure Media Services に接続するため、**.NET** または **REST API** を選択します。

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


###ストリーミング エンドポイントの構成

ストリーミング エンドポイントの概要とその管理方法の詳細については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md)」を参照してください。

###メディアのアップロード 

**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、ファイルをアップロードします。

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###ジョブまたはタスクの作成

ジョブは、一連のタスク (エンコード、インデックス作成など) に関するメタデータを含むエンティティです。各タスクでは、入力資産でアトミック操作が実行されます。エンコード ジョブの作成方法の例については、以下をご覧ください。

概要については、「[Working with Azure Media Services Jobs (Azure Media Services ジョブの操作)](media-services-jobs.md)」をご覧ください。

**.NET** または **REST API** を使用したタスクに適したメディア プロセッサを取得します。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

次の例では、**Azure 管理ポータル**、**.NET**、**REST API** を使用してエンコード ジョブを作成します。

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####インデックス作成

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

####エンコード 

**概要**:

- [動的パッケージの概要](media-services-dynamic-packaging-overview.md)
- [Azure Media Services を使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md)

**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、**Azure Media Encoder** でエンコードします。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

**.NET** を使用した**Media Encoder Premium ワークフロー**での高度なエンコード

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

####ジョブの進行状況の監視

**Azure 管理ポータル**、**.NET**、**REST API** を使用してジョブの進行状況を監視するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

###コンテンツの保護 

**概要**:

[Content Protection の概要](media-services-content-protection-overview.md)

高度暗号化標準 (Advanced Encryption Standard: AES) (128 ビット暗号化キーを使用) または PlayReady DRM を使用して資産を暗号化する場合は、コンテンツ キーを作成する必要があります。

キーを作成するために **.NET** または **REST API** を使用します。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

コンテンツ キーを作成すると、**.NET** または **REST API** を使用してキー承認ポリシーを構成できます。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


**.NET** または **REST API** を使用して資産配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


####パートナーとの統合

[castLabs を使用して Azure Media Services に DRM ライセンスを配信する](media-services-castlabs-integration.md)

###資産の公開と配信

動的パッケージの概要

> [AZURE.SELECTOR]
- [Overview](media-services-dynamic-packaging-overview.md)


コンテンツの配信の概要

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

**Azure 管理ポータル**、**.NET**、または **REST API** を使用して資産を発行します (ロケーターを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

###Azure CDN を有効にする

Media Services では、Azure CDN との統合をサポートしています。Azure CDN を有効にする方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)」をご覧ください。

###Media Services アカウントのスケーリング

**Media Services** は、アカウントのプロビジョニングに使用する **[ストリーミング予約ユニット]** と **[エンコード予約ユニット]** の数を指定することで規模を設定できます。

ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。各ストレージ アカウントの上限は 500 TB です。既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一の Media Services アカウントにアタッチすることを選択できます。

[この](media-services-how-to-scale.md)トピックは、関連するトピックにリンクしています。

###既存のプレーヤーを使用してコンテンツを再生する

詳細については、[既存のプレーヤーを使用したコンテンツの再生](media-services-playback-content-with-existing-players.md)に関するトピックをご覧ください


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=August15_HO9-->