---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170100"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[許可されている仮想マシン SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |このポリシーを使用して、組織でデプロイできる仮想マシン SKU のセットを指定できます。 |拒否 |1.0.0 |
|[ディザスター リカバリーを構成されていない仮想マシンの監査](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |ディザスター リカバリーが構成されていない仮想マシンを監査します。 ディザスター リカバリーの詳細については、 https://aka.ms/asr-doc にアクセスしてください。 |auditIfNotExists |1.0.0 |
|[Managed Disks を使用していない VM の監査](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |このポリシーは、マネージド ディスクを使用していない VM を監査します |監査 |1.0.0 |
|[Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |このポリシーでは、VM にマルウェア対策の拡張機能が構成されていない場合に、既定の構成で Microsoft IaaSAntimalware 拡張機能をデプロイします。 |deployIfNotExists |1.0.0 |
|[仮想マシン スケール セットの診断ログを有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |1.0.0 |
|[Azure 向け Microsoft Antimalware は保護定義を自動的に更新するように構成する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |このポリシーは、Microsoft Antimalware 保護定義の自動更新が構成されていないすべての Windows 仮想マシンを監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[Microsoft IaaSAntimalware 拡張機能は Windows Server に展開する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |このポリシーは、Microsoft IaaSAntimalware 拡張機能がデプロイされていないすべての Windows Server VM を監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[インストールする必要があるのは、許可されている VM 拡張機能のみ](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |このポリシーは、承認されていない仮想マシン拡張機能を制御します。 |Audit、Deny、Disabled |1.0.0 |
|[Virtual Machine Scale Sets で自動 OS イメージ パッチ適用が必要](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |このポリシーは、Virtual Machine Scale Sets での自動 OS イメージ パッチ適用を有効にし、最新のセキュリティ修正プログラムを毎月安全に適用することによって、常に Virtual Machines を保護します。 |deny |1.0.0 |
|[アタッチされていないディスクを暗号化する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |このポリシーは、アタッチされていないディスクで、暗号化が有効でないものすべてを監査します。 |Audit、Disabled |1.0.0 |
|[仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |新しい Azure Resource Manager をお使いの仮想マシンに使用して、次のようなセキュリティの拡張機能を提供します: 強化されたアクセスの制御 (RBAC)、改善された監査、ARM ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレットのためのキー コンテナーへのアクセス、Azure AD に基づく認証、セキュリティ管理を容易にするタグとリソース グループのサポート |Audit、Deny、Disabled |1.0.0 |
