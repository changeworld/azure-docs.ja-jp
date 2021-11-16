---
title: Azure Communication Services - Azure Monitor の有効化
titleSuffix: An Azure Communication Services concept document
description: 診断設定を使用して Communications Services のログとメトリックを構成します。
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: f9de0567c3c07a10b780c4932a09e155f0e2ddea
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138653"
---
# <a name="enable-logs-via-diagnostic-settings-in-azure-monitor"></a>Azure Monitor の診断設定でログを有効化する

## <a name="overview"></a>概要

Communications Services では、[Azure Monitor ログ](/azure/azure-monitor/logs/data-platform-logs)と [Azure Monitor メトリック](/azure/azure-monitor/essentials/data-platform-metrics)によって監視と分析の機能が提供されます。 各 Azure リソースには、次の条件を定義する独自の診断設定が必要です。

- 設定で定義されている送信先に送信されるログとメトリック データのカテゴリ。 使用できるカテゴリは、リソースの種類によって異なります。
- ログを送信する 1 つ以上の送信先。 現在の送信先には、Log Analytics ワークスペース、Event Hubs、Azure Storage が含まれます。
- 1 つの診断設定では、各送信先を 1 つだけ定義することができます。 特定の種類の複数の送信先 (たとえば、2 つの異なる Log Analytics ワークスペース) にデータを送信する場合は、複数の設定を作成します。 各リソースには、最大 5 つの診断設定を作成できます。

Azure Monitor リソースを構成して、Communications Services のログとメトリックの作成を開始する手順について以下で説明します。 すべての Azure リソースを横断する診断設定の使用の詳細については、[診断設定](/azure/azure-monitor/essentials/diagnostic-settings)に関するページを参照してください。

以下の手順は、次の Communications Services ログに適用されます。

- [通話概要と通話診断のログ](call-logs-azure-monitor.md) (プレビュー)

## <a name="access-diagnostic-settings"></a>診断設定へのアクセス

Communications Services の診断設定にアクセスするには、まず、Azure portal 内で対象の Communications Services のホーム ページにアクセスします。

:::image type="content" source="media\enable-logging\portal-home-go-to-acs-resource.png" alt-text="Communications Services リソース":::

そこから、左側のナビゲーション ペインの [監視] セクションにある [診断設定] をクリックします。

:::image type="content" source="media\enable-logging\resource-diagnostic-settings-nav.png" alt-text="ナビゲーションの [診断設定]":::

[診断設定の追加] リンクをクリックします (Communications Services で使用できる、ログとメトリックのさまざまなソースに注意してください)。

:::image type="content" source="media\enable-logging\diagnostic-setting-add.png" alt-text="診断設定: カテゴリの詳細":::

## <a name="adding-a-diagnostic-setting"></a>診断設定の追加

次に、診断設定の名前を選択するよう求められます。これは、監視している Azure リソースが多数ある場合に便利です。 また、ログまたはメトリックとして監視する、ログとメトリックのデータ ソースを選択するよう求められます。 違いの詳細については、「[Azure Monitor データ プラットフォーム](/azure/azure-monitor/data-platform)」を参照してください。

:::image type="content" source="media\enable-logging\diagnostic-setting-categories-details-acs.png" alt-text="診断設定の追加":::

## <a name="choose-destinations"></a>保存先の選択

また、ログの保存先を選択するように求められます。 プラットフォーム ログとメトリックは、次の表の送信先に送信できます。Azure Monitor のドキュメント「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)」でも、さらに詳しく説明されています。

| 到着地 | 説明 |
|:------------|:------------|
| [Log Analytics ワークスペース](/azure/azure-monitor/logs/design-logs-deployment) | Log Analytics ワークスペースにログとメトリックを送信すると、強力なログ クエリを使用して、Azure Monitor で収集された他の監視データと組み合わせて分析できるほか、アラートや視覚化などの Azure Monitor の他の機能を使用することもできます。 |
| [Event Hubs](/azure/event-hubs/) | Event Hubs にログとメトリックを送信すると、サードパーティ製の SIEM やその他のログ分析ソリューションなどの外部システムにデータをストリーミングできます。 |
| [Azure Storage アカウント](/azure/storage/blobs/) | Azure ストレージ アカウントにログとメトリックをアーカイブすると、監査、スタティック分析、またはバックアップに役立ちます。 Azure Monitor ログや Log Analytics ワークスペースと比較すると、Azure ストレージはコストが低く、ログを無期限に保持することができます。 |

次の設定は、Communications Services リソース内で表示される内容の例です。

:::image type="content" source="media\enable-logging\diagnostic-setting-destination-acs.png" alt-text="診断設定: 保存先の詳細":::

これらはすべて、特定のストレージ ニーズに適合させることができる柔軟なオプションです。ただし、[Log Analytics ワークスペース] オプションが選択されている場合は、その他の機能や組み込みの分析インサイトが提供されます。

## <a name="log-analytics-workspace-for-additional-analytics-features"></a>追加の分析機能のための Log Analytics ワークスペース

ログの送信先として [Log Analytics ワークスペース](/azure/azure-monitor/logs/log-analytics-overview)を選択すると、Azure Monitor 内で全般的に、また Communications Services に対して有効になる機能が増えます。 Log Analytics は Azure portal 内のツールであり、Azure Monitor のログ、メトリック、[ブック](/azure/azure-monitor/visualize/workbooks-overview)内のデータや、[アラート](/azure/azure-monitor/alerts/alerts-log)、[通知アクション](/azure/azure-monitor/alerts/action-groups)、[REST API アクセス](https://dev.loganalytics.io/)などを使用して[クエリ](/azure/azure-monitor/logs/queries)を作成、編集、実行するために使用します。

Communications Services のログには、便利な[既定のクエリ パック](/azure/azure-monitor/logs/query-packs#default-query-pack)が用意されており、データをすばやく分析して理解するためのインサイトの初期セットが提供されます。 これらのクエリ パックについては、[Communications Services 用の Log Analytics](log-analytics.md) に関するページで説明しています。 ブックを使用したインサイトと視覚化も多数用意されています。これらについては、[Communications Services のログ用のブック](insights.md)に関するページで説明しています。
