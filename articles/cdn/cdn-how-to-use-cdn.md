<properties 
	pageTitle="CDN を使用する方法 | Microsoft Azure"
	description="Azure Content Delivery Network (CDN) を使用して、BLOB と静的コンテンツをキャッシュすることにより、高帯域幅コンテンツを配信する方法について説明します。"
	services="cdn"
	documentationCenter=".net"
	authors="zhangmanling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/01/2015"
	ms.author="mazha"/>


# Azure 用 CDN の使用

Azure Content Delivery Network (CDN) は、Azure で任意の HTTP アプリケーションを拡張するための基本的要素です。CDN は、Azure のお客様にグローバル ソリューションを提供するもので、エンド ユーザーに近い場所にコンテンツをキャッシュして配信します。そのため、ユーザーからの要求は、毎回オリジンを参照する代わりに、最適なパフォーマンスが得られる CDN エッジ POP にインテリジェントにルーティングされます。これにより、パフォーマンスとユーザー エクスペリエンスが大幅に向上します。現在の CDN ノードの場所の一覧については、「[Azure CDN ノードの場所]」を参照してください。

CDN を使用して Azure データをキャッシュすることには、次のような利点があります。

-   コンテンツ ソースから遠く離れた場所にいる、コンテンツの読み込みに数多くの "インターネット トリップ" が必要なアプリケーションを使用するエンド ユーザーに対する、パフォーマンスとユーザー エクスペリエンスの向上
-   製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型の配信スケール
-   ユーザー要求を分散させ、コンテンツをグローバル エッジ POP から配信することによる、オリジン宛てのトラフィックの削減とオリジンの負荷軽減

既存の CDN のお客様は、[Azure 管理ポータル]で Azure CDN を使用できます。CDN はサブスクリプションのアドオン機能であり、別の[お支払いプラン]があります。

##手順 1: Azure に CDN オリジンを作成する

CDN オリジンとは、CDN によって取得されエッジ POP にキャッシュされるコンテンツがある場所です。統合された Azure オリジンには、Azure Apps、Cloud Services、Storage、Media Services が含まれます。

## 手順 2: Azure オリジンを示す CDN エンドポイントを作成する

オリジンを設定すると、[新しい CDN エンドポイントを作成](cdn-create-new-endpoint.md)するときにオリジンの一覧で選択できるようになります。

> [AZURE.NOTE]エンドポイントのために作成された構成は、すぐには使用できません。CDN ネットワークを通じて伝播する登録などのために最大 60 分かかります。CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 400 (正しくない要求) を受け取る場合があります。

## 手順 3: CDN 構成を設定する 

CDN エンドポイントに対して、キャッシュ ポリシー、クエリ文字列のキャッシュなど、さまざまな機能を有効にすることができます。

## 手順 4: CDN コンテンツにアクセスする

CDN にキャッシュされたコンテンツにアクセスするには、ポータルで提供される CDN URL を使用します。たとえば、キャッシュされた BLOB のアドレスは、http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*> のようになります。



## 関連項目

[Azure Content Delivery Network (CDN) の概要](cdn-overview.md)
 

<!---HONumber=September15_HO1-->