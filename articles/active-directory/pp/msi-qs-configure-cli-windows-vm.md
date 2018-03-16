---
title: "Azure CLI を使用して Azure VM のユーザーによって割り当てられた MSI を構成する方法"
description: "Azure CLI を使用して、Azure VM のユーザーによって割り当てられた管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e8d21aad23782f22b93baf12ce58d1aed4dd5dee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Azure CLI を使用して、VM のユーザーによって割り当てられた管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID は、Azure Active Directory の管理対象 ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure CLI を使用して、Azure VM のユーザー割り当て MSI を有効化する方法と削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある [試してみる] ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 次に、[az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。 ユーザー割り当て MSI と VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

このセクションでは、VM を作成する方法と、ユーザー割り当て MSI を VM に割り当てる方法について説明します。 使用する VM が既にある場合は、このセクションをスキップして次のセクションに進んでください。

1. 使用するリソース グループが既にある場合は、この手順をスキップできます。 [az group create](/cli/azure/group/#az_group_create) を使用して、MSI の包含およびデプロイ用の[リソース グループ](~/articles/azure-resource-manager/resource-group-overview.md#terminology)を作成します。 `<RESOURCE GROUP>` と `<LOCATION>` のパラメーターの値は、必ず実際の値に置き換えてください。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az_identity_create) を使用してユーザー割り当て MSI を作成します。  `-g` パラメーターは MSI が作成されるリソース グループを指定し、`-n` パラメーターはその名前を指定します。 `<RESOURCE GROUP>` と `<MSI NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
応答には、次のように、作成されたユーザー割り当て MSI の詳細が含まれています。 MSI に割り当てられたリソース `id` 値は、次の手順で使用されます。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vm create](/cli/azure/vm/#az_vm_create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターで指定された新しいユーザー割り当て MSI に関連付けられている VM を作成します。 前の手順で作成した `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>`、および `<`MSI ID>` のパラメーターの値は、必ず実際の値に置き換えてください。`` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `` の値には、前の手順で作成したユーザー割り当て MSI のリソース`id` プロパティを使用してください。 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

1. [az identity create](/cli/azure/identity#az_identity_create) を使用してユーザー割り当て MSI を作成します。  `-g` パラメーターは MSI が作成されるリソース グループを指定し、`-n` パラメーターはその名前を指定します。 `<RESOURCE GROUP>` と `<MSI NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
応答には、次のように、作成されたユーザー割り当て MSI の詳細が含まれています。 MSI に割り当てられたリソース `id` 値は、次の手順で使用されます。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity) を使用して、ユーザー割り当て MSI を VM に割り当てます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<MSI ID>` は、前の手順で作成されたユーザー割り当て MSI のリソース `id` プロパティになります。

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

1. [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity) を使用して、ユーザー割り当て MSI を VM から削除します。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<MSI NAME>` は、`az identity create` コマンドで与えられたユーザー割り当て MSI の `name` プロパティになります (前のセクションの例を参照してください)。

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>次の手順

- [管理対象サービス ID の概要](msi-overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。 

  - [CLI で Windows 仮想マシンを作成する](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [CLI で Linux 仮想マシンを作成する](~/articles/virtual-machines/linux/quick-create-cli.md) 

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
















