---
title: チュートリアル - Google Maps から Azure Maps に移行する | Microsoft Azure Maps
description: Google Maps から Microsoft Azure Maps に移行する方法に関するチュートリアルです。 ガイダンスでは、Azure Maps の API と SDK への切り替え方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ee7eda58c211ca570b052d55c813999e62b95fde
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876313"
---
# <a name="tutorial---migrate-from-google-maps-to-azure-maps"></a>チュートリアル - Google Maps から Azure Maps に移行する

この記事では、Web アプリケーション、モバイル アプリケーション、サーバーベース アプリケーションを Google Maps から Microsoft Azure Maps プラットフォームに移行する方法についての分析情報を提供します。 このチュートリアルには、Azure Maps に移行するための比較コード サンプル、移行に関する提案、ベスト プラクティスが含まれています。

## <a name="azure-maps-platform-overview"></a>Azure Maps プラットフォームの概要

Azure Maps は、あらゆる業界の開発者に強力な地理空間機能を提供します。 それらの機能には、Web およびモバイル アプリケーションに地理的コンテキストを提供するために、定期的に更新されるマップ データが搭載されています。 Azure Maps は、Azure One API に準拠した REST API のセットです。 REST API は、Maps のレンダリング、検索、ルート指定、トラフィック、タイム ゾーン、位置情報、ジオフェンシング、マップ データ、天気、モビリティ、空間演算を提供します。 演算は、Web と Android の両方の SDK によって実現されているため、開発が簡単になり、柔軟性が高く、複数のプラットフォーム間で移植性が確保されます。

## <a name="high-level-platform-comparison"></a>プラットフォームのおおまかな比較

次の表は、Google マップの機能とそれに対応する Azure Maps の機能の大まかな一覧です。 この一覧に Azure Maps のすべての機能が網羅されているわけではありません。 Azure Maps のその他の機能には、アクセシビリティ、ジオフェンシング、等時線、空間演算、直接マップ タイル アクセス、バッチ サービス、データ カバレッジの比較 (画像のカバレッジ) などがあります。

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
| Nearest Roads               | ✓                                      |
| Places Search               | ✓                                      |
| Places Details              | 該当なし – Web サイトと電話番号を利用可能 |
| Places Photos               | 該当なし                                    |
| Place Autocomplete          | ✓                                      |
| Snap to Road                | ✓                                      |
| Speed Limits                | ✓                                      |
| Static Maps                 | ✓                                      |
| Static Street View          | 該当なし                                    |
| タイム ゾーン                   | ✓                                      |
| Maps Embedded API           | 該当なし                                    |
| Map URL                    | 該当なし                                    |

Google Maps では、基本的なキーベースの認証が提供されます。 Azure Maps では、基本的なキーベースの認証と Azure Active Directory 認証の両方が提供されます。 Azure Active Directory 認証には、基本的なキーベースの認証と比較して、多くのセキュリティ機能があります。

## <a name="licensing-considerations"></a>ライセンスに関する考慮事項

Google マップから Azure Maps に移行する際は、ライセンスに関して次の点を考慮してください。

- Azure Maps では、対話型マップの使用量に対して料金が請求され、その使用量は、読み込まれたマップ タイルの数が基準となります。 一方、Google マップでは、マップ コントロールの読み込みに対して料金が請求されます。 対話型 Azure Maps SDK では、開発コストを削減するために、マップ タイルが自動的にキャッシュされます。 マップ タイルが 15 個読み込まれるごとに、1 個の Azure Maps トランザクションが生成されます。 対話型 Azure Maps SDK では、512 ピクセルのタイルが使用され、平均でページ ビューあたり 1 個またはそれより少ないトランザクションが生成されます。
- 多くの場合、Google Maps Web サービスの静的マップ画像を Azure Maps Web SDK に置き換えるとよりコスト効果が高くなります。 Azure Maps Web SDK ではマップ タイルが使用されます。 ユーザーがマップをパンしたりズームしたりしない限り、多くの場合、マップの読み込みごとにトランザクションのごく一部のみが生成されます。 Azure Maps Web SDK には、必要に応じてパンとズームを無効にするオプションがあります。 また、Azure Maps Web SDK には、静的なマップ Web サービスよりも非常に多くの視覚化オプションが用意されています。
- Azure Maps を使用すると、データをそのプラットフォームから Azure に格納できます。 また、[利用規約](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)に従って、最大 6 か月間データをキャッシュすることもできます。

Azure Maps のいくつかの関連リソースを次に示します。

- [Azure Maps の価格のページ](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps 使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (Microsoft オンライン サービスの使用条件に含まれています)
- [Azure Maps での適切な価格レベルの選択](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>おすすめの移行プラン

移行プランの概要を次に示します。

1. アプリケーションで使用されている Google マップ SDK とサービスの一覧を作成します。 代わりとなる SDK とサービスが Azure Maps にあることを確認します。
2. [https://azure.com](https://azure.com) で Azure サブスクリプションを作成します (まだお持ちでない場合)。
3. Azure Maps アカウント ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) と認証キーまたは Azure Active Directory ([ドキュメント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)) を作成します。
4. アプリケーション コードを移行します。
5. 移行したアプリケーションをテストします。
6. 移行したアプリケーションを運用環境にデプロイします。

## <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

Azure Maps アカウントを作成し、Azure Maps プラットフォームにアクセスできるようにするには、次の手順を行います。

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure portal](https://portal.azure.com/) にサインインします。
3. [Azure Maps アカウント](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)を作成します。 
4. [Azure Maps サブスクリプション キー](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details)を取得するか、Azure Active Directory 認証を設定し、セキュリティを強化します。

## <a name="azure-maps-technical-resources"></a>Azure Maps の技術リソース

Azure Maps に関する役に立つ技術リソースの一覧を次に示します。

- 概要: [https://azure.com/maps](https://azure.com/maps)
- ドキュメント: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK コード サンプル: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 開発者フォーラム: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- ビデオ: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- ブログ: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- 技術ブログ: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps のフィードバック (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>移行サポート

開発者は、[フォーラム](https://aka.ms/AzureMapsForums) または多くの Azure サポート オプション ([https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)) のいずれかを使用して、移行サポートを探すことができます。

次を利用して Google Maps アプリケーションを移行する方法を習得できます。 

[Android アプリを移行する](migrate-from-google-maps-android-app.md) 

[Web サービスを移行する](migrate-from-google-maps-web-services.md) 

[Web アプリを移行する](migrate-from-google-maps-web-app.md)