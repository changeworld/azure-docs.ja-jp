---
title: Azure Security Center FAQ - 既存の MMA に関する質問
description: この FAQ では、Microsoft Monitoring Agent を既に使用しており、脅威の防御、検出、対応を可能にする製品である Azure Security Center を検討しているお客様からの質問に回答します。
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661891"
---
# Azure Monitor ログを既に使用しているお客様向けの FAQ<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center は VM とワークスペース間の既存の接続をオーバーライドしますか?

VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存のワークスペース接続をオーバーライドしません。 代わりに、Security Center は既存のワークスペースを使用します。 VM のレポート先のワークスペースに "Security" または "SecurityCenterFree" ソリューションがインストールされている場合、VM は保護されます。 

[データ収集] 画面で選択されているワークスペースに Security Center ソリューションがインストールされ (まだ存在しない場合)、ソリューションは関連する VM にのみ適用されます。 ソリューションを追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。 [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用することができます。

> [!TIP]
> Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent がインストールされず、セキュリティの監視は制限されます。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center は既存の Log Analytics ワークスペースにソリューションをインストールしますか? 課金にどのような影響がありますか?
その VM が、作成したワークスペースに既に接続されていることを Security Center が特定すると、Security Center はご利用の価格レベルに従ってそのワークスペースでソリューションを有効にします。 ソリューションは、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って関連する Azure VM にのみ適用されるため、課金額は同じままです。

- **Free レベル** - Security Center によってワークスペースに 'SecurityCenterFree' ソリューションがインストールされます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によってワークスペースに 'Security' ソリューションがインストールされます。

   ![既定のワークスペースのソリューション](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>環境内に既にワークスペースがありますが、それらを使用してセキュリティ データを収集できますか?
VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存の接続済みワークスペースを使用します。 Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って、ソリューションは関連する VM にのみ適用されます。

Security Center で VM に Microsoft Monitoring Agent がインストールされると、Security Center で作成された既定のワークスペースが使用されます。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>ワークスペースにはセキュリティ ソリューションが既にあります。 課金にどのような影響がありますか?
Security & Audit ソリューションは、Azure VM で Security Center Standard レベルの機能を有効にするために使用されます。 Security & Audit ソリューションがワークスペースに既にインストールされている場合、Security Center は既存のソリューションを使用します。 課金額の変更はありません。