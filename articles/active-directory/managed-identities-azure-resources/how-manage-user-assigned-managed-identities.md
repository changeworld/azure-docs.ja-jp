---
title: ユーザー割り当てマネージド ID の管理 - Azure AD
description: ユーザー割り当てマネージド ID を作成します。
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: barclayn
zone_pivot_groups: identity-mi-methods
ms.openlocfilehash: 83f47dbbdd0968a845871b106be9142fd67f9967
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "122651972"
---
# <a name="manage-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID の管理



Azure リソースのマネージド ID を使用すると、コードで資格情報を管理する必要がなくなります。 これらを使用すると、Azure AD 認証をサポートするリソースにアクセスする際にアプリケーションで使用できる Azure Active Directory (Azure AD) トークンを取得できます。 ID は Azure で管理されるためユーザーが行う必要はありません。

マネージド ID には、システム割り当てとユーザー割り当ての 2 種類があります。 2 つの主な違いは、システムによって割り当てられたマネージド ID のライフサイクルが、使用されるリソースにリンクされていることです。 ユーザー割り当てマネージド ID は、複数のリソースで使用できます。 マネージド ID の詳細については、[Azure リソースのマネージド ID とは](overview.md)を参照してください。

::: zone pivot="identity-mi-methods-azp"
このアーティクルでは、Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 [システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。


## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を作成します。
1. 検索ボックスに、「**マネージド ID**」と入力します。 **[サービス]** の下で、 **[マネージド ID]** を選択します。
1. **[追加]** をクリックして、 **[ユーザー割り当てマネージド ID を作成する]** ウィンドウの次のボックスに値を入力します。
    - **サブスクリプション**:ユーザー割り当てマネージド ID を作成するサブスクリプションを選択します。
    - **リソース グループ**: ユーザー割り当てマネージド ID を作成するリソース グループを選択するか、 **[新規作成]** をクリックして新しいリソース グループを作成します。
    - **リージョン**: ユーザー割り当てマネージド ID をデプロイするリージョンを選択します。たとえば、**米国西部** などです。
    - **名前**: ユーザー割り当てマネージド ID の名前です。たとえば、UAI1 とします。
  
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]
  
   ![ユーザー割り当て済みマネージド ID の作成ウィンドウを示すスクリーンショット。](media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
1. **[確認と作成]** を選択して変更を確認します。
1. **[作成]** を選択します。

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID を一覧表示または読み取るには、アカウントへの [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) または [Managed Identity Contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) ロールの割り当てが必要です。

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を一覧表示します。
1. 検索ボックスに、「**マネージド ID**」と入力します。 **[サービス]** の下で、 **[マネージド ID]** を選択します。
1. ご使用のサブスクリプションのユーザー割り当てマネージド ID の一覧が表示されます。 ユーザー割り当てマネージド ID の詳細を確認するには、名前をクリックします。

   ![ユーザー割り当てマネージド ID の一覧を示すスクリーンショット。](media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当て ID を削除しても、それが割り当てられていた VM やリソースから削除されることはありません。 VM からユーザー割り当てマネージド ID を削除する場合は、[VM からユーザー割り当て ID を削除する](qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)を参照してください。

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を削除します。
1. ユーザー割り当てマネージド ID を選択して、 **[削除する]** をクリックします。
1. 確認ボックスで **[はい]** を選択します。

   ![ユーザー割り当てマネージド ID の削除を示すスクリーンショット。](media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID にロールを割り当てる 

ユーザー割り当てマネージド ID にロールを割り当てるには、お使いのアカウントに[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールの割り当てが必要です。

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を一覧表示します。
1. 検索ボックスに、「**マネージド ID**」と入力します。 **[サービス]** の下で、 **[マネージド ID]** を選択します。
1. ご使用のサブスクリプションのユーザー割り当てマネージド ID の一覧が表示されます。 ロールを割り当てるユーザー割り当てマネージド ID を選択します。
1. **[アクセス制御 (IAM)]** を選択したら、 **[ロール割り当ての追加]** を選択します。

   ![ユーザー割り当てマネージド ID のスタートを示すスクリーンショット。](media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

1. **[ロールの割り当ての追加]** ウィンドウで、次の値を構成してから **[保存]** を選択します。
   - **[ロール]** - 割り当てるロール。
   - **[アクセスの割り当て先]** - ユーザー割り当てマネージド ID を割り当てるリソース。
   - **[選択]** - アクセスを割り当てるメンバー。
   
   ![ユーザー割り当てマネージド ID の IAM を示すスクリーンショット。](media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)



::: zone-end



::: zone pivot="identity-mi-methods-azcli"

このアーティクルでは、Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 *[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください*。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。


[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]  
> CLI を使用してアプリのサービス プリンシパルを使用しているときにユーザーのアクセス許可を変更するには、CLI の一部がグラフ API に対して GET 要求を実行するため、サービス プリンシパルに Azure Active Directory Graph API でより多くの権限を与える必要があります。 そうしないと、「この操作を完了するのに十分な権限がありません」というメッセージが表示される可能性があります。 これを行うには、Azure AD の **[アプリの登録]** に移動し、アプリを選択し、 **[API のアクセス許可]** を選択し、スクロールダウンして **[Azure Active Directory Graph]** を選択します。 そこから、 **[アプリケーションのアクセス許可]** を選択し、適切なアクセス許可を追加します。 

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、[az identity create](/cli/azure/identity#az_identity_create) コマンドを使用します。 `-g` パラメーターには、ユーザー割り当てマネージド ID を作成するリソース グループを指定します。 `-n` パラメーターでは、名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID を一覧表示または読み取るには、アカウントへの [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) または [Managed Identity Contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、[az identity list](/cli/azure/identity#az_identity_list) コマンドを使用します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

JSON 応答内のユーザー割り当てマネージド ID には、キー `type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当て ID を削除するには、[az identity delete](/cli/azure/identity#az_identity_delete) コマンドを使用します。 -n パラメーターには、その名前を指定します。 -g パラメーターには、ユーザー割り当てマネージド ID が作成されたリソース グループを指定します。 `<USER ASSIGNED IDENTITY NAME>` および `<RESOURCE GROUP>` パラメーターの値は、実際の値に置き換えます。

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 `az vm/vmss identity remove` コマンドを使用して VM または仮想マシン スケール セットからそれらを削除します。

## <a name="next-steps"></a>次のステップ

Azure CLI の ID コマンドの一覧は、[az identity](/cli/azure/identity) をご覧ください。

Azure VM にユーザー割り当てマネージド ID を割り当てる方法については、[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)を参照してください。


::: zone-end

::: zone pivot="identity-mi-methods-powershell"

このアーティクルでは、PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 *[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください*。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[試してみる]** ボタンから開くことができます。
    - Azure PowerShell を使用して、スクリプトをローカルで実行します。次のセクションの説明を参照してください。

このアーティクルでは、PowerShell を使って、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

### <a name="configure-azure-powershell-locally"></a>ローカルで Azure PowerShell を構成する

このアーティクルのために、Cloud Shell を使わずにMicrosoft Azure PowerShell をローカルで使用するには、次の手順に従います。

1. [最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps) をインストールします (まだインストールしていない場合)。

1. Azure にサインインします。

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet の最新バージョン](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    次の手順のために、このコマンドを実行した後、現在の PowerShell セッションを `Exit` 終了する必要があるかもしれません。

1. このアーティクルのユーザー割り当てマネージド ID 操作を実行するために、`Az.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールします。

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、`New-AzUserAssignedIdentity` コマンドを使用します。 `ResourceGroupName` パラメーターには、ユーザー割り当てマネージド ID を作成するリソース グループを指定します。 `-Name` パラメーターでは、名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID を一覧表示または読み取るには、アカウントへの [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) または [Managed Identity Contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、[Get-AzUserAssigned] コマンドを使います。 `-ResourceGroupName` パラメーターでは、ユーザー割り当てマネージド ID を作成したリソース グループを指定します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```

応答内のユーザー割り当てマネージド ID には、キー `Type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を削除するには、`Remove-AzUserAssignedIdentity` コマンドを使用します。 `-ResourceGroupName` パラメーターには、ユーザー割り当て ID を作成したリソース グループを指定します。 `-Name` パラメーターでは、名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 ID の割り当ては、別に削除する必要があります。

## <a name="next-steps"></a>次のステップ

Azure リソースの Azure PowerShell マネージド ID コマンドの詳細については、「[Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)」を参照してください。


::: zone-end


::: zone pivot="identity-mi-methods-arm"

このアーティクルでは、Azure Resource Manager を使用してユーザー割り当てマネージド ID を作成します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 *[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください*。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

Resource Manager テンプレートを使用して、ユーザー割り当てマネージド ID を一覧表示したり削除したりすることはできません。 ユーザー割り当てマネージド ID の作成と一覧表示については、次の記事を参照してください。

- [ユーザー割り当てマネージド ID を一覧表示する](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [ユーザー割り当てマネージド ID を削除する](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)

## <a name="template-creation-and-editing"></a>テンプレートの作成と編集

Azure portal とスクリプトを使う場合と同じように、Azure Resource Manager テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイできます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。 次の操作を行います。

- [Microsoft Azure Marketplace からのカスタム テンプレート](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)を使用し、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)に基づいてテンプレートを作成したりします。
- [元のデプロイ](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)または[デプロイの現在の状態](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
- ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) を使用してから、PowerShell または Azure CLI を使用してアップロードおよびデプロイします。
- Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を使用して、テンプレートを作成およびデプロイします。 

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、次のテンプレートを使用します。 `<USER ASSIGNED IDENTITY NAME>` は実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>次のステップ

Azure Resource Manager テンプレートを使用して Azure VM にユーザー割り当てマネージド ID を割り当てる方法については、[テンプレートを使用したAzure VM 上のリソースのマネージド ID の構成](qs-configure-template-windows-vm.md)に関するページを参照してください。




::: zone-end


::: zone pivot="identity-mi-methods-rest"

この記事では、REST を使用して、ユーザー割り当てマネージド ID を作成、一覧表示、および削除する方法について説明します。


## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 *[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください*。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事で取り上げるすべてのコマンドは、クラウドでもローカルでも実行できます。
    - クラウドで実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。
    - ローカルで実行するには、[curl](https://curl.haxx.se/download.html) と [Azure CLI](/cli/azure/install-azure-cli) をインストールします。


この記事では、CURL を使用して REST API を呼び出し、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

## <a name="obtain-a-bearer-access-token"></a>ベアラー アクセス トークンを取得する

1. ローカルで実行している場合は、Azure CLI を使用して Azure にサインインします。

    ```
    az login
    ```

1. [az account get-access-token](/cli/azure/account#az_account_get_access_token) を使用してアクセス トークンを取得します。

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**要求ヘッダー**

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

**要求本文**

|名前  |説明  |
|---------|---------|
|場所     | 必須。 リソースの場所。        |

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID を一覧表示または読み取るには、アカウントへの [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) または [Managed Identity Contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) ロールの割り当てが必要です。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 CURL を使用して VM からユーザー割り当てマネージド ID を削除する場合は、「[Azure VM からユーザー割り当て ID を削除する](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)」を参照してください。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

## <a name="next-steps"></a>次のステップ

CURL を使用してユーザー割り当てマネージド ID を Azure VM または仮想マシン スケール セットに割り当てる方法については、次を参照してください。
- [REST API 呼び出しを使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-rest-vm.md#user-assigned-managed-identity) 
- [REST API 呼び出しを使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](qs-configure-rest-vmss.md#user-assigned-managed-identity)

::: zone-end


