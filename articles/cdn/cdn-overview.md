<properties 
	pageTitle="Azure CDN の概要" 
	description="Azure Content Delivery Network (CDN) の概要と、CDN を使用して BLOB と静的コンテンツをキャッシュして高帯域幅コンテンツを配信する方法について説明します。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Azure Content Delivery Network (CDN) の概要

Azure Content Delivery Network (CDN) では、クラウド サービスで使用される Azure の BLOB と静的コンテンツを戦略的に配置された場所にキャッシュし、ユーザーへのコンテンツ配信に最大帯域幅を使用できます。

既存の CDN 顧客は、[Microsoft Azure ポータル](https://portal.azure.com)から CDN エンドポイントを管理できるようになりました。


CDN では、世界各地の物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツを配信するためのグローバル ソリューションを開発者に提供します。現在の CDN ノードの場所の一覧については、「[Azure コンテンツ配信ネットワーク (CDN) POP 場所](cdn-pop-locations.md)」を参照してください。

CDN を使用して Azure データをキャッシュすると、次のような利点があります。

- コンテンツ ソースから遠く離れた場所にいる、コンテンツの読み込みに数多くの "インターネット トリップ" が必要なアプリケーションを使用しているエンド ユーザーに対する、パフォーマンスとユーザー エクスペリエンスの向上
- 製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型の配信スケール 


>[AZURE.IMPORTANT]CDN エンドポイントを作成または有効にした場合、世界中に反映されるまでに最大で 90 分かかることがあります。
 
最初にオブジェクトに対する要求が CDN に行われると、オブジェクトはオブジェクトのソースの元の場所から直接取得されます。この配信元には、Azure ストレージ アカウント、Web アプリ、クラウド サービス、パブリック Web 要求を受け入れる任意のカスタム配信元などがあります。CDN 構文を使用して要求が行われると、その要求は、要求が行われた場所から最も近い CDN エンドポイントにリダイレクトされ、オブジェクトへのアクセスが提供されます。オブジェクトがそのエンドポイントで見つからない場合は、サービスから取得され、そのエンドポイントにキャッシュされます。有効期間 (TTL) の設定はキャッシュされたオブジェクトでも維持されています。

## Standard の機能

Standard CDN レベルには、以下の機能が含まれます。

- [Storage、Web Apps、Media Services](cdn-create-a-storage-account-with-cdn.md) などの Azure サービスと簡単に統合
- [クエリ文字列のキャッシュ](cdn-query-string.md)
- [カスタム ドメイン名のサポート](cdn-map-content-to-custom-domain.md)
- [国のフィルタリング](cdn-restrict-access-by-country.md)
- [コア分析](cdn-analyze-usage-patterns.md)
- [カスタム コンテンツ配信元](cdn-map-content-to-custom-domain.md)
- [HTTPS のサポート](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- 負荷分散
- DDOS 保護
- [高速消去](cdn-purge-endpoint.md)

 
## Premium の機能

Premium CDN レベルには Standard レベルのすべての機能に加え、以下の機能があります。

- [カスタマイズ可能なルール ベースのコンテンツ配信エンジン](cdn-rules-engine.md)
- [詳細な HTTP レポート](cdn-advanced-http-reports.md)
- [リアルタイム統計](cdn-read-time-stats.md)

<!---HONumber=AcomDC_1210_2015-->