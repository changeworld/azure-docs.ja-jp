<properties 
	pageTitle="ビデオ プレーヤー アプリケーションの開発" 
	description="このトピックでは、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できるプレーヤー フレームワークとプラグインへのリンクも提供します。" 
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


#ビデオ プレーヤー アプリケーションの開発

##概要

Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。このトピックでは、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる SDK とプレーヤー フレームワークへのリンクもご紹介します。

##既存のプレーヤーを使用してコンテンツを再生する

詳細については、「[既存のプレーヤーを使用したコンテンツの再生](media-services-playback-content-with-existing-players.md).」に関するトピックをご覧ください

##Azure Media Player


[Azure Media Player](aka.ms/ampdocs) は、さまざまなブラウザーやデバイス上で Microsoft Azure Media Services からメディア コンテンツを再生できる組み込みの Web ビデオ プレーヤーです。Azure Media Seria Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。使用する再生テクノロジにかかわらず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。これにより、Azure Media Services で提供されるコンテンツを、さまざまなデバイスやブラウザーで追加作業なしで再生できるようになります。

Microsoft Azure Media Services では、DASH、スムーズ ストリーミング、HLS ストリーミングなどの形式でコンテンツを提供して再生することができます。Azure Media Player はこれらの多用な形式を考慮し、プラットフォームやブラウザーの性能に基づいて最適なリンクを自動的に再生します。また、Microsoft Azure Media Services では PlayReady 暗号化や AES-128 ビットエンベロープ暗号化を使用したアセットの動的暗号化も可能です。Azure Media Player では、コンテンツが適切に構成されていれば、PlayReady 暗号化や AES-128 ビットエンベロープ暗号化されたコンテンツの暗号化を解除することができます。 

詳細については、「[Azure Media Player](aka.ms/ampdocs)」をご覧ください。


##プレーヤー アプリケーションを作成するためのその他のツール

次のような SDK を使用することもできます。

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media Platform:Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 プレーヤー フレームワークに関するドキュメント](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [OSMF 用 Microsoft Smooth Streaming Plugin](https://www.microsoft.com/download/details.aspx?id=36057) 
- [iOS 用 Media Player Framework](https://github.com/Azure/azure-media-player-framework) 
- [Microsoft(r) Smooth Streaming Client Porting Kit のライセンスについて](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- [XBOX ビデオ アプリケーションの開発](http://xbox.create.msdn.com/) 

詳細については、「[ビデオ プレーヤー アプリケーションの開発](https://msdn.microsoft.com/library/dn223283.aspx)」をご覧ください。

##広告

Azure Media Services では、Windows メディア プラットフォームのプレーヤー フレームワークを通じて広告の挿入がサポートされています。広告がサポートされるプレーヤー フレームワークは、Windows 8、Silverlight、Windows Phone 8、iOS デバイスで使用できます。各プレーヤー フレームワークには、プレーヤー アプリケーションの実装方法を示すサンプル コードが含まれています。メディアには、3 種類の広告を挿入できます。

線形 - メイン ビデオを一時停止するフル フレーム広告

非線形 - メイン ビデオの再生中に表示されるオーバーレイ広告 (通常は、プレーヤー内に配置されるロゴや他の静的イメージ)

コンパニオン - プレーヤーの外部に表示される広告

広告は、メイン ビデオの時系列のどのポイントにも配置できます。広告をいつ再生し、どの広告を再生するかをプレーヤーに通知する必要があります。これは、一連の標準 XML ベース ファイルである Video Ad Service Template (VAST)、Digital Video Multiple Ad Playlist (VMAP)、Media Abstract Sequencing Template (MAST)、Digital Video Player Ad Interface Definition (VPAID) を使用して行います。VAST ファイルは、表示する広告を指定します。VMAP ファイルは、各広告をいつ再生するかを指定します。このファイルには VAST XML が含まれています。MAST ファイルを使用して広告の順序を指定することもできます。このファイルにも VAST XML が含まれています。VPAID ファイルは、ビデオ プレーヤーと広告や広告サーバーの間のインターフェイスを定義します。詳細については、「[広告の挿入](https://msdn.microsoft.com/library/dn387398.aspx)」をご覧ください。

ライブ ストリーミング ビデオでのクローズド キャプションと広告のサポートの詳細については、「[サポートされる字幕と広告挿入の標準」](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)」をご覧ください。


<!--HONumber=52-->