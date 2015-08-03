<properties 
	pageTitle="Azure Media Services の概要" 
	description="このトピックでは、Azure Media Services の概要を説明します。" 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Azure Media Services の概要

Microsoft Azure Media Services は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。Media Services は、オンデマンド、また各種クライアント (TV、PC、モバイル デバイスなど) へのライブ ストリーム配信の両方でのビデオやオーディオの安全なアップロード、格納、エンコード、パッケージ化を可能にする REST API に基づいています。

Media Services を使いこなして、エンド ツー エンドのワークフローを構築できます。ワークフローの一部にサード パーティのコンポーネントを使用することもできます。たとえば、サード パーティのエンコーダーを使用してエンコードしてから、Media Services を使用してアップロード、保護、パッケージ化、配信などを行うことができます。

Media Services ソリューションを構築するために、以下を使用できます。

- [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 次の使用可能なクライアント SDK のうちのいずれか: [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media)、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 既存のツール: [Microsoft Azure 管理ポータル](http://manage.windowsazure.com/) または [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)。


次のポスターは、メディアの作成から使用までの Azure Media Services のワークフローを示しています。ポスターは「[Azure Media Services poster](http://www.microsoft.com/download/details.aspx?id=38195)」(Azure Media Services のポスター) からダウンロードできます。

![概要][overview]

**サービス レベル アグリーメント (SLA)**:

- Media Services のエンコードでは、REST API トランザクションの可用性が 99.9% 保証されます。
- ストリーミングでは、1 つ以上のストリーミング ユニットが購入された場合、既存のメディア コンテンツに対して 99.9% の可用性で要求が適切に処理されます。
- ライブ チャンネルでは、実行中のチャンネルが 99.9% 以上の時間において外部に接続されることが保証されます。
- コンテンツ保護では、99.9% 以上の時間においてキー要求が適切に処理されることが保証されます。
- インデクサーでは、エンコード予約ユニットの 99.9% の時間においてインデクサー タスクの要求が適切に処理されます。

	詳細については、「[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)」参照してください。

##概念

詳細については、「[概念](media-services-concepts.md)」を参照してください。


##Azure Media Services を使用したメディアのオンデマンド配信

次のトピックでは、一般的な Media Services ビデオ オンデマンドのワークフローの手順について説明します。このトピックには、Media Services でサポートされるテクノロジを使用してこれらの手順を実行する方法を示す他のトピックへのリンクがあります。

[Azure Media Services を使用したメディアのオンデマンド配信](media-services-video-on-demand-workflow.md)。

##Azure Media Services を使用したライブ ストリーミング配信

次のトピックでは、一般的な Media Services ライブ ストリーミングのワークフローの手順について説明します。このトピックには、Media Services でサポートされるテクノロジを使用してこれらの手順を実行する方法を示す他のトピックへのリンクがあります。

[Azure Media Services を使用したライブ ストリーミング配信](media-services-live-streaming-workflow.md)。

##コンテンツの使用

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。次のトピックには、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる、SDK とプレーヤー フレームワークへのリンクがあります。

[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)

##パターンとプラクティスのガイダンス

[パターンとプラクティスのガイダンス](https://wamsg.codeplex.com/) [オンライン ドキュメント](https://msdn.microsoft.com/library/dn735912.aspx) [ダウンロード可能な電子ブック](https://www.microsoft.com/download/details.aspx?id=42629)

##サポート

[Azure サポート](http://azure.microsoft.com/support/options/)では、Media Services を含む Azure のオプションについてサポートを提供しています。

##次のステップ

[Azure Media Services を使用したライブ ストリーミング配信](media-services-live-streaming-workflow.md)

[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)
 
[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO4-->