---
title: Azure Maps の概要 | Microsoft Docs
description: Azure Maps の概要
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4882eadb8db5137d6fcf75c6d80c34ae050d3a6d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193953"
---
# <a name="an-introduction-to-azure-maps"></a>Azure Maps の概要
Azure Maps は、Maps、Search、Routing、Traffic、Time Zones のサービス API を含む地理空間サービスのポートフォリオです。 サービスのポートフォリオにより、使い慣れたツールを使って、Azure ソリューションに場所情報を統合するソリューションを迅速に開発および拡張できます。 Azure Maps は、あらゆる業界の開発者に対して、Web アプリケーションやモバイル アプリケーションに地理的コンテキストを提供するのに不可欠で、最新のマッピング データが搭載された、強力な地理空間機能を提供します。 Azure Maps は一連の REST API であり、開発を簡単かつ柔軟にし、複数のメディア間で移植可能にする Web ベースの JavaScript コントロールが伴います。 

Azure Maps については、次のビデオで紹介されています。

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Maps は、地理的なコンテキストを必要とする Azure アプリケーションを支援する 5 つの主要なサービスで構成されます。 各サービスについて詳しく説明します。

**Render Service** は、開発者がマッピングを中心とする Web アプリケーションおよびモバイル アプリケーションを作成できるように設計されています。 このサービスは、高品質のラスター グラフィックス イメージ、19 のズーム レベル、または完全にカスタマイズ可能なベクター形式のマップ イメージを使います。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

**Route Service** には、堅牢な現実世界のインフラストラクチャ ジオメトリ計算および複数の輸送モード指示が含まれています。 このサービスを使って、開発者は、自動車、トラック、自転車、徒歩などの複数の移動手段について指示を計算できます。 サービスでは、交通状況、重量制限、危険物輸送などの入力も検討できます。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

**Search Service** は、開発者が住所、場所、名前またはカテゴリ別の事業所一覧、およびその他の地理情報を検索できるように設計されています。 Search Service は、緯度/経度に基づいて住所や交差点の[逆引き地理コード](https://en.wikipedia.org/wiki/Reverse_geocoding)を行うこともできます。 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

**Time Zone Service** を使うと、緯度と経度の組み合わせまたは [IANA ID](http://www.iana.org/) を使って、現在、過去、未来のタイム ゾーン情報のクエリを実行できます。 また、Time Zone Service では、Microsoft Windows タイム ゾーン ID を IANA タイム ゾーンに変換したり、UTC に対するタイム ゾーン オフセットを取得したり、それぞれのタイム ゾーンで現在の時刻を取得したりすることもできます。 Time Zone Service に対するクエリの一般的な JSON 応答は、次の例のようになります。

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

**Traffic Service** は、開発者が交通情報を必要とする Web アプリケーションおよびモバイル アプリケーションを作成できるように設計された Web サービスのスイートです。 サービスでは、次の 2 つのデータ型が提供されます。
* トラフィック フロー - ネットワーク内のすべての主要道路で観察されたリアルタイムの速度と移動時間。 
* トラフィック インシデント - 道路ネットワークでの交通渋滞およびインシデントに関する正確なビュー。

![Azure Maps の交通情報](media/about-azure-maps/Introduction_Traffic.png)

Azure Maps は、モビリティ向けに構築されており、プログラミング モデルが言語を選ばず REST API を介した JSON 出力をサポートしているため、クロス プラットフォーム アプリケーションを支援できます。 さらに、Azure Maps は Web アプリケーションとモバイル アプリケーションの両方をすばやく簡単に開発できるように、単純なプログラミング モデルの便利な JavaScript マップ コントロールを提供します。 

Azure Maps は、キー ベースの認証スキームを使っているので、[Azure Portal](http://portal.azure.com) に移動して Azure Maps アカウントを作成すればサービスにアクセスできます。 アカウントでは 2 つのキーがあらかじめ自動的に生成されます。 アプリケーションへのこれらの場所機能の直接統合を始めるには、Azure Maps サービスへの要求でどちらかのキーを使います。

## <a name="unsupported-regions"></a>サポートされていないリージョン
Azure Maps API は、一部の国では現在利用できません。 現在の IP アドレスをチェックして、ご自身の IP アドレスの場所が、以下のサポートされていない国に含まれていないことをご確認ください。

* アルゼンチン
* 中国
* インド
* モロッコ
* パキスタン
* 韓国

## <a name="relationship-with-bing-maps"></a>Bing 地図との関係
このドキュメントで説明するマップは、Bing Maps で提供されるものとは異なることにご注意ください。 これらは多くの同じ機能を共有しますが、この 2 つのサービスは異なるものであり、関係していません。 この Azure サービスは、Bing Maps 製品のサービスやロードマップには影響しません。

Microsoft の目標は、位置情報サービスの観点から開発者コミュニティに選択肢を提供することです。 次の表には、使用するサービスを決定する開発者向けのガイダンスが含まれています。 

| シナリオ | Azure Maps を使用するタイミング | Bing 地図を使用するタイミング |
| ------------- | ------------- | ------------- |
| 開発環境 | 他の Azure サービスへの組み込みまたは調整 | サード パーティのクラウドまたはその他の開発環境の使用 |
| 開発段階  | Azure Maps は、初期段階のテストと概念実証開発用に最適化されています | 運用環境にはエンタープライズ グレード SLA が必要です |
| 価格オプション | 予備の開発者向けの価格オプションで十分です | カスタマイズされたエンタープライズ グレードの価格が必要です |
| ユース ケース環境 | 車内での使用が必要です | 車内での使用は不要です |
| 地理的範囲 | インド、中国、日本、および韓国は不要です | インド、中国、日本、および韓国のマップ カバレッジが必要です |
| マッピングの内容 | 標準のサーフェス マップで十分です | 衛星画像、航空写真、および道路画像が必要です |
| 元になっているマップ ソース | TomTom マッピング データが推奨されます | HERE マッピング データが推奨されます |

[Azure Maps アカウントに今すぐ](http://aka.ms/azurelbsportal)サインアップしてください。

## <a name="next-steps"></a>次の手順

これで、Azure Maps の概要の説明が終わりました。 次の手順では、サービスを示すサンプル アプリを試します。

> [!div class="nextstepaction"]
> [デモの対話形式の検索マップを起動する](quick-demo-map-app.md)

