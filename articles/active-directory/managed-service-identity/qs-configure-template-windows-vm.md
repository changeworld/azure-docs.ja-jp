---
title: テンプレートを使用して Azure VM でマネージド サービス ID を構成する方法
description: Azure Resource Manager テンプレートを使用して、Azure VM でマネージド サービス ID を構成する方法をステップ バイ ステップで説明します。
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 79b499f8063e5c15f76d89182955cbd90fb1039f
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629312"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>テンプレートを使用して VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure VM で、Azure Resource Manager デプロイ テンプレートを使用して、次の管理対象サービス ID (MSI) 操作を実行する方法を学びます。

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - VM を作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、Azure VM から削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - ユーザー割り当て ID を VM に割り当てたり、VM から削除したりするための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure portal とスクリプトを使う場合と同じように、[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイすることができます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)または[デプロイの現在の状態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/resource-manager-create-first-template.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の VM での、システムまたはユーザー割り当て ID の有効化も同じように行われます。 また、既定では Azure Resource Manager は、デプロイに対して[増分更新](../../azure-resource-manager/deployment-modes.md)を実行します。

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使用して、システム割り当て ID の有効化や無効化を行います。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Azure VM の作成中に、または既存の VM でシステム割り当て ID を有効にする

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートをエディターに読み込んだら、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachines` リソースを探します。 使用するエディターや、編集しているテンプレートが新しいデプロイと既存のデプロイのどちらであるかによって、実際の表示は次のスクリーンショットと多少異なる場合があります。

   >[!NOTE] 
   > この例は、`vmName`、`storageAccountName`、`nicName` などの変数がテンプレートで定義されていることを前提としています。
   >

   ![テンプレートのスクリーンショット - VM を見つける](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. システム割り当て ID を有効にするには、`"identity"` プロパティを `"type": "Microsoft.Compute/virtualMachines"` プロパティと同じレベルで追加します。 次の構文を使用します。

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (省略可能) VM マネージド サービス ID 拡張機能を `resources` 要素として追加します。 Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。  次の構文を使用します。

   >[!NOTE] 
   > 次の例では、Windows VM の拡張機能 (`ManagedIdentityExtensionForWindows`) がデプロイ済みであることを前提としています。 `"name"` 要素と `"type"` 要素については、代わりに `ManagedIdentityExtensionForLinux` を使用して Linux 用に構成することもできます。
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

   ![テンプレート更新後のスクリーンショット](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>ロールに VM のシステム割り当て ID を割り当てる

ご利用の VM でシステム割り当て ID を有効にしたら、作成先となったリソース グループへの**閲覧者**アクセス許可などのロールを付与することができます。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。
 
2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、以下の情報を追加して、それが作成されたリソース グループへの**閲覧者**アクセス許可をご利用の VM に付与します。  テンプレートの構造は、選択したデプロイ モデルとエディターに応じて異なる場合があります。
   
   `parameters` セクションの下に、以下を追加します。

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    `variables` セクションの下に、以下を追加します。

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    `resources` セクションの下に、以下を追加します。

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Azure VM でシステム割り当て ID を無効にする

MSI が不要になった VM がある場合は、次のようにします。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachines` リソースを探します。 システム割り当て ID のみを持つ VM がある場合は、ID の種類を `None` に変更して、無効にすることができます。  VM がシステム割り当て ID とユーザー割り当て ID の両方を持っている場合は、ID の種類から `SystemAssigned` を削除し、ユーザー割り当て ID の `identityIds` 配列と共に `UserAssigned` を保持します。  次の例は、ユーザー割り当て ID がない VM からシステム割り当て ID を削除する方法を示しています。
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure VM にユーザー割り当て ID を割り当てます。

> [!Note]
> Azure Resource Manager テンプレートを使用して、ユーザー割り当て ID を作成するには、[ユーザー割り当て ID を作成する](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)をご覧ください。

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>ユーザー割り当て ID を Azure VM に割り当てる

1. ユーザー割り当て ID を VM に割り当てるには、`resources` 要素で、次のエントリを追加します。  `<USERASSIGNEDIDENTITY>` は、作成したユーザー割り当て ID の名前に置き換えてください。
   
   > [!Important]
   > 次の例の `<USERASSIGNEDIDENTITYNAME>` 値は、変数に格納される必要があります。  また、Resource Manager テンプレートで仮想マシンにユーザー割り当ての ID を割り当てる現在サポートされている実装では、API バージョンは次の例のバージョンと一致している必要があります。
    
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (オプション) 次に、管理対象 ID 拡張機能を VM に割り当てるため、`resources` 要素で、以下のエントリを追加します。 Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。 次の構文を使用します。
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3.  完了すると、テンプレートは以下の例のようになります。

      ![ユーザー割り当て ID のスクリーンショット](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当て ID を削除する

MSI が不要になった VM がある場合は、次のようにします。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachines` リソースを探します。 ユーザー割り当て ID のみを持つ VM がある場合は、ID の種類を `None` に変更して、無効にすることができます。  VM にシステム割り当て ID とユーザー割り当て ID の両方が与えられている場合は、ユーザー割り当て ID の `identityIds` 配列と共に ID の種類から `UserAssigned` を削除します。
    
   VM からユーザー割り当て ID を 1 つ削除するには、`identityIds` 配列からそれを削除します。
   
   次の例は、システム割り当て ID がない VM からユーザー割り当て ID をすべて削除する方法を示しています。
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="related-content"></a>関連コンテンツ

- マネージド サービス ID の詳細については、[マネージド サービス ID](overview.md) に関するページを参照してください。

