---
title: Azure Cosmos DB に予約割引が適用されるしくみについて | Microsoft Docs
description: Azure Cosmos DB でプロビジョニングされるスループット (RU/秒) に予約割引を適用するしくみについて説明します。
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: cwatson
ms.reviewer: sngun
ms.openlocfilehash: adcd91a8f1b3368d03f4b634e7aef40104d953e3
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393640"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Azure Cosmos DB に予約割引が適用されるしくみについて

Azure Cosmos DB の予約容量を購入すると、予約の属性や数量に合致する Azure Cosmos DB リソースに予約割引が自動的に適用されます。 予約は Azure Cosmos DB リソースにプロビジョニングされるスループットに適用されますが、ソフトウェア、ネットワーク、ストレージ、事前定義コンテナーの料金には適用されません。

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB アカウントに適用される予約割引

予約割引は[プロビジョニングされたスループット](../cosmos-db/request-units.md) (RU/秒) に時間単位で適用されます。 Azure Cosmos DB リソースがまる 1 時間実行されない場合、予約属性に合致する他の Azure Cosmos DB リソースに予約割引が自動的に適用されます。 この割引は、同時に実行されている Azure Cosmos DB リソースに適用できます。 Azure Cosmos DB リソースがまる 1 時間実行されず、予約属性に合致するリソースが他にない場合、その時間については、予約割引の特典が完全に活用されません。

* 割引は階層化されています。つまり、予約の要求ユニットの数が多ければ、それだけ大きな割引が受けられます。  
* 予約購入では、リージョンのオンデマンド価格に等しい率ですべてのリージョンに割引が適用されます。 各リージョンの予約割引率については、この記事の「[リージョン別の予約割引](#reservation-discount-per-region)」セクションを参照してください。

## <a name="reservation-discount-per-region"></a>リージョン別の予約割引
予約割引は、単一サブスクリプションまたは登録済み/アカウント スコープを対象に、時間単位で Azure Cosmos DB スループットに適用されます。 予約割引は、次の比率で、さまざまなリージョンのメーター使用量に適用されます。

|メーターの説明  |リージョン |比率  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - アジア太平洋南東部  |  アジア太平洋南東部    |   1      |
|Azure Cosmos DB - 100 RU/秒/時間 - アジア太平洋東部 |   アジア太平洋東部   |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 北ヨーロッパ|  北ヨーロッパ       |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 韓国南部|    韓国南部     |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 西ヨーロッパ|    西ヨーロッパ     |      1   |
|Azure Cosmos DB - 100 RU/秒/時間 - 韓国中部|   韓国中部    |       1  |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国南部|   英国南部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国西部|   英国西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国北部 |   英国北部    |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国南部 2|   英国南部 2      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国東部 2|  米国東部 2     |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中北部|   米国中北部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部|   米国西部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中部| 米国中部        |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部 2|   米国西部 2      |      1   |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中西部|   米国中西部      |       1  |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国東部|   米国東部      |  1       |
|Azure Cosmos DB - 100 RU/秒/時間 - 南アフリカ北部|     南アフリカ北部    |   1      |
|Azure Cosmos DB - 100 RU/秒/時間 - 南アフリカ西部 |    南アフリカ西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - インド南部|    インド南部     |    1.0375    |
|Azure Cosmos DB - 100 RU/秒/時間 - カナダ東部|   カナダ東部      |    1.1      |
|Azure Cosmos DB - 100 RU/秒/時間 - 東日本|   東日本      |    1.125     |
|Azure Cosmos DB - 100 RU/秒/時間 - 西日本|     西日本    |   1.125       |
|Azure Cosmos DB - 100 RU/秒/時間 - インド西部|     インド西部    |    1.1375     |
|Azure Cosmos DB - 100 RU/秒/時間 - インド中部|    インド中部     |  1.1375       |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア東部|     オーストラリア東部    |   1.15       |
|Azure Cosmos DB - 100 RU/秒/時間 - カナダ中部|  カナダ中部       |   1.2       |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス中部|   フランス中部      |    1.25      |
|Azure Cosmos DB - 100 RU/秒/時間 - ブラジル南部|  ブラジル南部       |   1.5      |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部|   オーストラリア中部      |   1.5      |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部 2| オーストラリア中部 2        |    1.5     |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス南部|    フランス南部     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>予約割引が適用されるしくみを示すシナリオ

予約に次の要件があるとします。

* 必要なスループット: 50,000 RU/秒  
* 使用されるリージョン: 2。 

この場合、2 つのリージョンで 100 RU/秒メーターの数量が 500 になり、RU/秒の合計消費量となる 1 時間あたり 100,000 に対して合計でオンデマンド料金が請求されます。 

**シナリオ 1**

たとえば、"米国中北部" リージョンと "米国西部" リージョンで Azure Cosmos DB デプロイが必要なとき、各リージョンのスループット消費量が 50,000 RU/秒であるとします。 100,000 RU/秒を予約購入すると、オンデマンド料金が完全に相殺されます。

予約で与えられる割引は (スループット消費量 * そのリージョンの予約割引率) として計算されます。 "米国中北部" リージョンと "米国西部" リージョンの場合、予約割引率は "1" です。 そのため、割引される RU/秒の合計は 100,000 RU/秒です (この値は、50,000 * 1 + 50,000 * 1 = 100,000 RU/秒として計算されます)。通常の従量課金制料金では追加の支払がありません。 

|メーターの説明 | リージョン |スループット消費量 (RU/秒) |RU/秒に適用される予約割引 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中北部  |   米国中北部  | 50,000  | 50,000  |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部  |  米国西部   |  50,000  |  50,000 |

**シナリオ 2**

たとえば、"オーストラリア中部 2" リージョンと "フランス南部" リージョンで Azure Cosmos DB デプロイが必要なとき、各リージョンのスループット消費量が 50,000 RU/秒であるとします。 100,000 RU/秒の予約購入は次のように適用されます ("オーストラリア中部 2" の使用量が先に割引されるとします)。

|メーターの説明 | リージョン |スループット消費量 (RU/秒) |RU/秒に適用される予約割引 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部 2  |  オーストラリア中部 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス南部  |  フランス南部   |  50,000 |  15,384  |

"オーストラリア中部 2" リージョンの 50,000 ユニットの使用量は、75,000 RU/秒の課金対象使用量 (または正規化された使用量) に相当します。 この値は (スループット消費量 * そのリージョンの予約割引率) として計算され、75,000 RU/秒 (この値は 50,000 * 1.5 = 75,000 RU/秒として計算されます) の課金対象使用量 (または正規化された使用量) に相当します。 

100,000 RU/秒の予約購入で "オーストラリア中部 2" の 75,000 RU/秒が相殺され、25,000 RU/秒が "フランス南部" リージョンのために残ります。 残りの 25,000 RU/秒から、15,384 RU/秒の予約割引 (この値は 25,000 / 1.625 = 15,384 RU/秒として計算されます) が "フランス南部" リージョンに適用されます。 "フランス南部" リージョンの残りの 34,616 RU/秒は通常の従量課金制料金で請求されます。 

Azure 請求システムでは、予約構成 (たとえば、今回のケースでは "オーストラリア中部 2") に一致し、処理される最初のインスタンスに予約の課金特典が割り当てられます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](../billing/billing-understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

* [Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)  
* [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソースの前払い](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)  
* [Azure の予約の管理](../billing/billing-manage-reserved-vm-instance.md)  
* [従量課金制サブスクリプションの予約使用量について](../billing/billing-understand-reserved-instance-usage.md)  
* [エンタープライズ加入契約の予約使用量について](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。

