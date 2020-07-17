---
title: Azure Security Center FAQ - 既存の Log Analytics エージェントに関する質問
description: この FAQ では、Log Analytics エージェントを既に使用しており、脅威の防御、検出、対応を可能にする製品である Azure Security Center を検討しているお客様からの質問に回答します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 2fe306cf7d17f0789c5e134c3fcad3f8f07a0b80
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612828"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Azure Monitor ログを既に使用しているお客様向けの FAQ<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center は VM とワークスペース間の既存の接続をオーバーライドしますか?

VM に Azure 拡張機能としてインストールされている Log Analytics エージェントが既にある場合、Security Center は既存のワークスペース接続をオーバーライドしません。 代わりに、Security Center は既存のワークスペースを使用します。 VM のレポート先のワークスペースに "Security" または "SecurityCenterFree" ソリューションがインストールされている場合、VM は保護されます。 

[データ収集] 画面で選択されているワークスペースに Security Center ソリューションがインストールされ (まだ存在しない場合)、ソリューションは関連する VM にのみ適用されます。 ソリューションを追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。 [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用することができます。

> [!TIP]
> Log Analytics エージェントが VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Log Analytics エージェントがインストールされず、セキュリティの監視は制限されます。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center は既存の Log Analytics ワークスペースにソリューションをインストールしますか? 課金にどのような影響がありますか?
その VM が、作成したワークスペースに既に接続されていることを Security Center が特定すると、Security Center はご利用の価格レベルに従ってそのワークスペースでソリューションを有効にします。 ソリューションは、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って関連する Azure VM にのみ適用されるため、課金額は同じままです。

- **Free レベル** - Security Center によってワークスペースに 'SecurityCenterFree' ソリューションがインストールされます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によってワークスペースに 'Security' ソリューションがインストールされます。

   ![既定のワークスペースのソリューション](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>環境内に既にワークスペースがありますが、それらを使用してセキュリティ データを収集できますか?
VM に Azure 拡張機能としてインストールされている Log Analytics エージェントが既にある場合、Security Center は既存の接続済みワークスペースを使用します。 Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って、ソリューションは関連する VM にのみ適用されます。

Security Center によって VM 上に Log Analytics エージェントがインストールされると、Security Center では既存のワープスペースを参照していない場合、Security Center によって作成された既定のワークスペースが使用されます。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>ワークスペースにはセキュリティ ソリューションが既にあります。 課金にどのような影響がありますか?
Security & Audit ソリューションは、Azure VM で Security Center Standard レベルの機能を有効にするために使用されます。 Security & Audit ソリューションがワークスペースに既にインストールされている場合、Security Center は既存のソリューションを使用します。 課金額の変更はありません。
