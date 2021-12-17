---
title: Azure CLI を使用してマネージド ID をアプリケーション ロールに割り当てる - Azure AD
description: Azure CLI を使用して、マネージド ID アクセスを別のアプリケーションのロールに割り当てる手順について説明します。
services: active-directory
documentationcenter: ''
author: christoc
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2021
ms.author: christoc
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 846085c773c1923f581968c06c02c5c4f8372e65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180201"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-azure-cli"></a>Azure CLI を使用してマネージド ID アクセスをアプリケーション ロールに割り当てる

Azure リソースのマネージド ID により、Azure Active Directory の ID が Azure サービスに提供されます。 それらが動作するためにコード内の資格情報は必要ありません。 この ID は、Azure AD 認証をサポートするサービスへの認証を行うために、Azure サービスによって使用されます。 アプリケーション ロールによってロールベースのアクセス制御の形式が提供され、サービスで承認規則を実装できます。

この記事では、Azure CLI を使用して、別のアプリケーションによって公開されているアプリケーション ロールにマネージド ID を割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>マネージド ID アクセスを別のアプリケーションのアプリ ロールに割り当てる

1. [Azure VM などの](qs-configure-cli-windows-vm.md) Azure リソースでマネージド ID を有効にします。

1. マネージド ID のサービス プリンシパルのオブジェクト ID を調べます。

   **システム割り当てのマネージド ID の場合** は、Azure portal のリソースの **[ID]** ページでオブジェクト ID を確認できます。 次のスクリプトを使用してオブジェクト ID を調べることもできます。 ステップ 1 で作成したリソースのリソース ID が必要になります。これは、Azure portal のリソースの **[プロパティ]** ページでわかります。

    ```azurecli
    resourceIdWithManagedIdentity="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}"
    
    oidForMI=$(az resource show --ids $resourceIdWithManagedIdentity --query "identity.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

    **ユーザー割り当てのマネージド ID の場合** は、Azure portal のリソースの **[概要]** ページで、マネージド ID のオブジェクト ID を確認できます。 次のスクリプトを使用してオブジェクト ID を調べることもできます。 ユーザー割り当てのマネージド ID のリソース ID が必要になります。

    ```azurecli
    userManagedIdentityResourceId="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}"
    
    oidForMI=$(az resource show --id $userManagedIdentityResourceId --query "properties.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

1. マネージド ID による要求の送信先であるサービスを表す新しいアプリケーション登録を作成します。 マネージド ID に対してアプリ ロールの付与を公開している API またはサービスのサービス プリンシパルが Azure AD テナントに既にある場合は、このステップをスキップします。

1. サービス アプリケーションのサービス プリンシパルのオブジェクト ID を調べます。 これは、Azure portal を使用して確認できます。 Azure Active Directory に移動して **[エンタープライズ アプリケーション]** ページを開き、アプリケーションを見つけて、 **[オブジェクト ID]** を調べます。 次のスクリプトを使用して、サービス プリンシパルのオブジェクト ID を表示名で検索することもできます。

    ```azurecli
    appName="{name for your application}"
    serverSPOID=$(az ad sp list --filter "displayName eq 'My App'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

    > [!NOTE]
    > アプリケーションの表示名は一意ではないため、取得したサービス プリンシパルが適切なアプリケーションのものであることを確認する必要があります。

    または、アプリケーション登録用の一意のアプリケーション ID でオブジェクト ID を見つけることができます。

    ```azurecli
    appID="{application id for your application}"
    serverSPOID=$(az ad sp list --filter "appId eq '$appID'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

1. ステップ 3 で作成したアプリケーションに[アプリ ロール](../develop/howto-add-app-roles-in-azure-ad-apps.md)を追加します。 ロールは、Azure portal または Microsoft Graph を使用して作成できます。 たとえば、次のようなアプリ ロールを追加できます。

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. アプリ ロールをマネージド ID に割り当てます。 アプリ ロールを割り当てるには、次の情報が必要です。
    * `managedIdentityObjectId`: マネージド ID のサービス プリンシパルのオブジェクト ID。ステップ 2 で確認しました。
    * `serverServicePrincipalObjectId`: サーバー アプリケーションのサービス プリンシパルのオブジェクト ID。ステップ 4 で確認しました。
    * `appRoleId`: サーバー アプリによって公開されるアプリ ロールの ID。ステップ 5 で生成しました。この例では、アプリ ロール ID は `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` です。
   
   次のスクリプトを実行してロールの割り当てを追加します。  この機能は Azure CLI では直接公開されておらず、ここでは代わりに、REST コマンドが使用されていることに注意してください。

    ```azurecli
    roleguid="0566419e-bb95-4d9d-a4f8-ed9a0f147fa6"
    az rest -m POST -u https://graph.microsoft.com/beta/servicePrincipals/$oidForMI/appRoleAssignments -b "{\"principalId\": \"$oidForMI\", \"resourceId\": \"$serverSPOID\",\"appRoleId\": \"$roleguid\"}"
    ```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure VM 上でマネージド ID を有効にするには、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md)」を参照してください。
