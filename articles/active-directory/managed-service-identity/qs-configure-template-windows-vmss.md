---
title: テンプレートを使用して Azure 仮想マシン スケール セットの MSI を構成する
description: Azure Resource Manager テンプレートを使用して、Azure VMSS で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930614"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>テンプレートを使用して VMSS のマネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Resource Manager デプロイ テンプレートを使用して、Azure VMSS においてマネージド サービス ID に対する次の操作を実行する方法を説明します。
- Azure VMSS においてシステム割り当ての ID を有効および無効にする
- Azure VMSS においてユーザー割り当ての ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- マネージド サービス ID の基本についてご不明な点がある場合は、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure portal とスクリプトを使う場合と同じように、[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイすることができます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)または[デプロイの現在の状態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/resource-manager-create-first-template.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の VM の MSI は同じ方法で有効化されます。 また、既定で Azure Resource Manager はデプロイに対して[増分更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)を行います。

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使って、システム割り当て ID を有効および無効にします。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Azure VMSS の作成中に、または既存の Azure VMSS で、システム割り当て ID を有効にする

1. テンプレートをエディターに読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 使用するエディターや、編集しているテンプレートが新しいデプロイと既存のデプロイのどちらであるかによって、実際の表示は次のスクリーンショットと多少異なる場合があります。
   
   ![テンプレートのスクリーンショット - VM を見つける](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. システム割り当て ID を有効にするには、`"identity"` プロパティを `"type": "Microsoft.Compute/virtualMachineScaleSets"` プロパティと同じレベルに追加します。 次の構文を使用します。

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (省略可能) 仮想マシン スケール セットの MSI 拡張機能を `extensionsProfile` 要素として追加します。 Azure Instance Metadata Service (IMDS) の ID を使ってトークンを取得することもできるため、このステップは省略可能です。  次の構文を使用します。

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

4. 完了すると、テンプレートは以下の例のようになります。

   ![テンプレート更新後のスクリーンショット](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当て ID を無効にする

> [!NOTE]
> 仮想マシンからのマネージド サービス ID の無効化は現在サポートされていません。 現時点では、システム割り当て ID とユーザー割り当て ID の使用を切り替えることができます。

システム割り当て ID は不要になったが、ユーザー割り当て ID はまだ必要な仮想マシン スケール セットがある場合:

- エディターにテンプレートを読み込み、ID の種類を `'UserAssigned'` に変更します

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure VMSS にユーザー割り当て ID を割り当てます。

> [!Note]
> Azure Resource Manager テンプレートを使用して、ユーザー割り当て ID を作成するには、「[ユーザー割り当て ID を作成する](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)」をご覧ください。

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>ユーザー割り当て ID を Azure VMSS に割り当てる

1. ユーザー割り当て ID を VMSS に割り当てるには、`resources` 要素に次のエントリを追加します。  `<USERASSIGNEDIDENTITY>` は、作成したユーザー割り当て ID の名前に置き換えてください。

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (省略可能) `extensionProfile` 要素に次のエントリを追加して、マネージド ID 拡張機能を VMSS に割り当てます。 Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。 次の構文を使用します。
   
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
3.  完了すると、テンプレートは以下の例のようになります。
   
      ![ユーザー割り当て ID のスクリーンショット](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>次の手順

- MSI について詳しくは、[管理対象サービスの概要](overview.md)に関するページをご覧ください。

