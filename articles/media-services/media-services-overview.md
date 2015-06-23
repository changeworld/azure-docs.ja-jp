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
	ms.date="02/26/2015" 
	ms.author="juliako"/>

# Azure Media Services の概要

Microsoft Azure Media Services は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。Media Services は、オンデマンド、また各種クライアント (TV、PC、モバイル デバイスなど) へのライブ ストリーム配信の両方でのビデオやオーディオの安全なアップロード、格納、エンコード、パッケージ化を可能にする REST API に基づいています。

Media Services を使いこなして、エンド ツー エンドのワークフローを構築できます。ワークフローの一部にサード パーティのコンポーネントを使用することもできます。たとえば、サード パーティのエンコーダーを使用してエンコードしてから、Media Services を使用してアップロード、保護、パッケージ化、配信などを行うことができます。

Media Services ソリューションを構築するために、以下を使用できます。

- [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 使用可能な次のいずれかの SDK:[Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media)、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 既存のツール:[Azure 管理ポータル](http://manage.windowsazure.com/) または [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**サービス レベル アグリーメント (SLA)**: Media Services では、Media Services エンコードの REST API の 99.9% の可能性が保証されます。オンデマンド ストリーミングでは、1 つ以上のストリーミング占有ユニットが購入された場合、既存のメディア コンテンツに対して 99.9% の可用性で要求が適切に対処されます。可用性は、1 か月の請求サイクルに対して計算されます。詳細については、[SLA に関するドキュメント](https://www.microsoft.com/download/details.aspx?id=39302)をご覧ください。

次のポスターは、メディアの作成から使用までの Azure Media Services のワークフローを示しています。ポスターは次の場所からダウンロードできます。[Azure Media Services ポスター](http://www.microsoft.com/download/details.aspx?id=38195).

![概要][概要]

## 概念

詳細については、「[概念](media-services-concepts.md)」をご覧ください。

## メディア機能の選択

ビデオ コンテンツを共有する際の最初の手順の 1 つは、クライアントが必要な機能の種類を特定することです。顧客はビデオ コンテンツをどのように表示しますか。彼らはインターネット接続を使用しますか。コンテンツをコンピューターまたはハンドヘルド デバイスに表示しますか。顧客はビデオが HD に含まれていることを望んでいますか。このような質問は、顧客に最良のエクスペリエンスを提供するのに役立ちます。

### ビデオへのアクセス
 
顧客は、次の 4 つの基本的な方法を使用してビデオにアクセスできます。

- オフライン表示 
- プログレッシブ ダウンロード
- ストリーミング/アダプティブ ビットレート ストリーミング

#### オフライン表示

ビデオをオフラインで表示する場合、顧客はビデオ全体を使用しているコンピューターまたはデバイスにダウンロードします。通常、ビデオは容量が非常に大きいため、ダウンロードが完了するまでに時間ががかル場合があります。オフライン表示の利点は、デバイスに一度ダウンロードすれば、ビデオを表示するためにネットワークに接続する必要がなくなる点です。 

#### プログレッシブ ダウンロード

プログレッシブ ダウンロードでは、顧客はインターネットに接続し、ビデオ全体がダウンロードされる前に、ビデオの表示を開始できる必要があります。オフライン表示とプログレッシブ ダウンロードのいずれの方法も、顧客がビデオの表示に使用するデバイスには、ビデオ全体を格納できるだけの十分な容量が必要です。

#### ストリーミング

ストリーミング技術でもインターネット接続が必要ですが、一度にビデオの一部がダウンロードされ、表示されると破棄されます。表示するデバイスでは非常に少量のストレージ領域しか必要ありません。ネットワーク接続のスループットは一定でない場合がありますが、顧客はネットワーク帯域幅がなくてもビデオを表示できます。アダプティブ ビットレート テクノロジでは、ビデオ再生アプリケージョンでネットワークの状態を特定し、複数のビットレートの中から選択できます。ネットワーク通信のパフォーマンスが低下した場合、クライアントはそれより低いビットレートを選択して、より低い画質レベルでビデオの再生をプレーヤーで継続できます。ネットワークの状態が改善したら、顧客はより高画質なより高いビットレートに切り替えることができます。Azure Media Services は、次のアダプティブ ビットレート テクノロジをサポートします。HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS。

### デバイスの選択

他の決定事項として、顧客がビデオの表示に使用するデバイスの種類を特定する必要があります。Media Services は、Web ブラウザー、スマート フォン、タブレット、Xbox、セットトップ ボックス、接続型 TV をサポートします。

#### Web ブラウザー

Web ブラウザーは、Windows PC、Macintosh PC、スマート フォンで実行できます。PC または Macintosh PC で実行する場合、大画面と大容量を利用できます。これにより、より高画質なビデオをストリーミングできます。Windows PC または Macintosh PC では、Media Services により配信されるビデオを、ネイティブ アプリケーションまたは HTML 対応 Web ブラウザーを使用して表示できます。ネイティブ アプリケーションでは、スムーズ ストリーミング、Apple HLS、プログレッシブ ダウンロード、オフライン表示がサポートされています。HTML5 Web ページは、プログレッシブ ダウンロードをサポートします。


#### スマート フォン

スマート フォンの画面は小さく、ストレージ容量も多くありません。これらのデバイスではストリーミングの使用をお勧めします。iPhone、Windows Phone、Android フォンはこれに対応しています。iPhone および Android フォンでは、スムーズ ストリーミングと HLS を使用できます。Windows Phone では、スムーズ ストリーミングを使用できます。

### タブレット

タブレットの画面はスマート フォンよりも大型ですが、その容量はスマート フォンより小さい傾向があります。タブレットでもストリーミングの使用をお勧めします。より大きい容量を備えたタブレットでは、オフライン表示だけでなく、プログレッシブ ダウンロードも利用できます。

#### Xbox

Xbox コンソールは画面も容量も大きいため、オフライン表示、プログレッシブ ダウンロード、ストリーミングに最適です。
セットトップ ボックスと接続型 TV
これらのデバイスの画面は大型であっても、容量は最小限であるため、ストリーミングの使用が最も適しています。

### サポートされているテクノロジ (デバイス別)

次の表に、Media Services でサポートされた各デバイスの種類と顧客が使用するテクノロジを示します。
 
<table border="1">
<tr><th>デバイス</th><th>テクノロジ</th></tr>
<tr><td>iOS</td><td>スムーズ ストリーミング、Apple HLS、プログレッシブ ダウンロード</td></tr>
<tr><td>Windows Phone 8</td><td>スムーズ ストリーミング</td></tr>
<tr><td>Windows RT</td><td>スムーズ ストリーミング</td></tr>
<tr><td>Windows</td><td>スムーズ ストリーミング、プログレッシブ ダウンロード</td></tr>
<tr><td>Android フォン</td><td>スムーズ ストリーミング、Apple HLS</td></tr>
<tr><td>Xbox</td><td>スムーズ ストリーミング</td></tr>
<tr><td>Macintosh</td><td>Apple HLS、プログレッシブ ダウンロード</td></tr>
<tr><td>セットトップ ボックス、接続型 TV</td><td>スムーズ ストリーミング、Apple HLS、プログレッシブ ダウンロード</td></tr>
</table>


## Azure Media Services を使用したメディアのオンデマンド配信

詳細については、「[Azure Media Services を使用したメディアのオンデマンド配信](media-services-video-on-demand-workflow.md)」をご覧ください。

## Azure Media Services を使用したライブ ストリーミング配信

詳細については、「[Delivering Live Streaming with Azure Media Services (Azure Media Services を使用したライブ ストリーミング配信)](media-services-live-streaming-workflow.md)」をご覧ください。上記のタスクの実行方法を説明するトピックへのリンクが含まれています。

## クライアント

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming)
- [Microsoft Media Platform:Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 プレーヤー フレームワークに関するドキュメント](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation)
- [OSMF 用 Microsoft Smooth Streaming Plugin](https://www.microsoft.com/download/details.aspx?id=36057)
- [iOS 用 Media Player Framework](https://github.com/Azure/azure-media-player-framework)
- [Licensing Microsoft(r) Smooth Streaming Client Porting Kit](https://www.microsoft.com/mediaplatform/sspk.aspx)
- Windows 8 でのビデオ アプリケーションの構築 
- [Xbox ビデオ アプリケーションの開発](http://xbox.create.msdn.com/)

詳細については、「[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)」をご覧ください。

## パターンとプラクティスのガイダンス

[Patterns and practices guidance (パターンとプラクティスのガイダンス)](https://wamsg.codeplex.com/)
[オンライン ドキュメント](https://msdn.microsoft.com/library/dn735912.aspx)
[ダウンロード可能な電子ブック](https://www.microsoft.com/download/details.aspx?id=42629)

## サポート

[Azure サポート](http://azure.microsoft.com/support/options/)では、Media Services を含む Azure のオプションについてサポートを提供しています。



<!-- Images -->
[概要]: ./media/media-services-overview/media-services-overview.png


<!--HONumber=52--> 