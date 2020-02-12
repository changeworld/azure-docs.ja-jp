---
title: チュートリアル:Google マップから Azure Maps に移行する | Microsoft Azure Maps
description: Google マップから Microsoft Azure Maps に移行する方法に関するチュートリアル。 ガイダンスでは、Azure Maps の API と SDK への切り替え方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1f6f282406c6813b2b126c300f21bda21e8f9464
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988976"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Google マップから Azure Maps に移行する

このチュートリアルでは、Web アプリケーション、モバイル アプリケーション、サーバーベース アプリケーションを Google Maps から Microsoft Azure Maps プラットフォームに移行する方法についての分析情報を提供します。 このチュートリアルには、Azure Maps に移行するための比較コード サンプル、移行に関する提案、ベスト プラクティスが含まれています。

## <a name="azure-maps-platform-overview"></a>Azure Maps プラットフォームの概要

Azure Maps には、あらゆる業界の開発者向けに、Web アプリケーションやモバイル アプリケーションに地理的コンテキストを提供するための、定期的に更新されるマップ データが搭載された強力な地理空間機能が用意されています。 Azure Maps は、Azure One API に準拠した REST API のセットです。 それらの REST API は、マップ、検索、ルート指定、トラフィック、タイム ゾーン、位置情報、ジオフェンシング、マップ データ、天気、モビリティ、空間演算を提供します。 演算は、Web と Android の両方の SDK によって実現されているため、開発が簡単になり、柔軟性が高く、複数のプラットフォーム間で移植性が確保されます。

## <a name="high-level-platform-comparison"></a>プラットフォームのおおまかな比較

次の表は、Google Maps の機能とそれに対応する Azure Maps の機能の大まかな一覧です。 この一覧に Azure Maps のすべての機能が網羅されているわけではありません。 Azure Maps には、他にもアクセシビリティ、ジオフェンシング API、等時線、空間演算、直接マップ タイル アクセス、バッチ サービス、データ カバレッジの比較 (画像のカバレッジ) などの機能があります。

| Google Maps 機能         | Azure Maps のサポート                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 対応予定                                |
| REST サービス API           | ✓                                      |
| Directions (ルーティング)        | ✓                                      |
| Distance Matrix             | ✓                                      |
| Elevation                   | 対応予定                                |
| Geocoding (Forward/Reverse) | ✓                                      |
| 地理的位置情報                 | 該当なし                                    |
| Places Search               | ✓                                      |
| Places Details              | 該当なし – Web サイトと電話番号を利用可能 |
| Places Photos               | 該当なし                                    |
| Place Autocomplete          | ✓                                      |
| Static Maps                 | ✓                                      |
| Static Street View          | 該当なし                                    |
| タイム ゾーン                   | ✓                                      |
| Maps Embedded API           | 該当なし                                    |
| Map URL                    | 該当なし                                    |

Google Maps では、基本的なキーベースの認証が提供されます。 Azure Maps には、基本的なキーベースの認証と、高度なセキュリティで保護された Azure Active Directory 認証の両方が用意されています。

## <a name="licensing-considerations"></a>ライセンスに関する考慮事項

Google Maps から Azure Maps に移行する場合は、ライセンスに関して次の点を考慮する必要があります。

- Azure Maps では、読み込まれたマップ タイルの数に基づいて対話型マップの使用量に対して料金が請求されます。一方、Google Maps では、マップ コントロールの読み込みに対して料金が請求されます。 対話型 Azure Maps SDK では、開発者のコストを削減するために、マップ タイルが自動的にキャッシュされます。 マップ タイルが 15 個読み込まれるごとに、1 個の Azure Maps トランザクションが生成されます。 対話型 Azure Maps SDK では、512 ピクセルのタイルが使用され、平均してページ ビューあたり 1 個またはそれより少ないトランザクションが生成されます。
- 多くの場合、Google Maps Web サービスの静的マップ画像は Azure Maps Web SDK に置き換えた方が、ずっとコスト効果に優れています。 Azure Maps Web SDK では、マップタイルを使用します。ユーザーがマップをパンしたりズームしたりしない限り、多くの場合、マップの読み込みごとにトランザクションのごく一部のみが生成されます。 Azure Maps Web SDK には、パンとズームを無効にするオプションがあります。 また、Azure Maps Web SDK には、静的なマップ Web サービスよりも非常に多くの視覚化オプションが用意されています。
- Azure Maps を使用すると、データをそのプラットフォームから Azure に格納できます。 また、[使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)に従って、最大 6 か月間キャッシュすることもできます。

Azure Maps のいくつかの関連リソースを次に示します。

- [Azure Maps の価格のページ](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps 使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (Microsoft オンライン サービスの使用条件に含まれています)
- [Azure Maps での適切な価格レベルの選択](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>おすすめの移行プラン

移行プランの概要を次に示します。

1. アプリケーションで使用している Google Maps SDK とサービスのインベントリを取得し、移行先となる代替の SDK とサービスが Azure Maps で用意されていることを確認します。
2. [https://azure.com](https://azure.com) で Azure サブスクリプションを作成します (まだお持ちでない場合)。
3. Azure Maps アカウント ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) と認証キーまたは Azure Active Directory ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)) を作成します。
4. アプリケーション コードを移行します。
5. 移行したアプリケーションをテストします。
6. 移行したアプリケーションを運用環境にデプロイします。

## <a name="azure-maps-technical-resources"></a>Azure Maps の技術リソース

Azure Maps に関する役に立つ技術リソースの一覧を次に示します。

- 概要: [https://azure.com/maps](https://azure.com/maps)
- ドキュメント: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK コード サンプル: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 開発者フォーラム: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- ビデオ: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- ブログ: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps のフィードバック (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>移行サポート

開発者は、[フォーラム](https://aka.ms/AzureMapsForums) または多くの Azure サポート オプション ([https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)) のいずれかを使用して、移行サポートを探すことができます。

## <a name="next-steps"></a>次のステップ

Google Maps アプリケーションを移行する方法について次の記事でご確認ください。

> [!div class="nextstepaction"]
> [Web アプリを移行する](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Android アプリを移行する](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Web サービスを移行する](migrate-from-google-maps-web-services.md)
