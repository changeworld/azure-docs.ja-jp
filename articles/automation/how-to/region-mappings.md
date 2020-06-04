---
title: リンクされた Log Analytics ワークスペースでサポートされるリージョン
description: この記事では、Automation アカウントと Log Analytics ワークスペースとの間でサポートされているリージョン マッピングについて説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: d594d5a9dd263817023d2b5987c03aa50828191c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191854"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>リンクされた Log Analytics ワークスペースでサポートされるリージョン

Azure Automation では、お使いの VM で、Update Management、Change Tracking とインベントリ、Start/Stop VMs during off-hours の各機能を有効にすることができます。 ただし、サブスクリプション内で Log Analytics ワークスペースと Automation アカウントをリンクすることは、特定のリージョンでのみサポートされています。 リージョン マッピングは Automation アカウントと Log Analytics ワークスペースのみに適用されます。 Log Analytics ワークスペースと Automation アカウントは同じサブスクリプションに含まれている必要がありますが、同じリージョンにデプロイされている別のリソース グループに存在することができます。 詳細については、[Log Analytics ワークスペースと Automation アカウント](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)に関する記事を参照してください。

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
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS の Log Analytics ワークスペースと Automation アカウントのマッピングは、正確なリージョン間のマッピングではありませんが、適切なマッピングです。

<sup>2</sup> 容量の制約により、新しいリソースを作成する場合はこのリージョンを使用できません。 これには、Automation アカウントと Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="unlink-a-workspace"></a>ワークスペースのリンク解除

ご使用の Automation アカウントを Log Analytics ワークスペースと統合する必要がなくなった場合は、Azure portal から直接、そのアカウントのリンクを解除できます。 Update Management、Change Tracking とインベントリ、または Start/Stop VMs during off-hours を使用している場合、続行する前にまずこれらを[削除する](move-account.md#remove-features)必要があります。 削除しないと、リンク解除操作を完了できません。 

これらの機能を削除した後、以下の手順に従って Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部の機能では、ワークスペースのリンクを解除する前に削除する必要のある Automation アセットが作成されている可能性があります。

1. Azure portal で、Automation アカウントを開きます。 [Automation アカウント] ページで、 **[関連リソース]** の下にある **[リンクされたワークスペース]** を選択します。

2. [ワークスペースのリンクを解除] ページで、 **[ワークスペースのリンクを解除]** を選択します。 続行するかどうかを確認するプロンプトが表示されます。

3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクの解除が処理されている間、メニューの **[通知]** で進行状況を追跡できます。

4. Update Management を使用していた場合は、不要になる以下の項目を削除することもできます。

    * 更新スケジュール:それぞれに、作成した更新プログラムのデプロイと一致する名前が付いています。
    * この機能のために作成したハイブリッド worker グループ:それぞれに `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` のような名前が付いています。

5. Start/Stop VMs during off-hours を使用していた場合は、不要になる以下の項目を削除することもできます。

    * VM の開始/停止の Runbook スケジュール
    * VM の開始/停止の Runbook
    * 変数

または、ワークスペース内の Automation アカウントからワークスペースのリンクを解除することもできます。

1. ワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 
2. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次のステップ

* Update Management について、[Update Management の概要](../automation-update-management.md)に関する記事で学習する。
* Change Tracking とインベントリについて、「[変更履歴とインベントリの概要](../change-tracking.md)」で学習する。
* Start/Stop VMs during off-hours について、[Start/Stop VMs during off-hours の概要](../automation-solution-vm-management.md)に関する記事で学習する。
