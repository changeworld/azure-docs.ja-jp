---
title: Azure CLI を使用して Azure VMSS においてシステム割り当て ID とユーザー割り当て ID を構成する方法
description: Azure CLI を使用して、Azure VMSS においてシステム割り当て ID とユーザー割り当て ID を構成する方法をステップ バイ ステップで説明します。
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 6474b34abeceb58c2eff9e7a2d2237ec47e61933
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447525"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Azure CLI を使用して、Azure 仮想マシン スケール セットの管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用して、Azure 仮想マシン スケール セット (VMSS) において管理対象サービス ID に対する次の操作を実行する方法を説明します。
- Azure VMSS においてシステム割り当ての ID を有効および無効にする
- Azure VMSS においてユーザー割り当ての ID を追加および削除する


## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - 仮想マシン スケール セットを作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、仮想マシン スケール セットから削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - 仮想マシン スケール セットからユーザー割り当て ID を割り当てたり削除するための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョンをインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 以降) 。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure CLI を使用して、Azure VMSS においてシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当て ID を有効にする

システム割り当て ID を有効にして仮想マシン スケール セットを作成する場合:

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 仮想マシン スケール セットをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az-group-create) を使用して、仮想マシン スケール セットとその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az-vmss-create) を使用して仮想マシン スケール セットを作成します。 次の例では、`--assign-identity` パラメーターからの要求により、システム割り当て ID を使用して *myVM* という仮想マシン スケール セットを作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにおいてシステム割り当て ID を有効にする

既存の Azure 仮想マシン スケール セットにおいてシステム割り当て ID を有効にする必要がある場合:

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) コマンドを使用し、既存の VM に対するシステム割り当て ID を有効にします。

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当て ID を無効にする

システム割り当て ID は不要になったが、ユーザー割り当て ID はまだ必要な仮想マシン スケール セットがある場合は、次のコマンドを使用します。

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

システム割り当て ID は不要になった、ユーザー割り当て ID がない仮想マシンがある場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

MSI VM 拡張機能を削除するには、[az vmss identity remove](/cli/azure/vmss/identity/#az-vmss-remove-identity) コマンドを使用してシステム割り当て ID を VMSS から削除します。

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure CLI を使用してユーザー割り当て ID を有効にする方法と削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Azure VMSS の作成中にユーザー割り当て ID を割り当てる

このセクションでは、VMSS を作成する方法と、ユーザー割り当て ID を VMSS に割り当てる方法について説明します。 使用する VMSS が既にある場合は、このセクションをスキップして次のセクションに進んでください。

1. 使用するリソース グループが既にある場合は、この手順をスキップできます。 [az group create](/cli/azure/group/#az-group-create) を使用して、ユーザー割り当て ID の包含およびデプロイ用の[リソース グループ](~/articles/azure-resource-manager/resource-group-overview.md#terminology)を作成します。 `<RESOURCE GROUP>` と `<LOCATION>` のパラメーターの値は、必ず実際の値に置き換えてください。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az-identity-create) を使用してユーザー割り当て ID を作成します。  `-g` パラメーターではユーザー割り当て ID を作成するリソース グループを指定し、`-n` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   応答には、次のように、作成されたユーザー割り当て ID の詳細が含まれています。 ユーザー割り当て ID に割り当てられたリソース `id` 値は、次の手順で使用されます。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vmss create](/cli/azure/vmss/#az-vmss-create) を使用して VMSS を作成します。 次の例では、`--assign-identity` パラメーターで指定された新しいユーザー割り当て ID に関連付けられている VMSS を作成します。 `<RESOURCE GROUP>`、`<VMSS NAME>`、`<USER NAME>`、`<PASSWORD>`、および `<USER ASSIGNED IDENTITY ID>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY ID>` の場合は、前の手順で作成されたユーザー割り当て ID のリソース `id` プロパティを使用します。 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>ユーザー割り当て ID を既存の Azure VM に割り当てる

1. [az identity create](/cli/azure/identity#az-identity-create) を使用してユーザー割り当て ID を作成します。  `-g` パラメーターではユーザー割り当て ID を作成するリソース グループを指定し、`-n` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    > [!IMPORTANT]
    > 名前に特殊文字 (アンダースコアなど) が含まれるユーザー割り当て ID の作成は現在サポートされていません。 英数字を使用してください。 アップデートは後ほどご確認ください。  詳細については、[よく寄せられる質問と既知の問題](known-issues.md)に関する記事をご覧ください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
応答には、次のように、作成されたユーザー割り当て ID の詳細が含まれています。 ユーザー割り当て ID に割り当てられたリソース `id` 値は、次の手順で使用されます。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity#az-vm-assign-identity) を使用して、ユーザー割り当て ID を VMSS に割り当てます。 `<RESOURCE GROUP>` と `<VMSS NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY ID>` は、前の手順で作成されたユーザー割り当て ID のリソース `id` プロパティになります。

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当て ID を削除する

仮想マシン スケール セットからユーザー割り当て ID を削除するには、[az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove)を使用します。 `<RESOURCE GROUP>` と `<VMSS NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<MSI NAME>` はユーザー割り当て ID の `name` プロパティになり、`az vmss identity show` を使用して VM の ID セクションで求めることができます。

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```

仮想マシン スケール セットにシステム割り当て ID がなく、そこからユーザー割り当て ID をすべて削除する場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID の両方がある場合は、システム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。 次のコマンドを使用します。

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="next-steps"></a>次の手順

- 
  [管理対象サービス ID の概要](overview.md)
- Azure 仮想マシン スケール セットの全作成に関するクイック スタートについては、次を参照してください。 

  - [CLI を使用して仮想マシン スケール セットを作成する](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















