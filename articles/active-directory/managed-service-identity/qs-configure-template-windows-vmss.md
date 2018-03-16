---
title: "テンプレートを使用して Azure 仮想マシン スケール セットの MSI を構成する"
description: "Azure Resource Manager テンプレートを使用して、Azure VMSS で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>テンプレートを使用して VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID (MSI) は、Azure Active Directory (Azure AD) で、自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Resource Manager デプロイ テンプレートを使用して Azure 仮想マシン スケール セットの MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中に、または既存の Azure 仮想マシン スケール セットで MSI を有効にする

Azure Portal とスクリプトの場合と同じように、Azure Resource Manager テンプレートは、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイする機能を提供します。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)または[デプロイの現在の状態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/resource-manager-create-first-template.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の Azure 仮想マシン スケール セットの MSI は同じ方法で有効化されます。 また、既定では Azure Resource Manager は、デプロイに対して[増分更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)を実行します。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートをエディターに読み込んだら、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 使用するエディターや、編集しているテンプレートが新しいデプロイと既存のデプロイのどちらであるかによって、実際の表示は次のスクリーンショットと多少異なる場合があります。
   
   ![テンプレートのスクリーンショット - VM を見つける](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. `"type": "Microsoft.Compute/virtualMachineScaleSets"` プロパティと同じレベルに `"identity"` プロパティを追加します。 次の構文を使用します。

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. その後、仮想マシン スケール セットの MSI 拡張機能を `extensionsProfile` 要素として追加します。 次の構文を使用します。

   >[!NOTE] 
   > 次の例では、Windows 仮想マシン スケール セットの拡張機能 (`ManagedIdentityExtensionForWindows`) がデプロイ済みであることを前提としています。 `"name"` 要素と `"type"` 要素については、代わりに `ManagedIdentityExtensionForLinux` を使用して Linux 用に構成することもできます。
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

5. 完了すると、テンプレートは以下の例のようになります。

   ![テンプレート更新後のスクリーンショット](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットから MSI を削除する

MSI が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. 前のセクションで追加した 2 つの要素、仮想マシン スケール セットの `"identity"` プロパティと `"extensionsProfile"` プロパティを削除します。

## <a name="next-steps"></a>次の手順

- MSI について詳しくは、[管理対象サービスの概要](overview.md)に関するページをご覧ください。

