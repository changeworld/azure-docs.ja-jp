---
title: Azure Automation と Log Analytics ワークスペースのマッピング
description: この記事では、ソリューションをサポートする、Automation アカウントと Log Analytics ワークスペース間で許可されているマッピングについて説明します
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 607cebca3e6e8ddd95900ecdbd7041e5f7bb50cc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165775"
---
# <a name="workspace-mappings"></a>ワークスペースのマッピング

Update Management、Change Tracking と Inventory、Start/Stop VMs during off-hours などを有効にする場合、サブスクリプションでの Log Analytics ワークスペースと Automation アカウントのリンクには、特定のリージョンのみがサポートされます。 このマッピングは Automation アカウントと Log Analytics ワークスペースのみに適用されます。 Log Analytics ワークスペースと Automation アカウントは同じサブスクリプションに含まれている必要がありますが、同じリージョンにデプロイされている別のリソース グループに存在することができます。

詳細については、[Log Analytics ワークスペースと Automation アカウント](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)に関する記事を参照してください。

## <a name="supported-mappings"></a>サポートされるマッピング

サポートするマッピングを次の表に示します。

|**Log Analytics ワークスペース リージョン**|**Azure Automation リージョン**|
|---|---|
|**米国**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**カナダ**||
|CanadaCentral|CanadaCentral|
|**アジア太平洋**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**ヨーロッパ**||
|UKSouth|UKSouth|
|西ヨーロッパ|西ヨーロッパ|
|**US Gov** ||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS の Log Analytics ワークスペースと Automation アカウントのマッピングは、正確なリージョン間のマッピングではありませんが、適切なマッピングです。

<sup>2</sup> 容量の制約により、新しいリソースを作成するときにリージョンを使用できません。 これには、Automation アカウントと Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="unlink-workspace"></a>ワークスペースのリンクの解除

ご使用の Automation アカウントを Log Analytics ワークスペースと統合する必要がなくなった場合は、Azure portal から直接、そのアカウントのリンクを解除できます。 Update Management、Change Tracking と Inventory、または Start/Stop VMs during off-hours を使用している場合、続行する前にこれらを削除する必要があります。 削除しないと、リンク解除操作を完了できません。 削除するために必要な手順を理解するために、有効なものそれぞれに関する記事を確認します。

これらを削除したら、以下の手順を行って Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部のソリューションでは、Automation アセットを作成している可能性があり、ワークスペースのリンクを解除する前にその削除が必要な場合があります。

1. Azure portal で、Automation アカウントを開きます。 [Automation アカウント] ページで、 **[関連リソース]** の下にある **[リンクされたワークスペース]** を選択します。

2. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックします。 続行するかどうかを確認するプロンプトが表示されます。

3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

4. Update Management を使用していた場合は、削除後に不要になる以下の項目を削除することもできます。

    * 更新スケジュール - それぞれに、作成した更新プログラムの展開と一致する名前が付いています。
    * ソリューション用に作成されたハイブリッド Worker グループ - それぞれには `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` のような名前が付いています。

5. Start/Stop VMs during off-hours を使用していた場合は、削除後に不要になる以下の項目を削除することもできます。

    * VM の開始/停止の Runbook スケジュール
    * VM の開始/停止の Runbook
    * 変数

または、ワークスペース内の Automation アカウントからワークスペースのリンクを解除することもできます。

1. ワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 
2. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次のステップ

* Update Management、Change Tracking、および Inventory のオンボードを行う方法について説明します。

    * [仮想マシン](../automation-onboard-solutions-from-vm.md)から
    * お使いの [Automation アカウント](../automation-onboard-solutions-from-automation-account.md)から
    * [複数のマシンを参照する](../automation-onboard-solutions-from-browse.md)とき
    * [Runbook](../automation-onboard-solutions.md) から

* Start/Stop VMs during off-hours をオンボードする方法について説明します。

    * [Start/Stop VMs during off-hours の概要](../automation-solution-vm-management.md)
