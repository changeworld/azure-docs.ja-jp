---
title: REST を使用して Azure VM でシステム割り当て ID とユーザー割り当て ID を構成する方法
description: CURL を使用して REST API を呼び出し、Azure VM でシステム割り当て ID とユーザー割り当て ID を構成する詳しい手順。
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 7926944f329665af2df287d120bd9f4a8ee78380
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433937"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>REST API 呼び出しを使用して Azure VM でマネージド ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID では、Azure Active Directory で自動マネージド システム ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、Azure VM で次のマネージド ID 操作を実行する方法について説明します。

- Azure VM においてシステム割り当ての ID を有効および無効にする
- Azure VM においてユーザー割り当ての ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - VM を作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、Azure VM から削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - ユーザー割り当て ID を VM に割り当てたり、VM から削除したりするための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。
- Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) をインストールするか、Azure Portal で [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。
- [Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) または [Linux ディストリビューション OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) を使用する場合は、[Azure CLI ローカル コンソールをインストール](/azure/install-azure-cli)します。
- Azure CLI ローカル コンソールを使用している場合は、システム割り当て ID またはユーザー割り当て ID を管理する Azure サブスクリプションに関連付けられているアカウントで `az login` を使用して Azure にサインインします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、Azure VM でシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中にシステム割り当て ID を有効にする

システム割り当て ID を有効にして Azure VM を作成するには、VM を作成して、アクセス トークンを取得し、CURL を使用して、システム割り当て ID の種類の値を指定して Resource Manager エンドポイントを呼び出す必要があります。

1. [az group create](/cli/azure/group/#az-group-create) を使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. ご利用の VM の[ネットワーク インターフェイス](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、VM を作成します。 次の例では、値 `"identity":{"type":"SystemAssigned"}` によって要求本文で識別される、システム割り当て ID を使用して *myVM* という VM を作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>既存の Azure VM においてシステム割り当て ID を有効にする

既存の VM でシステム割り当て ID を有効にするには、アクセ ストークンを取得し、CURL を使用して Resource Manager REST エンドポイントを呼び出し、ID の種類を更新する必要があります。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. ご利用の VM で、*myVM* という VM の値 `{"identity":{"type":"SystemAssigned"}` により要求本文で識別される、システム割り当て ID を有効にするには、以下の CURL コマンドを使用して Azure Resource Manager REST エンドポイントを呼び出します。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
   
   > [!IMPORTANT]
   > VM に割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当て ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 応答の `identity` 値で識別される、ユーザー割り当て ID が VM に割り当てられている場合は、VM でシステム割り当て ID を有効にしている間、ユーザー割り当て ID を保持する方法を示す手順 3 に進みます。

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 既存のユーザー割り当て ID を持つ VM でシステム割り当て ID を有効にするには、`SystemAssigned` を `type` 値に追加する必要があります。  
   
   たとえば、VM にユーザー割り当て ID `ID1` と `ID2`が割り当てられている状態で、VM にシステム割り当て ID を追加する場合は、次の CURL 呼び出しを使用します。 `<ACCESS TOKEN>` と `<SUBSCRIPTION ID>` は、ご利用の環境に適した値に置き換えます。
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Azure VM でシステム割り当て ID を無効にする

既存の VM でシステム割り当て ID を無効にするには、アクセ ストークンを取得し、CURL を使用して Resource Manager REST エンドポイントを呼び出し、ID の種類を `None` に更新する必要があります。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、システム割り当て ID を無効にして VM を更新します。  次の例では、*myVM* という VM から値 `{"identity":{"type":"None"}}` によって要求本文で識別される、システム割り当て ID を無効にします。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   > [!IMPORTANT]
   > VM に割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当て ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 応答の `identity` 値で識別される、ユーザー割り当て ID が VM に割り当てられている場合は、VM でシステム割り当て ID を無効にしている間、ユーザー割り当て ID を保持する方法を示す手順 3 に進みます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. ユーザー割り当て ID を持つ VM からシステム割り当て ID を削除するには、`UserAssigned` 値と `identityIds` 配列 (VM に割り当てられるユーザー割り当て ID を定義する) を保持したまま、`{"identity":{"type:" "}}` 値から `SystemAssigned` を削除します。

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、Azure VM でユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Azure VM の作成中にユーザー割り当て ID を割り当てる

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. ご利用の VM の[ネットワーク インターフェイス](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 「[Create a user assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」 (ユーザー割り当てマネージド ID を作成する) に示されている手順を使用して、ユーザー割り当て ID を作成します。

5. CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、VM を作成します。 次の例では、値 `"identity":{"type":"UserAssigned"}` によって要求本文で識別される、ユーザー割り当て ID `ID1` を使用して、リソース グループの *myResourceGroup* で *myVM* という VM を作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>ユーザー割り当て ID を既存の Azure VM に割り当てる

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  「[Create a user assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」 (ユーザー割り当てマネージド ID を作成する) に示されている手順を使用して、ユーザー割り当て ID を作成します。

3.  VM に割り当てられている既存のユーザーまたはシステム割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用して、VM に割り当てられている ID の種類を一覧表示する必要があります。 仮想マシン スケール セットにマネージド ID が割り当てられている場合、`identity` 値に一覧表示されます。

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    応答の `identity` 値で識別される、ユーザーまたはシステム割り当て ID が VM に割り当てられている場合は、VM でシステム割り当て ID を有効にしている間、ユーザー割り当て ID を保持する方法を示す手順 5 に進みます。

4. VM にユーザー割り当て ID が割り当てられていない場合は、次の CURL コマンドを使用して Azure Resource Manager REST エンドポイントを呼び出し、VM に最初のユーザー割り当て ID を割り当てます。  VM にユーザー割り当て ID が割り当てられている場合は、VM に複数のユーザー割り当て ID を追加する方法を示す次の手順に進みます。

   次の例では、リソース グループの *myResourceGroup* 内の *myVM* という VM に、ユーザー割り当て ID `ID1` を割り当てます。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. ユーザーまたはシステム割り当て ID がご利用の VM に割り当てられている場合は、VM に現在割り当てられているユーザーおよびシステム割り当て ID を保持しながら、`identityIDs` 配列に新しいユーザー割り当て ID を追加する必要があります。

   たとえば、現在、ご利用の VM にシステム割り当て ID とユーザー割り当て ID `ID1` が割り当てられている状態で、ユーザー ID `ID2` を追加する場合は、次の CURL コマンドを使用します。 `<ACCESS TOKEN>` は、Bearer アクセス トークンを要求したときの手順で受け取った値に置き換え、`<SUBSCRIPTION ID>` 値は、ご利用の環境に合わせて置き換えます。

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当て ID を削除する

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. VM に割り当てたままにする既存のユーザー割り当てマネージド ID を削除しないようにするか、システム割り当て ID を削除するには、次の CURL コマンドを使用して、マネージド ID を一覧表示する必要があります。 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   VM にマネージド ID が割り当てられている場合、応答の `identity` 値に一覧表示されます。

   たとえば、ご利用の VM にユーザー割り当て ID `ID1` と `ID2` が割り当てられている状態で、`ID1` を割り当てたままにし、システム割り当て ID を保持する場合は、`ID1` 値のみを保持しながら VM にユーザー割り当てマネージド ID を割り当てるときと同じ CURL コマンドを使用し、`SystemAssigned` 値を保持します。 これにより、システム割り当て ID を保持したまま、VM から `ID2` ユーザー割り当て ID が削除されます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

VM にシステム割り当て ID とユーザー割り当て ID の両方がある場合は、次のコマンドを使用して、システム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
VM にユーザー割り当て ID のみがあり、そのすべてを削除する場合は、次のコマンドを使用します。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>次の手順

REST を使用してユーザー割り当て ID を作成、一覧表示、削除する方法については、以下を参照してください。

- [REST API 呼び出しを使用して、ユーザー割り当て ID を作成、一覧表示、削除する](how-to-manage-ua-identity-rest.md)