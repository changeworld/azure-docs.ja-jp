---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5c1857372eb4aad2bf87e4078bae5e1fc41412a8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091852"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[デプロイ - 仮想マシンで Azure Monitor および Azure セキュリティ エージェントを有効にするための前提条件を構成する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Azure Monitor および Azure セキュリティ エージェントを自動的にインストールするようにマシンを構成します。 Security Center では、エージェントからイベントを収集し、それらを使用してセキュリティ アラートと調整されたセキュリティ強化タスク (推奨事項) を提供します。 監査レコードを保存するマシンと同じリージョンに、リソース グループと Log Analytics ワークスペースを作成します。 このポリシーは、少数のリージョンの VM にのみ適用されます。 |5 |1.0.0-preview |
|[仮想マシン スケール セットに対して Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) 内の仮想マシン スケール セットに対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 注: お使いのスケール セット upgradePolicy が手動に設定されている場合、セット内のすべての VM でアップグレードを呼び出して拡張機能を適用することが必要になります。 CLI では、これは az vmss update-instances になります。 |6 |1.0.1 |
|[VM 用 Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |指定されたスコープ (管理グループ、サブスクリプション、リソース グループ) 内の仮想マシン (VM) に対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 |10 |2.0.0 |
