---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 887ec9b2177e6d4071be99b9e6aec97b166c586d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108761439"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[\[プレビュー\]: デプロイ - 仮想マシン上の Azure Monitor と Azure Security エージェントを有効にするために前提条件を構成する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Azure Monitor および Azure セキュリティ エージェントを自動的にインストールするようにマシンを構成します。 Security Center では、エージェントからイベントを収集し、それらを使用してセキュリティ アラートと調整されたセキュリティ強化タスク (推奨事項) を提供します。 監査レコードを保存するマシンと同じリージョンに、リソース グループと Log Analytics ワークスペースを作成します。 このポリシーは、少数のリージョンの VM にのみ適用されます。 |5 |1.0.0-preview |
|[仮想マシン スケール セットに対して Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) 内の仮想マシン スケール セットに対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 注: お使いのスケール セット upgradePolicy が手動に設定されている場合、セット内のすべての VM でアップグレードを呼び出して拡張機能を適用することが必要になります。 CLI では、これは az vmss update-instances になります。 |6 |1.0.1 |
|[VM 用 Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |指定されたスコープ (管理グループ、サブスクリプション、リソース グループ) 内の仮想マシン (VM) に対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 |10 |2.0.0 |
