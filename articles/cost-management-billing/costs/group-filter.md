---
title: Azure Cost Management のグループおよびフィルター オプション
description: この記事では、Cost Management でグループとフィルターのオプションを使用する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 150a79c00003d390f75eb32bc386e826702a173b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997140"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>コスト分析のグループとフィルターのオプション

コスト分析には、多くのグループ化とフィルター処理のオプションがあります。 この記事は、それらを使用する局面を理解するのに役立ちます。

グループ化とフィルター処理のオプションについての動画を視聴するには、[Cost Management のディメンションとタグによるレポート](https://www.youtube.com/watch?v=2Vx7V17zbmk)に関する動画をご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>グループとフィルターに関するプロパティ

次の表は、コスト分析でグループ化とフィルタリングに関して利用できるオプションのうち特によく使うものと、それぞれの使用局面を示したものです。

| プロパティ | 使用する場合 | Notes |
| --- | --- | --- |
| **可用性ゾーン** | AWS のコストを可用性ゾーン別にします。 | AWS スコープと管理グループに対してのみ適用できます。 Azure データには可用性ゾーンが含まれないため、 **[No availability zone]\(可用性ゾーンなし\)** として表示されます。 |
| **請求期間** | PAYG のコストを請求月別にします。 | 請求対象となる PAYG 料金の正確な表現を取得するには、 **[請求期間]** を使用します。 カスタムの日付範囲に絞り込む場合は、請求期間の前後 2 日を余分に追加してください。 請求期間とまったく同じ日付に制限すると、請求書と一致しません。 コストは、請求期間に含まれるすべての請求書から表示されます。 特定の請求書に絞り込むには、 **[請求書 ID]** を使用します。 該当するのは PAYG サブスクリプションのみです。EA と MCA は歴月ごとに請求されます。 EA/MCA アカウントでは、日付の選択や月単位の細分性でカレンダーの月を使用して、同じ目的を実現できます。 |
| **料金タイプ** | 使用、購入、返金、および未使用予約のコストに分割します。 | 予約購入と返金は、実際のコストのときにだけ使用でき、償却コストを使用するときは使用できません。 未使用予約コストは、償却コスト表示のときにのみ使用できます。 |
| **部門** | コストを EA 部門別にします。 | EA および管理グループに対してのみ使用できます。 PAYG サブスクリプションには部門が存在しないため、 **[No department]\(部門なし)** または **[unassigned]\(割り当てなし\)** として表示されます。 |
| **登録アカウント** | EA アカウント所有者でコストを分割します。 | EA の課金アカウント、部門、管理グループに対してのみ使用できます。 PAYG サブスクリプションには EA 登録アカウントが存在しないため、 **[No enrollment account]\(登録アカウントなし\)** または **[unassigned]\(割り当てなし\)** として表示されます。 |
| **頻度** | 使用ベース、1 回限り、および定期的なコストに分割します。 | |
| **請求書 ID** | コストを請求書別にします。 | 未請求の料金にはまだ請求書 ID が存在せず、EA コストに請求書の詳細が含まれていないため、 **[No invoice ID]\(請求書 ID なし\)** として表示されます。  |
| **場所** | コストをリソースの場所またはリージョン別にします。 | 購入と Marketplace の使用は、 **[unassigned]\(割り当てなし\)** または **[No resource location]\(リソースの場所なし\)** として表示される可能性があります。 |
| **測定** | コストを使用量メーター別にします。 | 購入と Marketplace の使用は、 **[No meter]\(測定なし\)** として表示されます。 購入を特定するには「**料金タイプ**」を、Marketplace の料金を特定するには「**パブリッシャーの種類**」を参照してください。 |
| **操作** | AWS コストを操作別にします。 | AWS スコープと管理グループに対してのみ適用できます。 Azure データには操作が含まれないため、 **[No operation]\(操作なし\)** として表示されます。代わりに **[測定]** を使用してください。 |
| **価格モデル** | コストをオンデマンド、予約、またはスポット使用別にします。 | 購入は、"**オンデマンド**" として表示されます。 "**該当なし**" が表示された場合に、その使用量が予約なのか、オンデマンドでの使用量なのかを調べるには、 **[予約]** でグループ化します。また、購入を特定するには **[料金タイプ]** でグループ化します。
| **プロバイダー** | プロバイダーの種類別 (Azure、Microsoft 365、Dynamics 365、AWS など) にコストを分けます。 | 製品と業種の識別子。 |
| **パブリッシャーの種類** | Microsoft、Azure、AWS、Marketplace のコストを分けます。 | 値は、MCA アカウントの場合は **Microsoft**、EA と従量課金制アカウントの場合は **Azure** です。 |
| **予約** | 予約でコストを分割します。 | 予約に関連付けられていない使用または購入は、 **[No reservation]\(予約なし\)** として表示されます。 その他の Azure、AWS、Marketplace の購入を特定するには、 **[パブリッシャーの種類]** でグループ化してください。 |
| **リソース** | リソースでコストを分割します。 | Marketplace での購入は **[Other Marketplace purchases]\(その他の Marketplace での購入\)** として表示され、予約やサポートの料金などの Azure の購入は **[Other Azure purchases]\(その他の Azure の購入\)** として表示されます。 その他の Azure、AWS、または Marketplace での購入を識別するには、 **[パブリッシャーの種類]** でグループ化またはフィルター処理を行います。 |
| **リソース グループ** | リソース グループでコストを分割します。 | 購入、サブスクリプションに関連付けられていないテナント リソース、リソース グループにデプロイされていないサブスクリプション リソース、クラシック リソースにはリソース グループが存在しないため、 **[Other Marketplace purchases]\(その他の Marketplace での購入\)** 、 **[Other Azure purchases]\(その他の Azure の購入\)** 、 **[Other tenant resources]\(その他のテナント リソース\)** 、 **[Other subscription resources]\(その他のサブスクリプション リソース\)** 、 **[$system]** 、または **[その他の料金]** として表示されます。 |
| **リソースの種類** | リソースの種類でコストを分割します。 | 購入とクラシック サービスには Azure Resource Manager リソースの種類がないため、 **[その他]** 、 **[classic services]\(クラシック サービス\)** 、または **[No resource type]\(リソースの種類なし\)** として表示されます。 |
| **サービス名** または **測定カテゴリ** | Azure サービスでコストを分割します。 | 購入と Marketplace の使用は、 **[No service name]\(サービス名なし\)** または **[unassigned]\(割り当てなし\)** として表示されます。 |
| **サービス レベル** または **測定サブカテゴリ** | Azure 利用状況の測定のサブ分類でコストを分割します。 | 購入と Marketplace の使用は、空になるか、 **[unassigned]\(割り当てなし\)** として表示されます。 |
| **サブスクリプション** | コストを Azure サブスクリプションおよび AWS 連結アカウント別にします。 | 購入とテナントのリソースは、 **[No subscription]\(サブスクリプションなし\)** として表示されることがあります。 |
| **Tag** | 特定のタグ キーのタグ値でコストを分割します。 | 購入、サブスクリプションに関連付けられていないテナント リソース、リソース グループにデプロイされていないサブスクリプション リソース、クラシック リソースには、タグを付けることができないため、 **[Tags not available]\(タグの使用不可\)** として表示されます。 使用状況データにタグが含まれていないサービスは、 **[Tags not supported]\(タグはサポート対象外\)** として表示されます。 タグがリソースに指定されていない残りのケースは、 **[タグなし]** として表示されます。 [リソースの種類ごとのタグのサポート](../../azure-resource-manager/management/tag-support.md)に関するページを参照してください。 |

用語について詳しくは、「[Azure の利用状況と請求金額ファイル内の用語について](../understand/understand-usage.md)」をご覧ください。

## <a name="publisher-type-value-changes"></a>パブリッシャーの種類の値の変更

Cost Management では、PublisherType フィールドに、どの製品の料金であるかが示されます (Microsoft、Marketplace、または AWS ([クロス クラウド コネクタ](aws-integration-set-up-configure.md)が構成されている場合))。

変更点

2021 年 10 月 14 日、[Microsoft 顧客契約](../understand/review-customer-agreement-bill.md#check-access-to-a-microsoft-customer-agreement)を使用するすべてのお客様に対して、PublisherType フィールドの値 "Azure" が "Microsoft" に更新されます。 この変更は、Azure 以外の Microsoft 製品 (Microsoft 365 や Dynamics 365 など) をサポートするための今後の機能強化に対応するために行われます。

"Marketplace" と "AWS" の値は変更されません。

この変更は、エンタープライズ契約または従量課金制プランを使用しているお客様には影響しません。

**影響とアクション**
<a name="impact-action"></a>

2021 年 10 月 14 日より前にダウンロードしている Cost Management データについては、PublisherType フィールドの古い "Azure" 値を新しい "Microsoft" 値とみなす必要があります。 データは、エクスポート、使用状況の詳細、または Cost Management からダウンロードされている可能性があります。

PublisherType フィールドを値 "Azure" でフィルター処理する Cost Management + Billing REST API 呼び出しを使用している場合は、この変更に対応するために、October 2021 年 10 月 14 日以降は、新しい値 "Microsoft" でフィルター処理する必要があります。 以後、パブリッシャーの種類 = "Azure" でフィルター処理する API を呼び出しても、データは返されません。

この変更はフィルターに自動的に反映されるため、コスト分析や予算には影響しません。 パブリッシャーの種類 = "Azure" フィルターを使用して作成された保存済みビューまたは予算は、自動的に更新されます。

## <a name="next-steps"></a>次のステップ

- [コストの分析を開始する](./quick-acm-cost-analysis.md)。
