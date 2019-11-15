---
title: Azure Cost Management のデータを理解する | Microsoft Docs
description: この記事では、Azure Cost Management に含まれるデータと、それが処理、収集、表示、およびクローズされる頻度について詳しく説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721360"
---
# <a name="understand-cost-management-data"></a>Cost Management のデータを理解する

この記事では、Azure Cost Management に含まれる Azure のコストと使用状況のデータについて詳しく説明します。 また、データが処理、収集、表示、クローズされる頻度についても説明します。 お客様は、Azure の使用量に対して毎月課金されます。 請求期間は月単位ですが、期間の開始日と終了日はサブスクリプションの種類によって異なります。 Cost Management が使用状況データを受信する頻度は、さまざまな要因に基づいて決まります。 このような要因には、データの処理にかかる時間や、Azure サービスから請求システムに使用状況が送信される頻度などがあります。

Cost Management には、すべての使用量と購入の他に、Enterprise Agreement (EA) アカウント向けの予約とサード パーティ製品が含まれています。 従量課金制料金の Microsoft 顧客契約アカウントと個々のサブスクリプションには、Azure と Marketplace のサービスの使用量のみが含まれます。 サポート コストとその他のコストは含まれません。 コストは請求書が生成されるまで推定され、クレジットは考慮されません。

## <a name="supported-microsoft-azure-offers"></a>サポートされている Microsoft Azure プラン

Azure Cost Management で現在サポートされている [Microsoft Azure のプラン](https://azure.microsoft.com/support/legal/offer-details/)を次に示します。 Azure プランとは、ご利用の Azure サブスクリプションの種類です。 データは、 **[データ利用可能開始日]** の日付から Cost Management で使用できます。 サブスクリプションでプランが変更された場合、プラン変更日前のコストは使用できません。

| **カテゴリ**  | **プラン名** | **クォータ ID** | **プラン番号** | **データ利用可能開始日** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 年 5 月<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 年 5 月<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure エンタープライズ](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 年 5 月<sup>1</sup> |
| **Microsoft 顧客契約** | [Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 該当なし | 2019 年 3 月<sup>3</sup> |
| **Microsoft 顧客契約** | [Dev/Test 用 Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 該当なし | 2019 年 3 月<sup>3</sup> |
| **パートナーによってサポートされる Microsoft 顧客契約** | Microsoft Azure プラン | CSP_2015-05-01、CSP_MG_2017-12-01、および CSPDEVTEST_2018-05-01<br><br>クォータ ID は、Microsoft 顧客契約および従来の CSP サブスクリプションで再利用されます。 現時点では、Microsoft 顧客契約サブスクリプションのみがサポートされています。 | 該当なし | 2019 年 10 月 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | [Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018 年 10 月 2 日<sup>2</sup> |
| **従量課金制** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise:BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018 年 10 月 2 日<sup>2</sup> |

_<sup>**1**</sup> 2014 年 5 月よりも前のデータについては、[Azure エンタープライズ ポータル](https://ea.azure.com)にアクセスしてください。_

_<sup>**2**</sup> 2018 年 10 月 2 日よりも前のデータについては、[Azure アカウント センター](https://account.azure.com/subscriptions)にアクセスしてください。_

_<sup>**3**</sup> Microsoft 顧客契約は 2019 年 3 月に開始したため、これ以前の履歴データはありません。_

_<sup>**4**</sup>クレジットベースの前払い制サブスクリプションの履歴データは、請求書と一致しない場合があります。以下の「[履歴データが請求書と一致しない場合がある](#historical-data-might-not-match-invoice)」をご覧ください。_

次のプランはまだサポートされていません。

| Category  | **プラン名** | **クォータ ID** | **プラン番号** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany 従量課金制](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **クラウド ソリューション プロバイダー (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **クラウド ソリューション プロバイダー (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **クラウド ソリューション プロバイダー (CSP)** | Azure Germany in CSP (Microsoft Cloud Germany 用)   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **従量課金制**                 | Microsoft Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **従量課金制** | [学生向け Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **従量課金制**                 | [Microsoft Azure スポンサー プラン](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **サポート プラン** | Standard サポート                    | Default_2014-09-01 | MS-AZR-0041P |
| **サポート プラン** | Professional Direct サポート         | Default_2014-09-01 | MS-AZR-0042P |
| **サポート プラン** | Developer サポート                   | Default_2014-09-01 | MS-AZR-0043P |
| **サポート プラン** | Germany サポート プラン                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **サポート プラン** | Azure Government Standard サポート   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **サポート プラン** | Azure Government Pro-Direct サポート | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **サポート プラン** | Azure Government Developer サポート  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>オファーの種類を決定する
サブスクリプションのデータが表示されず、ご利用のサブスクリプションがサポート対象のプランに該当するかどうかわからない場合は、ご利用のサブスクリプションがサポート対象かどうかを検証することができます。 Azure サブスクリプションがサポート対象かどうかを検証するには、[Azure portal](https://portal.azure.com) にサインインします。 次に、左側のメニュー ウィンドウにある **[すべてのサービス]** を選択します。 サービスの一覧で **[サブスクリプション]** を選択します。 [サブスクリプション] の一覧で、検証するサブスクリプションを選択します。 選択したサブスクリプションが [概要] タブに表示され、**プラン**と**プラン ID** を確認できます。 次に例を示します。

![プランとプラン ID が表示された、サブスクリプションの [概要] タブの例](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management に含まれるコスト

Cost Management に含まれるデータと含まれないデータを次の表に示します。 すべてのコストは、請求書が生成されるまで推定されます。 示されているコストには、無料クレジットおよびプリペイド クレジットは含まれません。

**コストと使用状況データ**

| **含まれる** | **含まれない** |
| --- | --- |
| Azure サービスの使用状況<sup>5</sup>        | サポート料金 - 詳細については、[請求書の用語の説明](../billing/billing-understand-your-invoice.md)に関する記事を参照してください。 |
| Marketplace サービスの使用状況<sup>6</sup> | 税金 - 詳細については、[請求書の用語の説明](../billing/billing-understand-your-invoice.md)に関する記事を参照してください。 |
| マーケットプレースでの購入<sup>6</sup>      | クレジット - 詳細については、[請求書の用語の説明](../billing/billing-understand-your-invoice.md)に関する記事を参照してください。 |
| 予約購入<sup>7</sup>      |  |
| 予約購入の償却額<sup>7</sup>      |  |

_<sup>**5**</sup> Azure サービスの使用状況は、予約および交渉済みの価格に基づきます。_

_<sup>**6**</sup> マーケットプレースでの購入は、現時点では従量課金制、MSDN、Visual Studio プランではご利用いただけません。_

_<sup>**7**</sup> 現時点では、予約購入はエンタープライズ契約 (EA) アカウントでのみご利用いただけます。_

**Metadata**

| **含まれる** | **含まれない** |
| --- | --- |
| リソース タグ<sup>8</sup> | リソース グループのタグ |

_<sup>**8**</sup> リソース タグは、各サービスから使用状況が送信される際に適用されます。過去の使用状況に対してさかのぼって適用することはできません。_

## <a name="rated-usage-data-refresh-schedule"></a>評価済み使用状況データの更新スケジュール

コストと使用状況データは、Azure portal の [コストの管理と請求] および[サポートする API](index.yml) から利用できます。 コストを確認するときは、次の点に留意してください。

- 現在の請求期間の見積もり料金は、1 日に 6 回更新されます。
- 現在の請求期間の見積もり料金は、使用量の増加に伴い変更される可能性があります。
- 各更新は累積的であるため、すべての明細項目と、以前の更新からの情報が含まれます。
- Azure では、現在の請求期間の終了後 72 時間 (3 カレンダー日) 以内に請求期間が確定 ("_クローズ_") します。

次の例は、請求期間が終了するタイミングを示しています。

Enterprise Agreement (EA) サブスクリプション – 請求月が 3 月 31 日に終了する場合、料金の見積もりが更新されるのは最大で 72 時間後になります。 この例では、4 月 4 日午前 0 時 (UTC) です。

従量課金制サブスクリプション – 請求月が 5 月 15 日に終了する場合、料金の見積もりが更新されるのは最大で 72 時間後になる可能性があります。 この例では、5 月 19 日午前 0 時 (UTC) です。

### <a name="rerated-data"></a>データの再評価

[Cost Management API](index.yml)、Power BI、Azure portal のどの方法でデータを取得する場合でも、請求書がクローズされるまでは、現在の請求期間の料金が再評価され、その結果変更される可能性があります。

## <a name="cost-management-data-fields"></a>Cost Management のデータ フィールド

使用状況の詳細ファイルと Cost Management API には、次のデータ フィールドがあります。 次の太字のフィールドについては、パートナーはコスト分析でフィルターとグループ化の機能を使用して、複数のフィールドでコストを分析できます。 太字のフィールドは、パートナーがサポートしている Microsoft 顧客契約にのみ適用されます。

| **フィールド名** | **説明** |
| --- | --- |
| invoiceld | 特定の取引の請求書に表示される請求書 ID。 |
| previousInvoiceID | 返金 (負のコスト) がある、元の請求書への参照。 返金がある場合にのみ設定されます。 |
| billingAccountName | パートナーを表す課金アカウントの名前。 Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客のすべてのコストを計上します。 |
| billingAccountID | パートナーを表す課金アカウントの ID。 |
| billingProfileID | Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客の全請求書のコストを 1 つの請求通貨でグループ化するための請求プロファイルの ID。 |
| billingProfileName | Microsoft 顧客契約にオンボードした顧客と、SaaS、Azure Marketplace、予約などのエンタイトルメント購入を行った CSP 顧客の全請求書のコストを 1 つの請求通貨でグループ化するための請求プロファイルの名前。 |
| invoiceSectionName | 請求書で課金されているプロジェクトの名前。 パートナーによってオンボードされた Microsoft 顧客契約には適用されません。 |
| invoiceSectionID | 請求書で課金されているプロジェクトの識別子。 パートナーによってオンボードされた Microsoft 顧客契約には適用されません。 |
| **CustomerTenantID** | 顧客のサブスクリプションの Azure Active Directory テナントの識別子。 |
| **CustomerName** | 顧客のサブスクリプションの Azure Active Directory テナントの名前。 |
| **CustomerTenantDomainName** | 顧客のサブスクリプションの Azure Active Directory テナントのドメイン名。 |
| **PartnerTenantID** | パートナーの Azure Active Directory テナントの識別子。 |
| **PartnerName** | パートナーの Azure Active Directory テナントの名前。 |
| **ResellerMPNID** | サブスクリプションに関連付けられているリセラーの MPNID。 |
| costCenter | サブスクリプションに関連付けられているコスト センター。 |
| billingPeriodStartDate | 請求書に示されている請求期間の開始日。 |
| billingPeriodEndDate | 請求書に示されている請求期間の終了日。 |
| servicePeriodStartDate | 料金についてサービス使用量の評価が行われた評価期間の開始日。 Azure サービスの料金は、評価期間に対して決定されます。 |
| servicePeriodEndDate | 料金についてサービス使用量の評価が行われた期間の終了日。 Azure サービスの料金は、評価期間に基づいて決定されます。 |
| date | Azure の消費データについては、評価された使用日が表示されます。 予約済みインスタンスについては、購入日が表示されます。 定期的な料金と、Marketplace やサポートなどの 1 回限りの料金については、購入日が表示されます。 |
| productID | 消費または購入によって料金が発生した製品の識別子。 これは、パートナー センターに示されている、productID と SKuID の連結キーです。 |
| product | 請求書に示されている、消費または購入によって料金が発生した製品の名前。 |
| serviceFamily | 購入または課金された製品のサービス ファミリが表示されます。 たとえば、Storage や Compute など。 |
| productOrderID | サブスクリプションが属している資産または Azure プラン名の識別子。 たとえば、Azure Plan など。 |
| productOrderName | サブスクリプションが属している Azure プランの名前。 たとえば、Azure Plan など。 |
| consumedService | 従来の EA 使用量の詳細で使用される消費済みサービス (レガシ分類)。 |
| meterID | 測定された使用量の測定の ID。 |
| meterName | 測定された使用量の測定の名前を識別します。 |
| meterCategory | 使用量の最上位レベルのサービスを識別します。 |
| meterSubCategory | 料金に影響する可能性のある Azure サービスの種類またはサブカテゴリを定義します。 |
| meterRegion | データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
| サブスクリプション ID | Microsoft が生成する、Azure サブスクリプションの一意の識別子。 |
| subscriptionName | Azure サブスクリプションの名前。 |
| 期間 | プランの有効期間を表示します。 たとえば、予約インスタンスには、予約インスタンスの年間期間の 12 か月が表示されます。 1 回限りの購入または定期的な購入の場合、期間には、SaaS、Azure Marketplace、サポートについて 1 か月が表示されます。 Azure の消費には適用されません。 |
| publisherType (firstParty、thirdPartyReseller、thirdPartyAgency) | 発行元をファースト パーティ、サード パーティ リセラー、またはサード パーティ機関として識別する発行元の種類。 |
| partNumber | 未使用の予約インスタンスと Azure Marketplace サービスの部品番号。 |
| publisherName | Microsoft またはサードパーティの発行元を含む、サービスの発行元の名前。 |
| reservationId | 予約インスタンス購入の識別子。 |
| reservationName | 予約インスタンスの名前。 |
| reservationOrderId | 予約インスタンスの OrderID。 |
| frequency | 予約インスタンスの支払い頻度。 |
| resourceGroup | ライフサイクル リソース管理に使用される Azure リソース グループの名前。 |
| instanceID (または) ResourceID | リソース インスタンスの識別子。 |
| resourceLocation | リソースの場所の名前。 |
| Location | リソースの正規化された場所。 |
| effectivePrice | 価格設定通貨でのサービスの実効単価。 製品、サービス ファミリ、測定、オファーに対して一意です。 課金アカウントの価格シートの価格で使用されます。 階層化された価格または含まれている数量がある場合は、使用量のブレンド価格が表示されます。 |
| Quantity | 購入または消費された測定量。 請求期間中に使用された測定の量。 |
| unitOfMeasure | サービスが課金される単位を特定します。 たとえば、GB や時間数など。 |
| pricingCurrency | 単価を定義する通貨。 |
| billingCurrency | 請求コストを定義する通貨 |
| chargeType | 購入や返金など、Azure Cost Management でコストが表す料金の種類を定義します。 |
| costinBillingCurrency | 請求通貨での税引き前の ExtendedCost またはブレンド コスト。 |
| costinPricingCurrency | 価格に関連する価格通貨での税引き前の ExtendedCost またはブレンド コスト。 |
| **costinUSD** | 税引き前の、予測される ExtendedCost またはブレンド コスト (米国ドル)。 |
| **paygCostInBillingCurrency** | 価格が小売価格の場合のコストを表示します。 従量課金制の料金を請求通貨で表示します。 RBAC スコープでのみ使用できます。 |
| **paygCostInUSD** | 価格が小売価格の場合のコストを表示します。 従量課金制の料金を米国ドルで表示します。 RBAC スコープでのみ使用できます。 |
| exchangeRate | 価格通貨から請求通貨への換算に使用される為替レート。 |
| exchangeRateDate | 価格通貨から請求通貨への換算に使用される為替レートの日付。 |
| isAzureCreditEligible | コストが Azure クレジットでの支払い対象かどうかを示します。 |
| serviceInfo1 | これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 |
| serviceInfo2 | これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 |
| additionalInfo | サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。 |
| tags | ユーザーが測定に割り当てるタグです。 タグは、課金記録のグループ化に使用できます。 たとえば、タグを使用して、メーターを使用する部門ごとにコストを配分することができます。 |
| **partnerEarnedCreditRate** | パートナー管理者リンク アクセスに基づいたパートナー獲得クレジット (PEC) がある場合に適用される割引率。 |
| **partnerEarnedCreditApplied** | パートナー獲得クレジットが適用されているかどうかを示します。 |


## <a name="usage-data-update-frequency-varies"></a>使用状況データの更新頻度は一定ではない

Cost Management に使用状況データが表示されるタイミングは、次のような複数の要因によって決まります。

- Azure サービス (Storage、Compute、CDN、SQL など) から使用状況が送信される頻度。
- 評価エンジンとコスト管理パイプラインを通じて使用状況データが処理されるまでの時間。

一部のサービスは、他よりも頻繁に使用状況を送信します。 そのため、一部のサービスのデータは、データの送信頻度が少ない他のサービスよりも早く Cost Management に表示されることがあります。 通常、サービスの使用状況が Cost Management に表示されるまでに 8-24 時間かかります。 更新は累積的であるため、使用量の増加に伴い、その月のデータが更新されることに注意してください。

## <a name="historical-data-might-not-match-invoice"></a>履歴データが請求書と一致しない場合がある

クレジットベースの前払い制オファーの履歴データは、請求書と一致しない場合があります。 Azure 従量課金制、MSDN、Visual Studio の一部のプランでは、Azure クレジットと前払いを請求書に適用できます。 しかし、Cost Management に示される履歴データは、従量課金による推定請求金額のみに基づきます。 Cost Management の履歴データには、支払いとクレジットは含まれていません。 その結果、以下のオファーに対して示される履歴データが、請求書と完全に一致しない場合があります。

- 学生向け Azure (MS-AZR-0170P)
- Azure イン オープン プラン (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P、MS-AZR-0123P、MS-AZR-0125P、MS-AZR-0128P、MS-AZR-0129P)
- 無料試用版 (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P、MS-AZR-0059P、MS-AZR-0060P、MS-AZR-0063P、MS-AZR-0064P)

## <a name="see-also"></a>関連項目

- Cost Management の最初のクイック スタートをまだ完了していない場合は、[コスト分析の開始](quick-acm-cost-analysis.md)に関する記事をご覧ください。
