<properties
	pageTitle="Azure Mobile Engagement Web SDK の概要 | Microsoft Azure"
	description="Web SDK for Azure Mobile Engagement の最新の更新プログラムと手順"
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


# Azure Mobile Engagement Web SDK

Web アプリに Azure Mobile Engagement を統合する方法について詳しく説明したページには、ここからアクセスできます。独自の Web アプリで作業を開始する前に試してみる場合は、[15 分間チュートリアル](mobile-engagement-web-app-get-started.md)をご覧ください。

## 統合手順
1. [Web アプリに Mobile Engagement を統合する方法](mobile-engagement-web-integrate-engagement.md)を確認します。

2. タグ付けプランの実装について、[Web アプリで Mobile Engagement の高度なタグ付け API を使用する方法](mobile-engagement-web-use-engagement-api.md)を確認します。

## リリース ノート

### 2\.0.1 (2016/6/10)

-   Internet Explorer 8 と Internet Explorer 9 で Mobile Engagement Web SDK が無効になりました。
-   Opera Web ブラウザーの検出が修正されました。

すべてのバージョンについては、[完全なリリース ノート](mobile-engagement-web-release-notes.md)をご覧ください。

## アップグレードの手順

### 1\.2.1 から 2.0.0 へのアップグレード

以下のセクションでは、Mobile Engagement Web SDK 統合を、Capptain SAS によって提供される Capptain サービスから、Azure Mobile Engagement アプリに移行する方法について説明します。1.2.1 より前のバージョンから移行する場合は、Capptain Web サイトをご覧のうえ、まず 1.2.1 に移行してから、以下の手順を適用してください。

このバージョンの Mobile Engagement Web SDK では、Samsung Smart TV、Opera TV、webOS、または Reach の機能がサポートされません。

>[AZURE.IMPORTANT] Capptain と Azure Mobile Engagement は、同じサービスではありません。以下の手順では、クライアント アプリを移行する方法だけを説明します。アプリで Mobile Engagement Web SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

#### JavaScript ファイル

capptain-sdk.js ファイルを azure-engagement.js ファイルに置き換え、スクリプトのインポートを適宜更新します。

#### Capptain Reach の削除

このバージョンの Mobile Engagement Web SDK では、Reach の機能がサポートされません。アプリケーションに Capptain Reach を統合している場合は、それを削除する必要があります。

Reach CSS のインポートをページから削除し、関連する css ファイル (既定では capptain-reach.css) を削除します。

Reach のリソースである終了画像 (既定では capptain-close.png) とブランド アイコン (既定では capptain-notification-icon) を削除してください。

アプリ内通知の Reach UI を削除します。既定のレイアウトは次のとおりです。

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

テキスト/Web 通知および投票に使用される Reach UI を削除します。既定のレイアウトは次のとおりです。

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

`reach` オブジェクトを構成から削除します (存在する場合)。次のように表示されています。

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

その他の Reach 関連のカスタマイズ (カテゴリなど) をすべて削除します。

#### 非推奨 API の削除

Capptain の API の中には、Mobile Engagement Web SDK では非推奨になっているものがあります。

`agent.connect`、`agent.disconnect`、`agent.pause`、`agent.sendMessageToDevice` の各 API の呼び出しをすべて削除します。

Capptain 構成から、`onConnected`、`onDisconnected`、`onDeviceMessageReceived`、`onPushMessageReceived` の各コールバックをすべて削除します。

#### 構成

Mobile Engagement では、接続文字列を使用して、アプリケーション ID などの SDK 識別子を構成します。

アプリケーション ID を接続文字列に置き換えます。SDK 構成のグローバル オブジェクトが、`capptain` から `azureEngagement` に変わっている点に注意してください。

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

Azure ポータルにアプリケーションの接続文字列が表示されます。

#### JavaScript API

グローバル JavaScript オブジェクト `window.capptain` の名前が `window.azureEngagement` に変更されましたが、API 呼び出しには `window.engagement` エイリアスを使用できます。このエイリアスを使用して、SDK 構成を定義することはできません。

たとえば、`capptain.deviceId` は `engagement.deviceId` になり、`capptain.agent.startActivity` は `engagement.agent.startActivity` になります。他も同様です。

以前のバージョンの Azure Mobile Engagement Web SDK を既にアプリケーションに統合している場合は、[アップグレード手順](mobile-engagement-web-upgrade-procedure.md)をご覧ください。

<!---HONumber=AcomDC_0713_2016-->