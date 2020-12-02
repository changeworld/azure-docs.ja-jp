---
title: Azure Monitor for containers で PV の監視を構成する | Microsoft Docs
description: この記事では、Azure Monitor for containers で永続ボリュームを使用している Kubernetes クラスターの監視を構成する方法について説明します。
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e7c547c137fc84e6e6dfb2807b871ef0329a3c13
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186849"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Azure Monitor for containers で PV の監視を構成する

エージェントのバージョン *ciprod10052020* 以降、Azure Monitor for containers に統合されているエージェントで、PV (永続ボリューム) の使用状況の監視がサポートされるようになりました。

## <a name="pv-metrics"></a>PV メトリック

Azure Monitor for containers では、次のメトリックが 60 秒間隔で収集され **InsightMetrics** テーブルに格納されることで、PV の監視が自動的に開始されます。

|メトリックの名前 |メトリック ディメンション (タグ) |説明 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|特定のポッドによって使用される要求が含まれる特定の永続ボリュームのために使用される領域 (バイト単位)。 `pvCapacityBytes` は、データ インジェスト コストを削減し、クエリを簡略化するために、ディメンションとして [タグ] フィールドに組み込まれています。|

## <a name="monitor-persistent-volumes"></a>永続ボリュームを監視する

Azure Monitor for containers には、すべてのクラスター用のブックの中に、このメトリックの事前構成済みのグラフが含まれています。 **[ワークロードの詳細]** ブックの [永続ボリューム] タブに表示されるグラフを、左側のペインまたは [分析情報] の **[ブックの表示]** ドロップダウンからブックを選択することで、AKS クラスターから直接見つけることができます。 また、PV の使用状況に関して推奨されるアラートを有効にしたり、Log Analytics でこれらのメトリックのクエリを実行したりすることもできます。  

![Azure Monitor での PV ワークロード ブックの例](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>次のステップ

- 収集される PV メトリックの詳細を[こちら](./container-insights-agent-config.md)で確認します。