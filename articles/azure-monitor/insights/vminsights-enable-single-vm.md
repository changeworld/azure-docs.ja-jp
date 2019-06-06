---
title: 評価のために Azure Monitor for VMs (プレビュー) を有効にする | Microsoft Docs
description: この記事では、評価の目的で、1 つの Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs を有効にする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524039"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>評価のために Azure Monitor for VMs (プレビュー) を有効にする

少数の Azure 仮想マシン上で、あるいは 1 つの仮想マシンまたは仮想マシン スケール セット上で Azure Monitor for VMs (プレビュー) を評価する場合、最も簡単で直接的な方法は Azure portal から監視を有効にすることです。 このプロセスを完了すると、それらの監視が正常に開始され、何らかのパフォーマンスや可用性の問題が発生しているかどうかを確認できるようになります。 

開始する前に、必ず[前提条件](vminsights-enable-overview.md)を確認し、お使いのサブスクリプションおよびリソースが要件を満たしていることを確かめてください。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>1 つの Azure VM の監視を有効にする
Azure portal でお使いの Azure VM の監視を有効にするには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[仮想マシン]** を選択します。

1. 一覧から VM を選択します。

1. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

1. **[Insights (プレビュー)]** ページで、 **[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  

    このリストでは、サブスクリプションで仮想マシンがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、[こちら](vminsights-enable-overview.md#log-analytics)に示すサポートされているリージョンのいずれかで「[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)」の手順に従ってください。

監視を有効にした後、仮想マシンの正常性メトリックが表示されるまで、約 10 分かかる場合があります。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>1 つの仮想マシン スケール セットの監視を有効にする

Azure portal で Azure 仮想マシン スケール セットの監視を有効にするには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[仮想マシン スケール セット]** を選択します。

3. 一覧から仮想マシン スケール セットを選択します。

4. [仮想マシン スケール セット] ページの **[監視]** セクションで、 **[インサイト (プレビュー)]** を選択します。

5. **[インサイト (プレビュー)]** ページで、使用する既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。

    このリストでは、サブスクリプションで仮想マシンがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    ![仮想マシン スケール セットに対して Azure Monitor for VMs を有効にする](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、[こちら](vminsights-enable-overview.md#log-analytics)に示すサポートされているリージョンのいずれかで「[Log Analytics ワークスペースの作成](../learn/quick-create-workspace.md)」の手順に従ってください。

監視を有効にした後、スケール セットの監視データが表示されるまで、約 10 分かかる場合があります。

>[!NOTE]
>スケール セットで手動アップグレード モデルを使用している場合、設定を完了するためにインスタンスをアップグレードする必要があります。  これは、 **[設定]** セクションの [インスタンス] ページから実行できます。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>次の手順

仮想マシンまたは仮想マシン スケール セットの監視が有効になったので、Azure Monitor for VMs での分析にこの情報を使用できます。 正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。 VM のパフォーマンスのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関するページを参照してください。または、検出されたアプリケーションの依存関係を表示するには、[VM 用 Azure Monitor のマップの表示](vminsights-maps.md)に関するページを参照してください。