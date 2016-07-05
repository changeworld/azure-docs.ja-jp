<properties 
	pageTitle="Licensing Microsoft® Smooth Streaming Client Porting Kit" 
	description="Microsoft® Smooth Streaming Client Porting Kit のライセンスを取得する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat,vsood" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/21/2016"  
	ms.author="xpouyat"/>

#Licensing Microsoft® Smooth Streaming Client Porting Kit

##概要

Microsoft Smooth Streaming Client Porting Kit (**SSPK**) は、組み込みデバイスのメーカーやケーブル テレビ事業者、携帯電話会社、コンテンツ サービス プロバイダー、携帯電話端末メーカー、独立系ソフトウェア ベンダー (ISV)、ソリューション プロバイダーが、アダプティブ ストリーミング コンテンツをスムーズ ストリーミング形式で配信するための製品やサービスを構築できるように最適化されたスムーズ ストリーミング クライアントの実装です。SSPK は、デバイスやプラットフォームに依存しないスムーズ ストリーミング クライアントの実装であり、ライセンシーは任意のデバイスやプラットフォームに移植できます。

以下の図は全体的なアーキテクチャを示しています。IIS Smooth Streaming Porting Kit ボックスは、マイクロソフトの実装によるスムーズ ストリーミング クライアントで、スムーズ ストリーミング コンテンツの再生に必要なコア ロジックをすべて備えています。パートナー企業は、適切なインターフェイスを実装することによって、これを特定のデバイスやプラットフォーム向けに移植することができます。

![SSPK](./media/media-services-sspk/sspk-arch.png)

##説明

SSPK は、高い事業価値を提供する契約条件に基づいて使用許諾されます。SSPK のライセンスが業界にもたらす利点を次に示します。

- Smooth Streaming Porting Kit ソース (C++) 
  - スムーズ ストリーミング クライアントの機能を実装
  - フォーマット解析、ヒューリスティック、バッファリング ロジックなどを追加
- プレーヤー アプリケーションの API 
  -	メディア プレーヤー アプリケーションとの連携を可能にするプログラミング インターフェイス
- プラットフォーム アブストラクション レイヤー (PAL) インターフェイス 
  -	オペレーティング システム (スレッド、ソケット) との連携を可能にするプログラミング インターフェイス
- ハードウェア アブストラクション レイヤー (HAL) インターフェイス 
  -	ハードウェア A/V デコーダー (デコード、レンダリング) との連携を可能にするプログラミング インターフェイス
- DRM (デジタル著作権管理) インターフェイス 
  -	DRM アブストラクション レイヤー (DAL) を介して DRM を処理するためのプログラミング インターフェイス
  -	Microsoft PlayReady Porting Kit は別々に出荷されますが、このインターフェイスを介して連携します。Microsoft PlayReady デバイスのライセンスの詳細については、[ここ](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)をクリックしてください。
- 実装サンプル 
  -	サンプル PAL 実装 (Linux)
  -	サンプル HAL 実装 (GStreamer)

##ライセンス オプション

Microsoft Smooth Streaming Client Porting Kit は、2 種類のライセンス契約で使用許諾されます。スムーズ ストリーミング クライアントの中間製品を開発するための契約と、スムーズ ストリーミング クライアントの最終製品をエンド ユーザーに配布するための契約です。
 
- チップセット メーカー、システム インテグレーター、独立系ソフトウェア ベンダー (ISV) で中間製品開発のためのソース コード移植キットが必要な場合、Microsoft Smooth Streaming Client Porting Kit の**中間製品ライセンス**契約に署名する必要があります。
- スムーズ ストリーミング クライアントの最終製品をエンド ユーザーに配布するための権利を必要とするデバイス メーカーまたは ISV は、Microsoft Smooth Streaming Client Porting Kit の**最終製品ライセンス**契約に署名する必要があります。

###Microsoft Smooth Streaming Client Porting Kit 中間製品ライセンス

このライセンスでは、Smooth Streaming Client Porting Kit に加え、スムーズ ストリーミング クライアント中間製品を開発して他のライセンシー (つまり、スムーズ ストリーミング クライアントの最終製品を販売する Smooth Streaming Client Porting Kit デバイスのライセンシー) に販売するために必要な知的財産権がマイクロソフトから提供されます。

####料金体系

Smooth Streaming Client Porting Kit の利用には、1 回限りのライセンス使用料 50,000 米ドルがかかります。

###Microsoft Smooth Streaming Client Porting Kit 最終製品ライセンス

このライセンスでは、スムーズ ストリーミング クライアントの中間製品を他の Smooth Streaming Client Porting Kit ライセンシーから受け取り、その最終製品を自社ブランドとしてエンド ユーザーに販売するために必要なすべての知的財産権がマイクロソフトから提供されます。

####料金体系

スムーズ ストリーミング クライアントの最終製品は、ロイヤリティ モデルの下、次の条件で提供されます。

- 出荷されるデバイス実装あたり 0.10 ドル
- ロイヤリティの上限は年 50,000 ドル
- 毎年最初の 10,000 デバイス実装についてはロイヤリティなし 

##ライセンス取得の手順と SSPK の利用

ライセンスに関するお問い合わせは、[sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) 宛てに電子メールでお寄せください。

中間製品契約の登録済みライセンシーは、[SSPK Distribution ポータル](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/)にアクセスできます。

中間製品契約と最終製品契約を締結済みの SSPK ライセンシーは、技術的な質問を [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) までお問い合わせいただけます。

##Microsoft Smooth Streaming Client 中間製品契約ライセンシー

- Adroit Business Solutions, Inc
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- AVC Multimedia Software Co., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Fluendo S.A.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Irdeto USA Inc.
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- SoftAtHome
- Sony Corporation
- Tatung Technology Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##Microsoft Smooth Streaming Client 最終製品契約ライセンシー

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN.VE TİC.A.Ş
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Filmflex Movies Limited
- Fluendo S.A.
- Gibson Innovations Limited
- HANDAN BroadInfoCom Co., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange SA
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co.,Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- Skardin Industrial Corp.
- Sky Deutschland Fernsehen GmbH & Co. KG
- SmarDTV S.A.
- SoftAtHome
- Sony Corporation
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- Toshiba Lifestyle Products & Services Corporation
- Universal Media Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0622_2016-->