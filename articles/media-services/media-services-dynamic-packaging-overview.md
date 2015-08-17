<properties 
	pageTitle="動的パッケージの概要" 
	description="このトピックでは、動的パッケージの概要について説明します。" 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#動的パッケージ 

##概要

Microsoft Azure Media Services を使用して、多数のメディア ソース ファイルの形式、メディア ストリーミング形式、およびコンテンツ保護形式をさまざまなクライアント テクノロジ (iOS、XBOX、Silverlight、Windows 8 など) に配信できます。これらのクライアントは異なるプロトコルを認識します。たとえば、iOS は HTTP Live Streaming (HLS) V4 形式が必要で、Silverlight および Xbox はスムーズ ストリーミングが必要です。MPEG DASH、HLS またはスムーズ ストリーミングを認識するクライアントに提供する、一連のアダプティブ ビットレート (マルチビット レート) MP4 (ISO Base Media 14496-12) ファイルまたは一連のアダプティブ ビットレート スムーズ ストリーミング ファイルがある場合、Media Services の動的パッケージを活用する必要があります。

動的パッケージを使用すれば、一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルを含むアセットを作成するだけで済みます。そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

次の図は、従来のエンコードおよび静的パッケージングのワークフローを示しています。

![静的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

次の図は、動的パッケージのワークフローを示しています。

![動的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]動的パッケージを活用するには、コンテンツの配信元となるストリーミング エンドポイントのオンデマンド ストリーミング ユニットを 1 つ以上取得する 必要があります。詳細については、「[Media Services の規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」を参照してください。

##一般的なシナリオ

1. 入力ファイル (中間ファイル) をアセットにアップロードします。たとえば、H.264、MP4、または WMV などです (サポートされている形式の一覧については、「Media Services Encoder でサポートされる形式の一覧」を参照)。
 
1. Mezzanine ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードします。
 
1. オンデマンド ロケーターを作成することによって、アダプティブ ビットレート MP4 セットを含むアセットを発行します。
 
1. コンテンツにアクセスしてストリーミングするストリーミング URL を構築します。
 
>[AZURE.NOTE]すべての MP4 ファイルの形式が動的パッケージによってサポートされているわけではありません。詳細については、「[動的パッケージでサポートされていない形式](media-services-dynamic-packaging-overview.md#unsupported_formats)」を参照してください。

##動的ストリーミング用のアセットの準備

動的ストリーミング用にアセットを準備するには、2 つのオプションがあります。

- マスター ファイルをアップロードし、Azure Media Encoder を使用して H.264 MP4 アダプティブ ビットレートのセットを生成します。
- 既存のアダプティブ ビットレート セットをアップロードし、Media Packager を使用してそれらを検証します。

###マスター ファイルをアップロードし、Azure Media Encoder を使用して H.264 MP4 アダプティブ ビットレートのセットを生成します。

アセットをアップロードしてエンコードする方法については、次の記事を参照してください。


**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、ファイルをアップロードします。

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、**Azure Media Encoder** でエンコードします。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###既存のアダプティブ ビットレート セットをアップロードし、Media Packager を使用してそれらを検証します。

Media Services Encoder でエンコードされていないアダプティブ ビットレート MP4 ファイルのセットをアップロードする場合、通常はこのタスクを実行します。「[外部エンコーダーを使用してエンコードされたアダプティブ ビットレート MP4 を検証する](https://msdn.microsoft.com/library/azure/dn750842.aspx)」トピックでは、このタスクを実現する方法を示しています。

##コンテンツをクライアントにストリーミングする

アダプティブ ビットレート セットを作成したら、オンデマンド ロケーターを作成して、スムーズ ストリーミング、MPEG DASH、HLS、および HDS (Adobe Primetime/Access のライセンス所有者にのみ使用) のためのストリーミング URL を構成できます。

ロケーターを作成し、動的パッケージを使用してコンテンツをストリームする方法については、次のトピックを参照してください。

[顧客へのコンテンツの配信に関する概要](media-services-deliver-content-overview.md)。

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

**Azure の管理ポータル**または **.NET** を使用してアセットを公開します (ロケータを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>動的パッケージでサポートされない形式

動的パッケージでは、次のソース ファイルの形式はサポートされません。

- Dolby Digital Plus の MP4 ファイル。
- Dolby Digital Plus のスムーズ ファイル。 

<!---HONumber=August15_HO6-->