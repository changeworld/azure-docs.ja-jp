---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c36bec44cf65d1269806f9d43950f0755c31132c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618361"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[\[プレビュー\]: 仮想マシンで Azure Defender for SQL エージェントを構成する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_AzureDefenderForSql.json) |Azure Monitor エージェントがインストールされる場所に Azure Defender for SQL エージェントが自動的にインストールされるよう仮想マシンを構成します。 Security Center では、エージェントからイベントを収集し、それらを使用してセキュリティ アラートと調整されたセキュリティ強化タスク (推奨事項) を提供します。 マシンと同じリージョンに、リソース グループと Log Analytics ワークスペースを作成します。 このポリシーは、少数のリージョンの VM にのみ適用されます。 |2 |1.0.0-preview |
|[Azure Monitor エージェントを Linux 仮想マシンに構成し、データ収集ルールに関連付ける](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_LinuxPlatform_EnableDCR.json) |仮想マシン イメージ (OS) と場所が定義済みの一覧にあり、エージェントがインストールされていない場合は、Linux 仮想マシン用 Azure Monitor エージェントをデプロイします。  次に、[関連付け] を展開して、仮想マシンを指定のデータ収集ルールにリンクします。 OS イメージの一覧は、サポートの向上に伴って更新されます。 |2 |1.0.0 |
|[Azure Monitor エージェントを Windows 仮想マシンに構成し、データ収集ルールに関連付ける](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_WindowsPlatform_EnableDCR.json) |仮想マシン イメージ (OS) と場所が定義済みの一覧にあり、エージェントがインストールされていない場合は、Windows 仮想マシン用 Azure Monitor エージェントをデプロイします。  次に、[関連付け] を展開して、仮想マシンを指定のデータ収集ルールにリンクします。 OS イメージの一覧は、サポートの向上に伴って更新されます。 |2 |1.0.0 |
|[\[プレビュー\]: 仮想マシンに Azure Monitor および Azure セキュリティ エージェントを自動的にインストールするようにマシンを構成します](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Azure Monitor および Azure セキュリティ エージェントを自動的にインストールするようにマシンを構成します。 Security Center では、エージェントからイベントを収集し、それらを使用してセキュリティ アラートと調整されたセキュリティ強化タスク (推奨事項) を提供します。 監査レコードを保存するマシンと同じリージョンに、リソース グループと Log Analytics ワークスペースを作成します。 このポリシーは、少数のリージョンの VM にのみ適用されます。 |6 |2.0.0-preview |
|[仮想マシン スケール セットに対して Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) 内の仮想マシン スケール セットに対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 注: お使いのスケール セット upgradePolicy が手動に設定されている場合、セット内のすべての VM でアップグレードを呼び出して拡張機能を適用することが必要になります。 CLI では、これは az vmss update-instances になります。 |6 |1.0.1 |
|[VM 用 Azure Monitor を有効にする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |指定されたスコープ (管理グループ、サブスクリプション、リソース グループ) 内の仮想マシン (VM) に対して Azure Monitor を有効にします。 Log Analytics ワークスペースをパラメーターとして取得します。 |10 |2.0.0 |
