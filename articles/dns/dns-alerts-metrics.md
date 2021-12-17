---
title: メトリックとアラート - Azure DNS
description: このラーニング パスでは、Azure DNS のメトリックとアラートについて説明します。
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: f2eaac432673682b075763c6ce9fe9426ed77a70
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2021
ms.locfileid: "108017575"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS メトリックとアラート

Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 この記事では、Azure DNS サービスのメトリックとアラートについて説明します。

## <a name="azure-dns-metrics"></a>Azure DNS メトリック

Azure DNS は、DNS ゾーンの特定の側面を監視するためのメトリックを提供しています。 Azure DNS のメトリックを使用すると、満たされる条件に基づいてアラートを構成できます。 提供されるメトリックでは、[Azure Monitor サービス](../azure-monitor/index.yml)を使用してデータが表示されます。 メトリックス エクスプローラー エクスペリエンスおよびグラフ作成機能の詳細については、「[Azure Monitor メトリックス エクスプローラー](../azure-monitor/essentials/metrics-charts.md)」を参照してください。 
 
Azure DNS では、DNS ゾーン用の Azure Monitor に対して次のメトリックが提供されます。

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

詳細については、[メトリックの定義](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones)に関する記事を参照してください。

>[!NOTE]
> 現時点では、Azure DNS でホストされているパブリック DNS ゾーンのメトリックのみが提供されています。 Azure DNS でホストされているプライベート ゾーンがある場合は、これらのメトリックによって、プライベート ゾーンに関するデータは提供されません。 さらに、メトリックとアラート機能は、Azure パブリック クラウドに対してのみサポートされています。 ソブリン クラウドのサポートは今後提供される予定です。 

メトリックを表示できる最も細かい要素では、DNS ゾーンです。 現在、ゾーン内の個々のリソース レコードのメトリックは表示できません。

### <a name="query-volume"></a>クエリ量

"*クエリ量*" メトリックは、DNS ゾーン用の Azure DNS が受信した DNS クエリの数を示します。 測定単位は `Count` で、集計は一定期間中に受信したすべてのクエリの `Sum` です。

このメトリックを表示するには、Azure portal の **[監視]** ページから **[メトリック]** エクスプローラー エクスペリエンスを選択します。 DNS ゾーンにスコープを設定し、 **[適用]** を選択します。 *[メトリック]* のドロップダウンで `Query Volume` を選択し、 *[集計]* のドロップダウンで `Sum` を選択します。

![クエリ量](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*図: Azure DNS のクエリ量メトリック*

### <a name="record-set-count"></a>レコード セット数

*レコード セット数* メトリックは、DNS ゾーンの Azure DNS 内にあるレコード セットの数を示します。 ゾーン内で定義されているすべてのレコード セットが集計されます。 測定単位は `Count` で、集計はすべてのレコードセットの `Maximum` です。

このメトリックを表示するには、Azure portal の **[監視]** タブから **[メトリック]** エクスプローラー エクスペリエンスを選択します。 DNS ゾーンにスコープを設定し、 **[適用]** を選択します。 *[メトリック]* のドロップダウンで `Query Volume` を選択し、 *[集計]* のドロップダウンで `Sum` を選択します。

**[リソース]** ドロップダウン リストから DNS ゾーンを選択し、**レコード セット数** メトリックを選択してから、 **[集計]** として **[最大数]** を選択します。 

![レコード セット数](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*図: Azure DNS のレコード セット数メトリック*

### <a name="record-set-capacity-utilization"></a>レコード セットの容量使用率

"*レコード セットの容量使用率*" メトリックは、DNS ゾーンのレコードセット容量の使用率 (パーセント) を示します。 各 Azure DNS ゾーンには、ゾーンで許可されるレコードセットの最大数を定義するレコードセット制限があります。 詳細については、[DNS の制限](dns-zones-records.md#limits)に関するセクションを参照してください。 測定単位は `Percentage` で、集計タイプは `Maximum` です。

たとえば、DNS ゾーンで 500 個のレコードセットが構成されており、そのゾーンの既定のレコードセット制限が 5000 個であるとします。 RecordSetCapacityUtilization メトリックでは、値として 10% が示されます。これは、500 を 5000 で除算することで得られます。 

このメトリックを表示するには、Azure portal の **[監視]** タブから **[メトリック]** エクスプローラー エクスペリエンスを選択します。 DNS ゾーンにスコープを設定し、 **[適用]** を選択します。 *[メトリック]* のドロップダウンで `Record Set Capacity Utilization` を選択し、 *[集計]* のドロップダウンで `Sum` を選択します。 

![メトリックを表示する方法の例を示すスクリーンショット。](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*図: Azure DNS の レコード セット容量使用率メトリック*

## <a name="alerts-in-azure-dns"></a>Azure DNS のアラート

Azure Monitor には、使用可能なメトリック値ごとに構成できるアラートがあります。 詳細については、[Azure Monitor アラート](../azure-monitor/alerts/alerts-metric.md)に関するページを参照してください。

1. Azure DNS ゾーンに対してアラートを構成するには、Azure portal の *[監視]* ページで **[アラート]** を選択します。 次に、**[+ 新しいアラート ルール]** を選択します。

    :::image type="content" source="./media/dns-alerts-metrics/alert-metrics.png" alt-text="[監視] ページの [アラート] ボタンのスクリーンショット。":::


1. [スコープ] セクションの **[リソースの選択]** リンクをクリックして、 *[リソースの選択]* ページを開きます。 **[DNS ゾーン]** でフィルター処理し、ターゲット リソースとして使用する Azure DNS ゾーンを選択します。 ゾーンを選択したら、 **[完了]** を選択します。

    :::image type="content" source="./media/dns-alerts-metrics/select-resource.png" alt-text="アラートの構成での [リソースの選択] ページのスクリーンショット。":::

1. 次に、[条件] セクションの **[条件の追加]** リンクをクリックして、 *[Select a signal]\(シグナルの選択\)* ページを開きます。 アラートの構成対象として、3 種類の "*メトリック*" シグナルのいずれかを選択します。

    :::image type="content" source="./media/dns-alerts-metrics/select-signal.png" alt-text="[シグナルの選択] ページで利用可能なメトリックのスクリーンショット。":::

1. *[シグナル ロジックの構成]* ページで、選択したメトリックの評価のしきい値と頻度を構成します。

    :::image type="content" source="./media/dns-alerts-metrics/configure-signal-logic.png" alt-text="[シグナル ロジックの構成] ページのスクリーンショット。":::

1. アラートによるトリガーで通知を送信するか、アクションを呼び出すには、 **[アクション グループの追加]** をクリックします。 *[アクション グループの追加]* ページで、 **[+ アクション グループの作成]** を選択します。 詳細については、[アクション グループ](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

1. *[アラート ルール名]* を入力し、 **[アラート ルールの作成]** を選択して構成を保存します。

    :::image type="content" source="./media/dns-alerts-metrics/create-alert-rule.png" alt-text="[アラート ルールの作成] ページのスクリーンショット。":::

Azure Monitor メトリックのアラートを構成する方法の詳細については、[Azure Monitor を使用してアラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)を参照してください。 

## <a name="next-steps"></a>次のステップ

- [Azure DNS](dns-overview.md) の詳細を学習する。
