---
title: Resource Health を使用して Azure Data Explorer を監視する
description: Azure Resource Health を使用して Azure Data Explorer のリソースを監視します。
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262062"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Resource Health を使用して Azure Data Explorer を監視する (プレビュー)

Azure Data Explorer の [Resource Health](/azure/service-health/resource-health-overview) では、Azure Data Explorer リソースの正常性を把握し、問題をトラブルシューティングするためのアクションにつながる推奨事項が得られます。 Resource Health は 1 から 2 分おきに更新され、ご利用のリソースについて、現在と過去の正常性がレポートされます。 

Resource Health では、次のようなさまざまな正常性の状態を調べて、Azure Data Explorer リソースの正常性を判定します。
* リソースの可用性
* クエリの失敗

## <a name="access-resource-health-reporting"></a>Access Resource Health のレポート

1. [Azure portal](https://portal.azure.com/) で、サービスの一覧から **[モニター]** を選択します。
1. **[Service Health]**  >  **[Resource health]** の順に選択します。
1. **[サブスクリプション]** ボックスの一覧で、ご使用のサブスクリプションを選択します。 **[リソースの種類]** ボックスの一覧で、 **[Azure Data Explorer]** を選択します。
1. 結果のテーブルに、選択したサブスクリプションのすべてのリソースが一覧表示されます。 それぞれのリソースには、そのリソース正常性を示す正常性状態アイコンが表示されます。
1. リソースを選択して、[リソース正常性状態](#resource-health-status)と推奨事項を表示します。

    ![概要](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>リソースの正常性状態

リソースの正常性は、利用可能、使用不可、不明のいずれかの状態で表示されます。

### <a name="available"></a>利用可能

**利用可能**の正常性状態は、Azure Data Explorer リソースが正常であり、問題がないことを示します。

![利用可能](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>使用不可

**使用不可**の正常性状態は、クエリやインジェストを実行できなくなるような問題が Azure Data Explorer リソースに発生していることを示します。 たとえば、Azure Data Explorer リソースのノードが予期せず再起動されたようなケースが該当します。 Azure Data Explorer リソースが長期間この状態のままである場合は、サポートに問い合わせてください。

![使用不可](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Unknown

**不明**の正常性状態は、**Resource Health** がこの Azure Data Explorer リソースに関する情報を 10 分以上受け取っていないことを示します。 この状態は Azure Data Explorer リソースの正常性を明確に示すものではありませんが、トラブルシューティング プロセスにおいて重要なデータ ポイントです。 Azure Data Explorer クラスターが正常に機能している場合、状態は数分内に**利用可能**に変わります。 **不明**の正常性状態は、プラットフォーム内のイベントの影響をリソースが受けていることを示唆している可能性があります。 

> [!TIP]
> Azure Data Explorer クラスター リソースが停止状態の場合、その正常性は**不明**になります。

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>履歴情報

**[Resource Health]** ペインの **[正常性の履歴]** には、最大 4 週間分のリソース正常性状態情報が表示されます。 このペインでレポートされる正常性イベントの問題については、矢印を選択すると詳しい情報が得られます。 

![履歴](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>次のステップ

* [Resource Health アラートを構成する](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [チュートリアル:Azure Data Explorer で監視データを取り込んでクエリを実行する](ingest-data-no-code.md)
* [メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視](using-metrics.md)