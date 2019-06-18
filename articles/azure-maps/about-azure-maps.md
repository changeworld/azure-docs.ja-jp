---
title: Azure Maps の概要 | Microsoft Docs
description: Azure Maps の概要
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 8092cd169f93a6815e52517d805941ac7ddcbbc0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807501"
---
# <a name="what-is-azure-maps"></a>Azure Maps とは

Azure Maps は地理空間サービスのコレクションであり、使用可能な最新のマッピング データによって、精度の高い地理的なコンテキストが Web アプリケーションとモバイル アプリケーションに提供されます。 Azure Maps を構成する REST API では、複数のスタイルと衛星画像で**マップ**をレンダリングしたり、世界中の関心のある住所、場所、ポイントを**検索**したりできるほか、ポイント間、マルチポイント、マルチポイント最適化、等時線、商用車、影響を受ける交通、マトリックス ルーティングを**経路設定**したり、業界最高レベルの交通の流れとインシデントを表示したりできます。また、公共交通、バイク シェアリング、スクーター シェアリング、カー シェアリングの情報を要求し、代替移動手段とリアルタイム データを利用した経路を計画する**モビリティ** サービスや、**位置情報**によるユーザーの場所の特定、場所から**タイム ゾーン**への変換、場所での時刻の取得などを実現できます。 さらに、Azure Maps では、**ジオフェンシング**、マップ **データ** ストレージ、Azure での場所情報のホスティング、地理空間分析によって場所のインテリジェンスを提供する**空間演算**などのためのサービスが提供されています。 Azure Maps サービスは、REST API として直接、あるいは堅牢な **Web SDK** または **Android SDK** を介して、使用できます。 これらのツールを使用して、開発者は、Azure クラウド内から位置情報を Azure ソリューションに統合するソリューションの開発とスケーリングを迅速に行うことができます。 今すぐ無料の [Azure Maps アカウント](https://azure.microsoft.com/services/azure-maps/)にサインアップして、開発を始めてください。

次の動画では、Azure Maps について詳しく説明しています。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>マップ コントロール

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK を使用すると、インタラクティブ マップをカスタマイズして、Web アプリケーションまたはモバイル アプリケーションに独自のコンテンツや映像を表示することができます。 このコントロールには、WebGL が利用されているため、大きなデータ セットを高いパフォーマンスでレンダリングすることができます。 この SDK を使った開発は、JavaScript または TypeScript を使用して行います。

![Azure Maps Web SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK を使用して、強力なモバイル マッピング アプリケーションを作成できます。 

![Azure Maps Android SDK](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Maps のサービス

Azure Maps は、Azure アプリケーションに地理的なコンテキストを提供できる以下の 9 つのサービスで構成されます。

### <a name="data-service"></a>Data Service

データはマップに不可欠です。お客様のデータを Azure Maps サービスに近づけると、遅延が低減し、生産性が上がり、アプリケーションを輝かせる強力で新しいシナリオが作成されます。 Data Service では、空間演算や画像合成で使用する地理空間データをアップロードして格納することで、遅延を短縮し、生産性を向上させるほか、お客様のアプリケーション内で新しいシナリオを有効にできます。 このサービスの詳細については、[Data Service API](https://docs.microsoft.com/rest/api/maps/data) シリーズに関するページを参照してください。

### <a name="mobility-service"></a>モビリティ サービス

Azure Maps のモビリティ サービスでは、停留所、経路情報、推定される移動時間など、周辺の公共交通機関サービスに関するリアルタイムの位置情報インテリジェンスを提供します。 本サービスでは、指定された場所の周囲にある特定の種類のオブジェクト (公共交通機関の停留所、シェアリングされているバイク、スクーター、自動車など) を検索して、一連の交通オブジェクトをオブジェクトの詳細と共に返すことができます。 開発者は本サービスを使用して、基本情報のほかに詳細情報 (路線ジオメトリや停留所のリスト、スケジュールどおりおよびリアルタイムの交通の到着、サービス アラートなど) もカバーする、交通路線の詳細を要求することもできます。 ユーザーはドッキング ステーションの情報を要求することで、利用可能なシェアリング バイクが最も近くのドックにいくつ残されているかを要求することもできます。 Mobility Service には、カー シェアリングの利用可能な自動車を検索する機能もあり、将来の利用可能性や現在の燃料レベルなどの詳細を返すことができます。
Azure Maps Mobility Service では、リアルタイムでの移動計画が可能です。考えられる最良の経路オプションを返すことができるほか、徒歩やバイク、都市圏内 (市内) で利用可能な公共交通機関など、さまざまな移動手段を提示できます。 さらに開発者は、経路のジオメトリや移動プランの詳しいスケジュールなど、追加の情報が含まれた詳細な交通移動プランを要求することもできます。

本サービスとそのさまざまな機能の詳細については、[API に関するドキュメント](https://docs.microsoft.com/rest/api/maps/mobility)を参照してください

### <a name="render-service"></a>Render Service

Render Service は、開発者がマッピングを中心とする Web アプリケーションおよびモバイル アプリケーションを作成できるように設計されています。 このサービスは、高品質のラスター グラフィックス イメージ、19 のズーム レベル、または完全にカスタマイズ可能なベクター形式のマップ イメージを使います。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Render Service では現在、開発者が衛星画像を操作できるプレビュー API が提供されています。 詳細については、[Azure Maps Render API](https://docs.microsoft.com/rest/api/maps/render) に関するページを参照してください。

### <a name="route-service"></a>Route Service

Route Service には、現実世界のインフラストラクチャに関する堅牢なジオメトリ計算と複数の輸送モードでの道案内が含まれています。 このサービスを使って、開発者は、自動車、トラック、自転車、徒歩などの複数の移動手段について指示を計算できます。 サービスでは、交通状況、重量制限、危険物輸送などの入力も検討できます。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Route Service では現在、複数のルート要求のバッチ処理、一連の出発地と目的地の間における移動時間と距離のマトリックス、時間または燃料の要件に基づいた移動可能なルートまたは距離の検出など、高度な機能のプレビューが提供されています。 ルーティング機能の詳細については、[Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route) に関するページを参照してください。

### <a name="search-service"></a>検索サービス

Search Service は、開発者が住所、場所、名前またはカテゴリ別の事業所一覧、およびその他の地理情報を検索できるように設計されています。 Search Service では、緯度と経度に基づいて住所や交差点の[逆引き地理コード](https://en.wikipedia.org/wiki/Reverse_geocoding)を行うこともできます。

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Search Service ではさらに、ルートに沿った検索、より広い範囲内での検索、複数の検索要求のバッチ処理、ある一点の場所ではなく広い範囲での検索など、高度な機能が提供されています。 バッチおよび範囲の検索の API は現在プレビュー段階です。 検索機能の詳細については、[Azure Maps Search API](https://docs.microsoft.com/rest/api/maps/search) に関するページを参照してください。

### <a name="spatial-operations"></a>Spatial Operations

Azure Maps Spatial Operations は、位置情報を取得し、すぐさまこれを分析し、その時間と空間で発生している進行中のイベントをお客様にお知らせし、ほぼリアルタイムの分析とイベントの予測モデリングを可能にします。 このサービスでは、Azure Maps のお客様が、最接近点、大圏距離、バッファーなどのサービスを含め、一般的な地理空間数学計算のライブラリを使用して、位置情報のインテリジェンスをネイティブに強化できます。 本サービスとそのさまざまな機能の詳細については、[API に関するドキュメント](https://docs.microsoft.com/rest/api/maps/spatial)を参照してください。

### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service では、緯度と経度の組み合わせまたは [IANA ID](https://www.iana.org/) を使って、現在、過去、未来のタイム ゾーン情報のクエリを実行できます。 また、Time Zone Service では、Microsoft Windows タイム ゾーン ID を IANA タイム ゾーンに変換したり、UTC に対するタイム ゾーン オフセットを取得したり、それぞれのタイム ゾーンで現在の時刻を取得したりすることもできます。 Time Zone Service に対するクエリの一般的な JSON 応答は、次の例のようになります。

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

このサービスの詳細については、[Azure Maps Timezone API](https://docs.microsoft.com/rest/api/maps/timezone) に関するページを参照してください。

### <a name="traffic-service"></a>Traffic Service

Traffic Service は、交通情報を必要とする Web アプリケーションおよびモバイル アプリケーションを開発者が作成できるように設計された Web サービスのスイートです。 サービスでは、次の 2 つのデータ型が提供されます。

* トラフィック フロー - ネットワーク内のすべての主要道路で観察されたリアルタイムの速度と移動時間。
* トラフィック インシデント - 道路ネットワークでの交通渋滞およびインシデントに関する最新のビュー。

![Azure Maps の交通情報](media/about-azure-maps/Introduction_Traffic.png)

詳細については、[Azure Maps Traffic API](https://docs.microsoft.com/rest/api/maps/traffic) に関するページを参照してください。

### <a name="ip-to-location"></a>IP to Location

IP to Location サービスでは、特定の IP アドレスに関して、取得した 2 文字の国番号をプレビューできます。 このサービスを使用すると、地域に基づいてカスタマイズされたアプリケーション コンテンツを強化することで、ユーザー エクスペリエンスを調整して拡張できます。

IP to Location サービスの REST API については、[Azure Maps Geolocation API](https://docs.microsoft.com/rest/api/maps/geolocation) に関するページを参照してください。

## <a name="programming-model"></a>プログラミング モデル

モビリティを実現するよう設計された Azure Maps では、クロスプラットフォーム アプリケーションを強化できます。 言語に依存せず、[REST API](https://docs.microsoft.com/rest/api/maps/) を通じて JSON をサポートするプログラミング モデルが使用されます。

さらに、Azure Maps は Web アプリケーションとモバイル アプリケーションの両方をすばやく簡単に開発できるように、単純なプログラミング モデルの便利な [JavaScript マップ コントロール](https://docs.microsoft.com/javascript/api/azure-maps-control)を提供します。

## <a name="usage"></a>使用法

[Azure portal](https://portal.azure.com) に移動して Azure Maps アカウントを作成すると、Maps サービスにアクセスできます。

Azure Maps では、キーベースの認証スキームが使用されます。 アカウントでは 2 つのキーがあらかじめ自動的に生成されます。 アプリケーションへのこれらの場所機能の統合を始めるには、どちらかのキーを使用して Azure Maps サービスへの要求を行います。

## <a name="supported-regions"></a>サポートされているリージョン

Azure Maps API は現在、以下のリージョンを除くすべての国/地域で利用できます。

* アルゼンチン
* 中国
* インド
* モロッコ
* パキスタン
* 韓国

ご自身の現在の IP アドレスの場所が上記のサポートされていない国/地域に含まれていないことを確認してください。

## <a name="next-steps"></a>次の手順

Azure Maps の新機能に関する詳細情報:

> [!div class="nextstepaction"]
> [ルート マトリックス、等時線、IP 検索など](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Azure Maps を紹介するサンプル アプリの試行:

> [!div class="nextstepaction"]
> [クイック スタート:Web アプリを作成する](quick-demo-map-app.md)
