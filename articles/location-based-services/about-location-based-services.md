---
title: "Azure Location Based Services の概要 | Microsoft Docs"
description: "Azure Location Based Services (プレビュー) の概要"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: overview
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6871f174eb9bae57d9b4767520d0fb2d8d9631d3
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Azure Location Based Services (プレビュー) の概要
Azure Location Based Services は、Maps、Search、Routing、Traffic、Time Zones のサービス API を含む地理空間サービスのポートフォリオです。 Azure OneAPI に準拠するサービスのポートフォリオにより、使い慣れた開発者ツールを使って、Azure ソリューションに場所情報を統合するソリューションを迅速に開発および拡張できます。 Azure Location Based Services では、あらゆる業界の開発者に対して、Web アプリケーションやモバイル アプリケーションに地理的コンテキストを提供するのに不可欠で、最新のマッピング データが搭載された、強力な地理空間機能を提供します。 Azure Location Based Services は Azure One API に準拠した一連の REST API であり、開発を非常に簡単かつ柔軟で、複数のメディア間で移植可能にする Web ベースの JavaScript コントロールが伴います。 

Azure Location Based Services は、地理的なコンテキストを必要とする Azure アプリケーションを支援する 5 つの主要なサービスで構成されます。 各サービスについて、以下で詳しく説明します。

**Render Service** – Render Service は、開発者がマッピングを中心とする Web アプリケーションおよびモバイル アプリケーションを作成できるように設計されています。 このサービスは、高品質のラスター グラフィックス イメージ、19 のズーム レベル、または完全にカスタマイズ可能なベクター形式のマップ イメージを使います。

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service** – Route Service は、堅牢な現実世界のインフラストラクチャ ジオメトリ計算および複数の輸送モード指示で構築されています。 このサービスを使って開発者は、自動車、トラック、自転車、徒歩などの複数の旅行モードについて、交通状況、重量制限、危険物輸送などの複数の入力を使って、指示を計算できます。

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service** – Search Service は、開発者が住所、場所、名前またはカテゴリ別の事業所一覧、およびその他の地理情報を検索できるように設計されています。 Search Service は、緯度/経度に基づいて住所や交差点の[逆引き地理コード](https://en.wikipedia.org/wiki/Reverse_geocoding)を行うこともできます。 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service** – Time Zone Service を使うと、緯度と経度の組み合わせまたは [IANA ID](http://www.iana.org/) を使って、現在、過去、未来のタイム ゾーン情報のクエリを行うことができます。 また、Time Zone Service では、Microsoft Windows タイム ゾーン ID を IANA タイム ゾーンに変換したり、UTC に対するタイム ゾーン オフセットを取得したり、それぞれのタイム ゾーンで現在の時刻を取得したりすることもできます。 Time Zone Service に対するクエリの一般的な JSON 応答は、次のようになります。

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

**Traffic Service** – Traffic Service は、開発者が交通情報を必要とする Web アプリケーションおよびモバイル アプリケーションを作成できるように設計された Web サービスのスイートです。 このサービスは次のように分かれています。
1. トラフィック フロー - ネットワーク内のすべての主要道路で観察された速度と移動時間をリアルタイムに提供します。 
2. トラフィック インシデント - 道路ネットワークでの交通渋滞およびインシデントに関する正確なビューを提供します。

![Azure Location Based Services Traffic](media/about-location-based-services/Introduction_Traffic.png)

Azure Location Based Services は、モビリティ向けに構築されており、プログラミング モデルが言語を選ばず REST API を介した JSON 出力をサポートしているため、クロス プラットフォーム アプリケーションを支援できます。 さらに、Azure LBS は Web アプリケーションとモバイル アプリケーションの両方をすばやく簡単に開発できるように、単純なプログラミング モデルの便利な JavaScript マップ コントロールを提供します。 

Azure Location Based Services は、キー ベースの認証スキームを使っているので、[Azure Portal](http://portal.azure.com) に移動して Azure Location Based Services アカウントを作成すればサービスにアクセスできます。 アカウントでは 2 つのキーがあらかじめ自動的に生成されます。 アプリケーションへのこれらの場所機能の直接統合を始めるには、Azure Location Based Services サービスへの要求でどちらかのキーを使います。

**Bing Maps との関係** - このドキュメントで説明する Azure Location Based Services は、Bing Maps で提供されるものとは異なることに注意してください。  これらは多くの同じ機能を共有しますが、この 2 つのサービスは異なるものであり、関係していません。  Azure 内のこの新しいサービスの可用性で Bing Maps の製品やロードマップへの影響はなく、別個に管理されます。

Microsoft の目標は、位置情報サービスの観点から開発者コミュニティに選択肢を提供することです。  次に、さまざまなユース ケースと顧客の状況に対して使用すべきサービスについての、開発者向けのクイック ガイドです。  このガイドは、現時点ではパブリック プレビュー段階である Azure LBS に適用され、2018 年の後半に一般公開に達した後に更新されることに注意してください。

| 顧客の条件 | Azure Location Based Services を使用するタイミング | Bing Maps を使用するタイミング |
| ------------- | ------------- | ------------- |
| 開発環境 | 他の Azure サービスへの組み込みまたは利用 | サード パーティのクラウドまたはその他の開発環境の使用 |
| 開発段階  | Azure LBS は現在のところパブリック プレビュー段階のため、初期段階のテストと概念実証開発に最適化されています | 運用環境にはエンタープライズ グレード SLA が必要です |
| 価格オプション | 予備の開発者向けの価格オプションで十分です | カスタマイズされたエンタープライズ グレードの価格が必要です |
| ユース ケース環境 | 車内での使用が必要です | 車内での使用は不要です |
| 地理的範囲 | インド、中国、日本、および韓国は不要です | インド、中国、日本、および韓国のマップ カバレッジが必要です |
| マッピングの内容 | 標準のサーフェス マップで十分です | 衛星画像、航空写真、および道路画像が必要です |
| 元になっているマップ ソース | TomTom マッピング データが推奨されます | HERE マッピング データが推奨されます |

[Azure Location Based Services アカウントに今すぐ](http://aka.ms/azurelbsportal)サインアップしてください。

## <a name="next-steps"></a>次の手順

ここでは、Azure Location Based Services (プレビュー) の概要を説明しました。 次に、Location Based Services のことがわかるサンプル アプリを試し、Web アプリケーションでエンド ツー エンドのシナリオを作成してみてください。

> [!div class="nextstepaction"]
> [Azure Location Based Services (プレビュー) によるデモ版対話型マップ検索を開始する](quick-demo-map-app.md)
> [Azure Location Based Services を使用して近くの目的地を検索する](tutorial-search-location.md)
