---
title: 自動化を使用した Azure コストの管理
description: この記事では、自動化を使用して Azure コストを管理する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449833"
---
# <a name="manage-costs-with-automation"></a>自動化を使用したコストの管理

Cost Management の自動化を使用して、コスト データの取得と管理を行う一連のカスタム ソリューションをビルドすることができます。 この記事では、Cost Management の自動化の一般的なシナリオと状況に基づいて使用できるオプションについて説明します。 API を使用して開発する場合に、開発プロセスの時間を短縮するために役立つ一般的な API 要求の例を紹介します。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>オフライン分析のためのコスト データの取得を自動化する

ご自分の Azure コスト データをダウンロードし、それを他のデータ セットとマージすることが必要になる場合があります。 また、コスト データを自身のシステムに統合することが必要になる場合もあります。 関連するデータ量に応じて使用できるさまざまなオプションがあります。 どのような場合でも、API とツールを使用するには、適切なスコープでの Cost Management へのアクセス許可が必要です。 詳細については、[データへのアクセスの割り当て](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data)に関するページを参照してください。

## <a name="suggestions-for-handling-large-datasets"></a>大規模なデータ セットの処理に関する推奨事項

組織で、多くのリソースまたはサブスクリプションで Azure の使用率が高い場合、使用状況の詳細データが大量に生成されます。 多くの場合、Excel では、このような大きなファイルを読み込むことはできません。 このような場合、次のオプションをお勧めします。

**Power BI**

Power BI は、大量のデータの取り込みと処理に使用されます。 マイクロソフトエンタープライズ契約のお客様の場合、Power BI テンプレート アプリを使用して、ご使用の課金アカウントのコストを分析することができます。 レポートには、お客様が使用される主要なビューが含まれます。 詳細については、[Power BI テンプレート アプリを使用して Azure のコストを分析する](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)方法に関するページを参照してください。

**Power BI データ コネクタ**

データを毎日分析する必要がある場合、[Power BI データ コネクタ](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)を使用して詳細な分析のためのデータを取得することをお勧めします。 作成するレポートは、より多くのコストが発生すると、コネクタにより最新の状態に維持されます。

**Cost Management のエクスポート**

データを毎日分析する必要がない場合もあります。 その場合は、Cost Management の[エクスポート](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)機能を使用して、Azure Storage アカウントへのデータのエクスポートをスケジュールすることを検討してください。 その後、必要に応じてデータを Power BI に読み込むか、ファイルが十分に小さい場合は Excel でデータを分析できます。 エクスポートは Azure portal でご使用いただけます。また、[Exports API](https://docs.microsoft.com/rest/api/cost-management/exports) を使用してエクスポートを構成することもできます。

**Usage Details API**

コスト データ セットが小さい場合、[Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) の使用を検討してください。 コスト データが大量にある場合は、ある期間の可能な限り最小量の使用量データを要求する必要があります。 そのためには、要求で短い時間範囲を指定するか、フィルターを使用します。 たとえば、3 年分のコスト データを必要とするシナリオでは、この API は、1 回の呼び出しではなく、さまざまな時間範囲に対して複数の呼び出しを行う方がより効果的です。 この後、データを Excel に読み込んで、さらに分析を行うことができます。

## <a name="automate-retrieval-with-usage-details-api"></a>Usage Details API を使用して取得を自動化する

[Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) を使用すると、Azure の請求書に対応する未加工の集計されていないコスト データを簡単に取得できます。 この API は、組織がプログラムによるデータの取得ソリューションを必要とする場合に役立ちます。 小さいコスト データ セットを分析する場合は、この API の使用をご検討ください。 ただし、データ セットが大きい場合は、前述の他のソリューションを使用する必要があります。 Usage Details の詳細データは、1 日あたりの測定ごとに提供されます。 これは、毎月の請求額の計算に使用されます。 この API の一般提供 (GA) バージョンは、`2019-10-01` です。 API を使用して、予約および Azure Marketplace での購入のためにプレビュー バージョンにアクセスするには、`2019-04-01-preview` を使用してください。

### <a name="usage-details-api-suggestions"></a>Usage Details API に関する推奨事項

**要求のスケジュール**

Usage Details API に対する要求は、1 日あたり "_1 回のみ_" 行うことをお勧めします。 コスト データの更新の頻度および丸め処理の方法の詳細については、「[Cost Management のデータを理解する](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule)」を参照してください。

**フィルター処理を行わずに最上位レベルのスコープを対象とする**

この API を使用して、使用可能な最も高いレベルのスコープで、必要なすべてのデータを取得します。 必要なすべてのデータが取り込まれるまで待ってから、フィルター処理、グループ化、または集計分析を行います。 この API は、特に大量の集計されていない未加工のコスト データを提供するために最適化されています。 Cost Management の使用可能なスコープの詳細については、「[スコープを理解して使用する](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes)」を参照してください。 スコープの必要なデータをダウンロードした後、Excel で、フィルターやピボット テーブルを使用してデータを詳細に分析します。

## <a name="example-usage-details-api-requests"></a>Usage Details API に対する要求の例

次の要求例は、Microsoft のお客様が、直面する可能性のある一般的なシナリオに対処するために使用されています。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>特定の日付範囲内のスコープの使用状況の詳細を取得する

要求によって返されるデータは、使用状況が課金システムによって受信された日付に対応します。 これには、複数の請求書のコストが含まれている場合があります。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>償却コストの詳細を取得する

未払いの購入を表示するために実際のコストが必要な場合、次の要求の *metric* を `ActualCost` に変更します。 償却コストと実際のコストを使用する場合、`2019-04-01-preview` バージョンを使用する必要があります。 この API の現在のバージョンは、`2019-10-01` と同様に動作します。ただし、新しい型およびメトリック属性が追加され、プロパティ名が変更されています。 Microsoft 顧客契約をお持ちの場合は、次の例の `startDate` と `endDate` がフィルターになります。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>エクスポートを使用して大規模なコスト データ セットを繰り返し取得する

エクスポート機能は、定期的なコスト データのダンプをスケジュールするためのソリューションです。 この方法は、使用状況のファイルが大きすぎて、Usage Details API を使用してデータを確実に呼び出したり、ダウンロードしたりできない組織が、集計されていないコスト データを取得する場合にお勧めします。 データは、選択した Azure Storage アカウントに格納されます。 そこから、データをお使いのシステムに読み込み、チームで必要に応じて分析します。 Azure portal でエクスポートを構成するには、[データのエクスポート](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)に関するページを参照してください。

## <a name="data-latency-and-rate-limits"></a>データ待機時間とレート制限

この API は、1 日に 1 回だけ呼び出すことをお勧めします。 Cost Management データは、4 時間ごとに Azure リソース プロバイダーから新しい使用状況データを受け取って更新されます。 呼び出しの頻度が増やしても、追加のデータは提供されません。 代わりに、負荷が増加します。 データが変更される頻度と、データ待機時間の処理方法の詳細については、「[Cost Management のデータを理解する](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)」を参照してください。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>エラー コード 429 - 呼び出し回数がレート制限を超えた場合

Cost Management サブスクリプションをお持ちのすべてのユーザーに対して一貫したエクスペリエンスを実現するために、Cost Management API にはレート制限があります。 この制限に達すると、HTTP 状態コード `429: Too many requests` が返されます。 API の現在のスループット制限は、次のとおりです。

- 1 分あたり 30 回の呼び出し - スコープあたりのユーザーあたり、またはアプリケーションあたりの回数です。
- 1 分あたり 200 回の呼び出し - テナントあたりのユーザーあたり、またはアプリケーションあたりの回数です。

## <a name="next-steps"></a>次のステップ

- [Power BI テンプレート アプリを使用して Azure のコストを分析する](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。
- エクスポートを使用して、[データをエクスポートし、管理する](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。
- [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) の詳細情報