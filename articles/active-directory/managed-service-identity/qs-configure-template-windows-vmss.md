---
title: テンプレートを使用し、Azure 仮想マシン スケール セットのマネージド サービス ID を構成する
description: Azure Resource Manager テンプレートを使用し、Azure VMSS でマネージド サービス ID を構成する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: bee75bcefb370382825c6867ea504e14102aa107
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628285"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>テンプレートを使用し、仮想マシン スケールのマネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Resource Manager デプロイ テンプレートを使用し、Azure 仮想マシン スケール セットで次のマネージド サービス ID 操作を実行する方法を学びます。
- Azure 仮想マシン スケール セットでシステム割り当て ID を有効および無効にする
- Azure 仮想マシン スケール セットでユーザー割り当て ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - 仮想マシン スケール セットを作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、仮想マシン スケール セットから削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - 仮想マシン スケール セットからユーザー割り当て ID を割り当てたり削除するための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure portal とスクリプトを使う場合と同じように、[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイすることができます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)または[デプロイの現在の状態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/resource-manager-create-first-template.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の VM でのマネージド サービス ID の有効化も同じように行われます。 また、既定で Azure Resource Manager はデプロイに対して[増分更新](../../azure-resource-manager/deployment-modes.md)を行います。

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使って、システム割り当て ID を有効および無効にします。

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-or-an-existing-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当て ID を有効にする、または既存の Azure 仮想マシン スケール セットに対してシステム割り当て ID を有効にする

1. テンプレートをエディターに読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 使用するエディターや、編集しているテンプレートが新しいデプロイと既存のデプロイのどちらであるかによって、実際の表示は次のスクリーンショットと多少異なる場合があります。
   
   ![テンプレートのスクリーンショット - VM を見つける](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. システム割り当て ID を有効にするには、`"identity"` プロパティを `"type": "Microsoft.Compute/virtualMachineScaleSets"` プロパティと同じレベルに追加します。 次の構文を使用します。

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (省略可能) 仮想マシン スケール セットのマネージド サービス ID 拡張機能を `extensionsProfile` 要素として追加します。 Azure Instance Metadata Service (IMDS) の ID を使ってトークンを取得することもできるため、このステップは省略可能です。  次の構文を使用します。

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

   ![テンプレート更新後のスクリーンショット](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当て ID を無効にする

マネージド サービス ID が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 システム割り当て ID のみを持つ仮想マシン スケール セットがある場合は、ID の種類を `None` に変更して、無効にすることができます。  仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID の両方が与えられている場合は、ID の種類から `SystemAssigned` を削除し、ユーザー割り当て ID の `identityIds` 配列と共に `UserAssigned` を保持します。  次の例は、ユーザー割り当て ID がない仮想マシン スケール セットからシステム割り当て ID を削除する方法を示しています。
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure VMSS にユーザー割り当て ID を割り当てます。

> [!Note]
> Azure Resource Manager テンプレートを使用して、ユーザー割り当て ID を作成するには、「[ユーザー割り当て ID を作成する](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)」をご覧ください。

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>ユーザー割り当て ID を Azure VMSS に割り当てる

1. ユーザー割り当て ID を VMSS に割り当てるには、`resources` 要素に次のエントリを追加します。  `<USERASSIGNEDIDENTITY>` は、作成したユーザー割り当て ID の名前に置き換えてください。

   > [!Important]
   > 次の例の `<USERASSIGNEDIDENTITYNAME>` 値は、変数に格納される必要があります。  また、Resource Manager テンプレートで仮想マシンにユーザー割り当ての ID を割り当てる現在サポートされている実装では、API バージョンは次の例のバージョンと一致している必要があります。 

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
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

### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当て ID を削除する

マネージド サービス ID が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 ユーザー割り当て ID のみを持つ仮想マシン スケール セットがある場合は、ID の種類を `None` に変更し、無効にすることができます。  仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID の両方が与えられていて、システム割り当て ID を保持する場合は、ユーザー割り当て ID の `identityIds` 配列と共に ID の種類から `UserAssigned` を削除します。
    
   仮想マシン スケール セットからユーザー割り当て ID を 1 つ削除するには、`identityIds` 配列からそれを削除します。
   
   次の例は、システム割り当て ID がない仮想マシン スケール セットからユーザー割り当て ID をすべて削除する方法を示しています。
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="next-steps"></a>次の手順

- マネージド サービス ID の詳細については、[マネージド サービス ID](overview.md) に関するページを参照してください。

