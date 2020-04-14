---
title: Azure portal で Azure Monitor for VMs を有効にする
description: 1 つの Azure 仮想マシン上または仮想マシン スケール セット上で Azure Monitor for VMs を評価する方法について説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480710"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Azure portal で Azure Monitor for VMs を有効にする

この記事では、Azure portal を使用して、少数の Azure Virtual Machines (VM) で Azure Monitor for VMs を有効にする方法について説明します。 目標は、VM を監視し、パフォーマンスや可用性の問題を発見することです。 

始める前に、[前提条件](vminsights-enable-overview.md)を読み、サブスクリプションとリソースが要件を満たしていることを確認します。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>1 つの Azure VM の監視を有効にする
Azure VM の監視を有効にするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[仮想マシン]** を選択します。

1. 一覧から VM を選択します。

1. [VM] ページの **[監視]** セクションで、 **[Insights]\(分析情報\)** 、次に **[Enable]\(有効にする\)** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  

    この一覧では、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。 Log Analytics ワークスペースは、[サポートされているリージョン](vminsights-enable-overview.md#log-analytics)のいずれかに属している必要があります。

6. 構成が実行されているときに、ステータス メッセージを受け取ります。

    ![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>1 つの仮想マシン スケール セットの監視を有効にする

Azure 仮想マシン スケール セットの監視を有効にするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[仮想マシン スケール セット]** を選択します。

3. 一覧から仮想マシン スケール セットを選択します。

4. [仮想マシン スケール セット] ページの **[監視]** セクションで、 **[Insights]\(分析情報\)** 、次に **[Enable]\(有効にする\)** を選択します。

5. **[Insights]\(分析情報\)** ページで、既存の Log Analytics ワークスペースを使用する場合は、それをドロップダウン リストから選択します。

    このリストでは、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    ![仮想マシン スケール セットに対して Azure Monitor for VMs を有効にする](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >仮想マシン スケール セットからの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../learn/quick-create-workspace.md)に関するページを参照してください。 Log Analytics ワークスペースは、[サポートされているリージョン](vminsights-enable-overview.md#log-analytics)のいずれかに属している必要があります。

6. 構成が実行されているときに、ステータス メッセージを受け取ります。

    >[!NOTE]
    >スケール セットに手動アップグレード モデルを使用している場合は、インスタンスをアップグレードして設定を完了します。 **[インスタンス]** ページの **[設定]** セクションからアップグレードを開始できます。
    
    ![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

VM または仮想マシン スケール セットの監視が有効になったので、Azure Monitor for VMs での分析に監視情報を使用できます。 

## <a name="next-steps"></a>次のステップ

* 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの使用](vminsights-maps.md)に関する記事を参照してください。 
* VM のパフォーマンスでのボトルネックや全体的な使用率を特定するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。
