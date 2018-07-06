---
title: Azure Log Analytics でのデータ ソースの構成 | Microsoft Docs
description: データ ソースは、Log Analytics がエージェントや接続されている他のソースから収集するデータを定義します。  この記事では、Log Analytics でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 9a43774a5327536ae4fa1346ae933739ea629771
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130086"
---
# <a name="data-sources-in-log-analytics"></a>Log Analytics のデータ ソース
Log Analytics は、接続されたソースからデータを収集して Log Analytics ワークスペースに格納します。  Log Analytics が収集するデータは、構成するデータ ソースによって定義されます。  Log Analytics のデータは、レコード セットとして格納されます。  データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

![Log Analytics のデータ収集](./media/log-analytics-data-sources/overview.png)

データ ソースは[管理ソリューション](log-analytics-add-solutions.md)とは異なり、接続されたソースからのデータの収集と Log Analytics へのレコードの作成も行います。  データの収集に加え、ソリューションには、通常は、アプリケーションやサービスの操作を分析するのに役立つログ検索とビューが含まれます。


## <a name="summary-of-data-sources"></a>データ ソースの概要
次の表に、現在 Log Analytics で使用できるデータ ソースを示します。  各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。   また、Log Analytics へのデータ収集の手法および頻度に関する情報についても提供します。  この記事の情報を利用して、使用可能なさまざまなソリューションを識別し、異なる管理ソリューションのデータ フローと接続要件を把握できます。 列の詳細については、「[Azure での管理ソリューションのデータ収集の詳細](../monitoring/monitoring-solutions-inventory.md)」を参照してください。


| データ ソース | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [カスタム ログ](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 着信時 |
| [カスタム ログ](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 着信時 |
| [IIS ログ](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |ログ ファイル ロールオーバー設定によって異なる |
| [パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| [Windows イベント ログ](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 着信時 |


## <a name="configuring-data-sources"></a>データ ソースの構成
データ ソースは、Log Analytics の **[詳細設定]** の **[データ]** メニューから構成します。  すべての構成は、ワークスペース内の接続されているすべてのソースに配信されます。  現時点では、この構成からエージェントを除外することはできません。

![Windows イベントの構成](./media/log-analytics-data-sources/configure-events.png)

1. Azure Portal で、**[Log Analytics]**、目的のワークスペース、**[詳細設定]** の順に選択します。
2. **[データ]** を選択します。
3. 構成するデータ ソースをクリックします。
4. 構成の詳細については、上記の表のデータ ソース名をクリックして、リンク先のドキュメントを参照してください。


## <a name="data-collection"></a>データ収集
データ ソースの構成は、数分以内に Log Analytics に直接接続されたエージェントに配信されます。  指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Log Analytics に直接配信されます。  詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。  このエージェントは、他のエージェントと同じように管理パックをダウンロードし、指定されたデータを収集します。 データは、そのソースに応じて、管理サーバーに送信されてそこから Log Analytics に転送されるか、エージェントが管理サーバーを介さずに Log Analytics に送信します。 詳細については、「[データ収集の詳細](log-analytics-add-solutions.md#data-collection-details)」を参照してください。  Operations Manager および Log Analytics への接続と構成の配信頻度の変更の詳細については、[System Center Operations Manager との統合の構成](log-analytics-om-agents.md)に関するページを参照してください。

エージェントが Log Analytics または Operations Manager に接続できない場合は、そのままデータの収集を続け、接続が確立されたときにデータを送信します。  データの量がクライアントの最大キャッシュ サイズに達した場合、またはエージェントが 24 時間以内に接続を確立できなかった場合は、データが失われることがあります。

## <a name="log-analytics-records"></a>Log Analytics のレコード
Log Analytics によって収集されたすべてのデータは、レコードとしてワークスペースに保存されます。  さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、 **Type** プロパティによって識別されます。  各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。

## <a name="next-steps"></a>次の手順
* Log Analytics に機能を追加し、ワークスペースにデータを収集する[ソリューション](log-analytics-add-solutions.md)について学習します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* データ ソースやソリューションから収集された重要なデータについて事前に通知する[アラート](log-analytics-alerts.md)を構成します。
