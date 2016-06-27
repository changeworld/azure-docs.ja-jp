<properties
	pageTitle="Azure Mobile Engagement Web SDK の統合 | Microsoft Azure"
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
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Web アプリケーションに Engagement を統合する方法

> [AZURE.SELECTOR]
- [Windows ユニバーサル](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

この手順では、Web アプリケーションで Engagement の分析機能、監視機能を有効化する最も簡単な方法を説明します。

ユーザー、セッション、アクティビティ、クラッシュ、および技術に関するすべての統計をコンピューティングするために必要なログ レポートをアクティブ化するには、以下の手順で十分です。イベント、エラー、ジョブなどの他の統計情報をコンピューティングするのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、Engagement API を使用して手動で実行する必要があります ([Web アプリケーションで高度な Mobile Engagement タグ付け API を使用する方法を参照](mobile-engagement-web-use-engagement-api.md))。

## はじめに

[ここ](http://aka.ms/P7b453)から Web SDK をダウンロードします。この SDK は、**azure-engagement.js** という名前の単体の JavaScript ファイルとして出荷されます。Web アプリケーションまたはサイトのすべてのページにこのファイルをインクルードする必要があります。

このスクリプトは**必ず**、アプリケーションに必要な Engagement を構成するスクリプトまたはコード スニペット (ご自身で作成する必要があります) の**後に**読み込んでください。

## ブラウザーとの互換性

Engagement Web SDK には、ネイティブの JSON エンコーディング/デコーディングと (W3C CORS 仕様による) クロス ドメイン AJAX 要求が使用されています。

* Edge 12 以上
* IE 10 以上
* Firefox 3.5 以上
* Chrome 4 以上
* Safari 6 以上
* Opera 12 以上

## Engagement の構成

以下のように、**azureEngagement** というグローバルな JavaScript オブジェクトを生成するスクリプトを作成します。
 
実際のサイトには複数のページが含まれている可能性もあるので、この例では、このスクリプトについても、すべてのページにインクルードするものとして説明します。この手順では、`azure-engagement-conf.js` という名前でインクルードしています。

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

アプリケーションの `connectionString` が Azure ポータルに表示されます。

> [AZURE.NOTE] `appVersionName` と `appVersionCode` は省略可能ですが、分析機能でバージョン情報を処理できるように、それらを構成することをお勧めします。

## Engagement スクリプトをページにインクルードする

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

または

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## エイリアス

SDK スクリプトの読み込みが完了すると、SDK の API にアクセスするための **engagement** というエイリアスが作成されます (SDK の構成を定義する目的では使用できません)。このドキュメントでは、このエイリアスを参照として使用します。

この既定のエイリアスが、ページ内の別のグローバル変数と競合する場合は、SDK を読み込む前の構成の中で再定義してください。

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## 基本的なレポート

### セッションの追跡

Engagement セッションは、名前で識別される一連のアクティビティに分割されます。

クラシック Web サイトでは、サイトのページごとに異なるアクティビティを宣言することをお勧めします。ページ移動のない Web サイトまたは Web アプリケーションでは、もっと洗練された方法でアクティビティを追跡することが必要になります。

どちらにしても、ユーザー アクティビティを開始したり現在のユーザー アクティビティを変更したりするには、次の例のように `engagement.agent.startActivity` 関数を呼び出します。

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

開いているセッションは、アプリケーション ページを閉じてから 3 分以内に Engagement サーバーによって自動的に終了されます。

または、`engagement.agent.endActivity` を呼び出してセッションを手動で終了させることもできます。この場合、現在のユーザー アクティビティが 'idle' に設定され、その 10 秒後にセッションが実際に終了されます (ただしその間に、`engagement.agent.startActivity` を新たに呼び出してセッションを再開した場合を除く)。
 
この 10 秒の時間差は、グローバル **engagement** オブジェクト内で構成できます。

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `onunload` コールバックの中で `engagement.agent.endActivity` を使用することはできません (この段階での Ajax 呼び出しは不可)。

## 詳細な報告

必須ではありませんが、アプリケーションに固有の `events` や `errors`、`jobs` をレポートする場合は、`engagement.agent` オブジェクトを介して Engagement API を使用する必要があります。

Engagement API を通じて、Engagement の高度な機能をすべて利用することができます。この点については、「[How to use the advanced Mobile Engagement tagging API in a Web application (Mobile Engagement の高度なタグ付け API を Web アプリケーションから使用する方法)](mobile-engagement-web-use-engagement-api.md)」に詳しく説明されています。

## AJAX 呼び出しに使用する URL のカスタマイズ

SDK によって使用される URL はカスタマイズすることができます。たとえば、ログ URL (SDK でログに使用されるエンドポイント) を再定義するには、次のように構成をオーバーライドします。

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

URL 関数からの戻り値が `/`、`//`、`http://`、`https://` のいずれかの文字列である場合、既定のスキームは使用されません。

それらの URL には、既定で `https://` スキームが使用されます。既定のスキームをカスタマイズする必要がある場合は、次のように構成をオーバーライドしてください。

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->