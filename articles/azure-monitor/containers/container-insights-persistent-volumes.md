---
title: Container insights で PV の監視を構成する | Microsoft Docs
description: この記事では、Container insights で永続ボリュームを使用している Kubernetes クラスターの監視を構成する方法について説明します。
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713730"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Container insights で PV の監視を構成する

エージェントのバージョン *ciprod10052020* 以降、Container insights に統合されているエージェントで、PV (永続ボリューム) の使用状況の監視がサポートされるようになりました。

## <a name="pv-metrics"></a>PV メトリック

Container insights では、次のメトリックが 60 秒間隔で収集され **InsightMetrics** テーブルに格納されることで、PV の監視が自動的に開始されます。

|メトリックの名前 |メトリック ディメンション (タグ) |説明 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|特定のポッドによって使用される要求が含まれる特定の永続ボリュームのために使用される領域 (バイト単位)。 `pvCapacityBytes` は、データ インジェスト コストを削減し、クエリを簡略化するために、ディメンションとして [タグ] フィールドに組み込まれています。|

## <a name="monitor-persistent-volumes"></a>永続ボリュームを監視する

Container insights には、すべてのクラスター用のブックの中に、このメトリックの事前構成済みのグラフが含まれています。 **[ワークロードの詳細]** ブックの [永続ボリューム] タブに表示されるグラフを、左側のペインまたは [分析情報] の **[ブックの表示]** ドロップダウンからブックを選択することで、AKS クラスターから直接見つけることができます。 また、PV の使用状況に関して推奨されるアラートを有効にしたり、Log Analytics でこれらのメトリックのクエリを実行したりすることもできます。  

![Azure Monitor での PV ワークロード ブックの例](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>次のステップ

- 収集される PV メトリックの詳細を[こちら](./container-insights-agent-config.md)で確認します。
