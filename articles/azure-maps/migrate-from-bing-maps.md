---
title: チュートリアル:Bing 地図から Azure Maps に移行する | Microsoft Azure Maps
description: Bing 地図から Microsoft Azure Maps に移行する方法に関するチュートリアル。 ガイダンスでは、Azure Maps の API と SDK への切り替え方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643e49bdba76051c873ed549d5f6c21487f34056
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108135"
---
# <a name="migrate-from-bing-maps-to-azure-maps"></a>Bing 地図から Azure Maps に移行する

このガイドでは、Web アプリケーション、モバイル アプリケーション、サーバーベース アプリケーションを Bing 地図から Azure Maps プラットフォームに移行する方法についての分析情報を提供します。 このガイドには、Azure Maps に移行するための比較コード サンプル、移行に関する提案、ベスト プラクティスが含まれています。

## <a name="azure-maps-platform-overview"></a>Azure Maps プラットフォームの概要

Azure Maps は、Web アプリケーションやモバイル アプリケーションに地理的コンテキストを提供するために使用できる最新のマッピング データが搭載された強力な地理空間機能を、あらゆる業界の開発者に提供します。 Azure Maps は、Azure One API に準拠した一連の REST API であり、マップ、検索、経路指定、交通状況、タイム ゾーン、位置情報、マップ データ、気象データなど、さまざまなサービスに対応しています。Web SDK と Android SDK の両方が用意されており、開発を簡単かつ柔軟に行えるうえ、複数のプラットフォーム全体での移植が可能です。 [Azure Maps は Power BI でも利用できます](power-bi-visual-getting-started.md)。

## <a name="high-level-platform-comparison"></a>プラットフォームのおおまかな比較

次の表では、Bing 地図機能と、それらの機能に相当する Azure Maps でのサポートの大まかな一覧を示します。 この一覧には、その他の Azure Maps 機能 (アクセシビリティ、ジオフェンシング API、交通サービス、空間演算、直接マップ タイル アクセス、一括サービスなど) は含まれていません。

| Bing 地図の機能                     | Azure Maps のサポート |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | 対応予定            |
| UWP SDK                               | 対応予定            |
| WPF SDK                               | 対応予定            |
| REST サービス API                     | ✓                  |
| 自動提案                           | ✓                  |
| ルート案内 (トラックを含む)          | ✓                  |
| Distance Matrix                       | ✓                  |
| 標高                            | 対応予定            |
| 映像 - 静的マップ                  | ✓                  |
| 映像メタデータ                      | ✓                  |
| 等時線                            | ✓                  |
| Local Insights                        | ✓                  |
| ローカル検索                          | ✓                  |
| 位置情報認識                  | ✓                  |
| 位置情報 (フォワード ジオコーディングと逆ジオコーディング) | ✓                  |
| 最適化旅程ルート            | 対応予定            |
| Snap to Road                         | ✓                  |
| Spatial Data Services (SDS)           | Partial            |
| タイム ゾーン                             | ✓                  |
| トラフィック インシデント                     | ✓                  |
| 構成ドリブン マップ             | N/A                |

Bing 地図では、基本的なキーベースの認証が提供されます。 Azure Maps には、基本的なキーベースの認証と、高度なセキュリティで保護された Azure Active Directory 認証の両方が用意されています。

## <a name="licensing-considerations"></a>ライセンスに関する考慮事項

Bing 地図から Azure Maps に移行する場合は、ライセンスに関して次の点を考慮する必要があります。

-   Azure Maps では、読み込まれたマップ タイルの数に基づいて対話型マップの使用量に対して料金が請求されます。一方、Bing 地図では、マップ コントロール (セッション) の読み込みに対して料金が請求されます。 Azure Maps では、開発者のコストを削減するために、マップ タイルが自動的にキャッシュされます。 マップ タイルが 15 個読み込まれるごとに、1 個の Azure Maps トランザクションが生成されます。 対話型 Azure Maps SDK では、512 ピクセルのタイルが使用され、平均してページ ビューあたり 1 個またはそれより少ないトランザクションが生成されます。

-   Azure Maps を使用すると、データをそのプラットフォームから Azure に格納できます。 また、[使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)に従って、最大 6 か月間キャッシュすることもできます。

Azure Maps のいくつかのライセンス関連リソースを次に示します。

-   [Azure Maps の価格のページ](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps 使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (Microsoft オンライン サービスの使用条件に含まれています)
-   [Azure Maps での適切な価格レベルの選択](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>おすすめの移行プラン

移行プランの概要を次に示します。

1.  アプリケーションで使用している Bing Maps SDK とサービスのインベントリを取得し、移行先となる代替の SDK とサービスが Azure Maps で用意されていることを確認します。
2.  <https://azure.com> で Azure サブスクリプションを作成します (まだお持ちでない場合)。
3.  Azure Maps アカウント ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) と認証キーまたは Azure Active Directory ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)) を作成します。
4.  アプリケーション コードを移行します。
5.  移行したアプリケーションをテストします。
6.  移行したアプリケーションを運用環境にデプロイします。

## <a name="azure-maps-technical-resources"></a>Azure Maps の技術リソース

Azure Maps に関する役に立つ技術リソースの一覧を次に示します。

-   概要: https://azure.com/maps
-   ドキュメント: <https://aka.ms/AzureMapsDocs>
-   Web SDK コード サンプル: <https://aka.ms/AzureMapsSamples>
-   開発者フォーラム: <https://aka.ms/AzureMapsForums>
-   ビデオ: <https://aka.ms/AzureMapsVideos>
-   ブログ: <https://aka.ms/AzureMapsBlog>
-   Azure Maps のフィードバック (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>移行サポート

開発者は、[フォーラム](https://aka.ms/AzureMapsForums) または多くの Azure サポート オプション (<https://azure.microsoft.com/support/options/>) のいずれかを使用して、移行サポートを探すことができます。

## <a name="new-terminology"></a>新しい用語 

以下に示したのは、Bing 地図に関してよく用いられる用語のうち、Azure Maps では呼称が異なるものの一覧です。

| Bing 地図の用語                    | Azure Maps の用語                                                |
|-----------------------------------|----------------------------------------------------------------|
| 航空写真                            | 衛星映像または航空映像                                            |
| 道順                        | ルート指定と呼ばれることもあります                             |
| エンティティ                          | ジオメトリまたは地物                                         |
| `EntityCollection`                | データ ソースまたはレイヤー                                           |
| `Geopoint`                        | [位置]                                                       |
| `GeoXML`                          | 空間 IO モジュールの XML ファイル                             |
| グラウンド オーバーレイ                    | イメージ レイヤー                                                    |
| ハイブリッド (マップの種類に関して) | 道路を含む衛星映像                                           |
| 情報ボックス                           | ポップアップ                                                          |
| 場所                          | [位置]                                                       |
| `LocationRect`                    | 境界ボックス                                                   |
| マップの種類                          | [Map style] (地図のスタイル)                                                      |
| ナビゲーション バー                    | マップ スタイル ピッカー、ズーム コントロール、ピッチ コントロール、コンパス コントロール |
| 画鋲                           | バブル レイヤー、シンボル レイヤー、HTML マーカー                      |

## <a name="next-steps"></a>次の手順

Bing 地図アプリケーションを移行する詳しい方法について次の記事でご確認ください。

> [!div class="nextstepaction"]
> [Web アプリを移行する](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [Web サービスを移行する](migrate-from-bing-maps-web-services.md)
