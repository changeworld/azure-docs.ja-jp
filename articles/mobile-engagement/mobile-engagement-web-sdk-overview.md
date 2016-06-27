<properties
	pageTitle="Azure Mobile Engagement Web SDK の概要 | Microsoft Azure"
	description="Web SDK for Azure Mobile Engagement の最新の情報と手順"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Web SDK for Azure Mobile Engagement

Web アプリに Azure Mobile Engagement を統合する方法について詳しく説明したページには、こちらからアクセスできます。まず試してみる場合は、「[15 分間チュートリアル](mobile-engagement-web-app-get-started.md)」をご覧ください

## 統合手順
1. ここから開始: [Web アプリに Mobile Engagement を統合する方法](mobile-engagement-web-integrate-engagement.md)

2. タグ付けに関する計画の実行: [Mobile Engagement の高度なタグ付け API を Web アプリケーションから使用する方法](mobile-engagement-web-use-engagement-api.md)

## リリース ノート

### 2\.0.1 (06/10/2016)

-   IE8 と IE9 で SDK を無効にしました。
-   Opera Web ブラウザーの検出を修正しました。

すべてのバージョンについては、[完全なリリース ノート](mobile-engagement-web-release-notes.md)をご覧ください。

## アップグレードの手順

### 1\.2.1 から 2.0.0 へ

Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。以前のバージョンから移行する場合は、Capptain Web サイトをご覧のうえ、まず 1.2.1 に移行し、次の手順を適用してください。

このバージョンの Engagement Web SDK では、samsung-tv、OperaTV、webOS、Reach の機能がサポートされません。

>[AZURE.IMPORTANT] Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

#### JavaScript ファイル

`capptain-sdk.js` ファイルを `azure-engagement.js` ファイルに置き換え、スクリプトのインポートを適宜更新します。

#### Capptain Reach の削除

このバージョンの Engagement Web SDK では、Reach の機能がサポートされません。アプリケーションに Capptain Reach を統合した場合は、Reach を削除する必要があります。

Reach css のインポートをページから削除し、関連する css ファイル (既定では capptain-reach.css) を削除します。

Reach のリソースを削除します。終了画像 (既定では capptain-close.png) とブランド アイコン (既定では capptain-notification-icon) を削除してください。

アプリ内通知に使用される Reach UI を削除します。既定では、次のようなレイアウトになっています。

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

テキスト/Web のアナウンスと投票に使用される Reach UI を削除します。既定では、次のようなレイアウトになっています。

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

`reach` オブジェクトを構成から削除します (存在する場合)。次に例を示します。

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

その他 Reach 関連のカスタマイズ (カテゴリなど) を行っている場合は、それらをすべて削除します。

#### 非推奨 API の削除

Engagement バージョンの SDK では、Capptain のいくつかの API が非推奨となっています。

`agent.connect`、`agent.disconnect`、`agent.pause`、`agent.sendMessageToDevice` の API を呼び出している場合は、それらをすべて削除します。

Capptain の構成から `onConnected`、`onDisconnected`、`onDeviceMessageReceived`、`onPushMessageReceived` のコールバックを削除します (存在する場合)。

#### 構成

Engagement では、接続文字列を使用してアプリケーション ID などの SDK の識別子を構成します。

アプリケーション ID を接続文字列に置き換えます。SDK の構成に使用するグローバル オブジェクトが `capptain` から `azureEngagement` に移されている点にも注意してください。

移行前:

	window.capptain = {
	  appId: ...,
	  [...]
	};

移行後:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

アプリケーションの接続文字列が Azure ポータルに表示されます。

#### JavaScript API

グローバル JavaScript オブジェクト `window.capptain` の名前は `window.azureEngagement` に変更しましたが、API 呼び出しには `window.engagement` エイリアスを使用できます (このエイリアスを使用して SDK の構成を定義することはできません)。

たとえば `capptain.deviceId` は `engagement.deviceId` に、`capptain.agent.startActivity` は `engagement.agent.startActivity` になります。

SDK の旧バージョンをアプリケーションに既に統合している場合、[アップグレード手順](mobile-engagement-web-upgrade-procedure.md)を参照してください。

<!---HONumber=AcomDC_0615_2016-->