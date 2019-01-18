---
title: Log Analytics でエージェントのデータ ソースを構成する | Microsoft Docs
description: データ ソースでは、Log Analytics がエージェントや接続されている他のソースから収集するログ データが定義されます。  この記事では、Log Analytics でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: d9bedeeb2e354dab8bc6a7be56826f28914326be
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101532"
---
# <a name="agent-data-sources-in-log-analytics"></a>Log Analytics でのエージェントのデータ ソース
Log Analytics でエージェントから収集されるデータは、構成されたデータ ソースによって定義されます。  エージェントからのデータは、一連のレコードを含んだ[ログ データ](data-collection.md)として格納されます。  データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

![ログ データ コレクション](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>データ ソースの概要
次の表に、現在 Log Analytics で使用できるエージェントのデータ ソースを示します。  各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。   また、収集の手法および頻度に関する情報についても提供します。 


| データ ソース | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [カスタム ログ](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 着信時 |
| [カスタム ログ](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 着信時 |
| [IIS ログ](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |ログ ファイル ロールオーバー設定によって異なる |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [パフォーマンス カウンター](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| [Windows イベント ログ](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 着信時 |


## <a name="configuring-data-sources"></a>データ ソースの構成
データ ソースは、ワークスペースの **[詳細設定]** の **[データ]** メニューから構成します。  すべての構成は、ワークスペース内の接続されているすべてのソースに配信されます。  現時点では、この構成からエージェントを除外することはできません。

![Windows イベントの構成](./media/agent-data-sources/configure-events.png)

1. Azure Portal で、**[Log Analytics]**、目的のワークスペース、**[詳細設定]** の順に選択します。
2. **[データ]** を選択します。
3. 構成するデータ ソースをクリックします。
4. 構成の詳細については、上記の表のデータ ソース名をクリックして、リンク先のドキュメントを参照してください。


## <a name="data-collection"></a>データ収集
データ ソースの構成は、数分以内に Log Analytics に直接接続されたエージェントに配信されます。  指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Log Analytics に直接配信されます。  詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。  このエージェントは、他のエージェントと同じように管理パックをダウンロードし、指定されたデータを収集します。 データは、そのソースに応じて、管理サーバーに送信されてそこから Log Analytics に転送されるか、エージェントが管理サーバーを介さずに Log Analytics に送信します。 詳しくは、[Azure における監視ソリューションのデータ収集の詳細](../../azure-monitor/insights/solutions-inventory.md)に関するページを参照してください。  Operations Manager および Log Analytics への接続と構成の配信頻度の変更の詳細については、[System Center Operations Manager との統合の構成](../../log-analytics/log-analytics-om-agents.md)に関するページを参照してください。

エージェントが Log Analytics または Operations Manager に接続できない場合は、そのままデータの収集を続け、接続が確立されたときにデータを送信します。  データの量がクライアントの最大キャッシュ サイズに達した場合、またはエージェントが 24 時間以内に接続を確立できなかった場合は、データが失われることがあります。

## <a name="log-records"></a>ログ レコード
Log Analytics によって収集されたすべてのデータは、レコードとしてワークスペースに保存されます。  さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、 **Type** プロパティによって識別されます。  各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。

## <a name="next-steps"></a>次の手順
* Azure Monitor に機能を追加し、Azure Monitor ワークスペース内にデータを収集する[監視ソリューション](../../azure-monitor/insights/solutions.md)について学習します。
* [ログ クエリ](../../log-analytics/log-analytics-queries.md)について学習し、データ ソースと監視ソリューションから収集されたデータを分析します。  
* データ ソースや監視ソリューションから収集された重要なデータについて事前に通知する[アラート](../../monitoring-and-diagnostics/monitoring-overview-alerts.md)を構成します。
