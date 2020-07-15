---
title: Azure portal で Azure Monitor for VMs を有効にする
description: 1 つの Azure 仮想マシン上または仮想マシン スケール セット上で Azure Monitor for VMs を評価する方法について説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507060"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Azure portal で単一の VM または VMSS に対して Azure Monitor を有効にする
この記事では、Azure portal を使用して 1 つの仮想マシンまたは仮想マシン スケール セットに対して Azure Monitor for VMs を有効にする方法について説明します。 この手順は、次に対して使用できます。

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc マシン

始める前に、[前提条件](vminsights-enable-overview.md)を読み、サブスクリプションとリソースが要件を満たしていることを確認します。  

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs の有効化

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[仮想マシン]** 、 **[仮想マシン スケール セット]** 、または **[マシン - Azure Arc]** を選択します。

1. 一覧からリソースを選択します。

1. メニューの **[監視]** セクションで、 **[Insights]\(分析情報\)** 、次に **[Enable]\(有効にする\)** を選択します。 次の例は Azure 仮想マシンを示していますが、このメニューは Azure VMSS または Azure Arc の場合も同様です。

    ![VM に対して Azure Monitor for VMs を有効にする](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. VM が Log Analytics ワークスペースにまだ接続されていない場合は、選択するように求められます。 以前に[ワークスペースを作成](../../azure-monitor/learn/quick-create-workspace.md)したことがない場合は、VM または VMSS がサブスクリプションでデプロイされる場所については既定値を選択できます。 このワークスペースは、まだ存在しない場合、作成されて構成されます。

2. 構成が実行されているときに、ステータス メッセージを受け取ります。

    >[!NOTE]
    >スケール セットに手動アップグレード モデルを使用している場合は、インスタンスをアップグレードして設定を完了します。 **[インスタンス]** ページの **[設定]** セクションからアップグレードを開始できます。

    ![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>次のステップ

* 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの使用](vminsights-maps.md)に関する記事を参照してください。 
* VM のパフォーマンスでのボトルネックや全体的な使用率を特定するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。
