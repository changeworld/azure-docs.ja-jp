---
title: REST を使用して Azure VMSS でシステム割り当て ID とユーザー割り当て ID を構成する方法
description: REST API を呼び出す CURL を使用して、Azure VMSS でシステム割り当て ID とユーザー割り当て ID を構成する方法をステップ バイ ステップで説明します。
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
ms.openlocfilehash: c4ccd75f679e58dd42f136bf1199eda88dc3f087
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431717"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>REST API 呼び出しを使用して仮想マシン スケール セットでマネージド ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID では、Azure Active Directory で自動マネージド システム ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットで次のマネージド ID 操作を実行する方法について説明します。

- Azure 仮想マシン スケール セットでシステム割り当て ID を有効および無効にする
- Azure 仮想マシン スケール セットでユーザー割り当て ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - 仮想マシン スケール セットを作成して、システム割り当てマネージド ID またはユーザー 割り当てマネージド ID を有効化したり、仮想マシン スケール セットから削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - 仮想マシン スケール セットからユーザー割り当て ID を割り当てたり削除するための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。
- Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) をインストールするか、Azure Portal で [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。
- [Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) または [Linux ディストリビューション OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) を使用する場合は、[Azure CLI ローカル コンソールをインストール](/azure/install-azure-cli)します。
- Azure CLI ローカル コンソールを使用している場合は、システム割り当て ID またはユーザー割り当て ID を管理する Azure サブスクリプションに関連付けられているアカウントで `az login` を使用して Azure にサインインします。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットでシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にシステム割り当て ID を有効にする

システム割り当て ID を有効にして仮想マシン スケール セットを作成するには、仮想マシン スケール セットを作成し、システム割り当て ID の種類の値を指定して Resource Manager エンドポイントを呼び出す CURL を使用するためのアクセス トークンを取得する必要があります。

1. [az group create](/cli/azure/group/#az-group-create) を使用して、仮想マシン スケール セットとその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 仮想マシン スケール セット用の[ネットワーク インターフェイス](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットを作成します。 次の例では、値 `"identity":{"type":"SystemAssigned"}` からの要求本文で指定されたシステム割り当て ID を使用して、*myResourceGroup* 内の *myVMSS* という仮想マシン スケール セットを作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにおいてシステム割り当て ID を有効にする

既存の仮想マシン スケール セットでシステム割り当て ID を有効にするには、アクセス トークンを取得し、ID の種類を更新するために Resource Manager REST エンドポイントを呼び出す CURL を使用する必要があります。

1. Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. *myVMSS* という仮想マシン スケール セットの値 `{"identity":{"type":"SystemAssigned"}` により要求本文で指定された仮想マシン スケール セットでシステム割り当て ID を有効にするには、Azure Resource Manager REST エンドポイントを呼び出す以下の CURL コマンドを使用します。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
   
   > [!IMPORTANT]
   > 仮想マシン スケール セットに割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当て ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 応答の `identity` 値で指定されたユーザー割り当て ID が仮想マシン スケール セットに割り当てられている場合は、仮想マシン スケール セットでシステム割り当て ID を有効にしている間、ユーザー割り当て ID を保持する方法を示す手順 3 にスキップします。

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 既存のユーザー割り当て ID を持つ仮想マシン スケール セットでシステム割り当て ID を有効にするには、`SystemAssigned` を `type` 値に追加する必要があります。  
   
   たとえば、仮想マシン スケール セットにユーザー割り当て ID `ID1` と `ID2`が割り当てられている状態で、仮想マシン スケール セットにシステム割り当て ID を追加する場合は、次の CURL 呼び出しを使用します。 `<ACCESS TOKEN>` と `<SUBSCRIPTION ID>` は、ご利用の環境に適した値に置き換えます。
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当て ID を無効にする

既存の仮想マシン スケール セットでシステム割り当て ID を無効にするには、アクセス トークンを取得し、ID の種類を `None` に更新するために Resource Manager REST エンドポイントを呼び出す CURL を使用する必要があります。

1. Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. システム割り当て ID を無効にするには、Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットを更新します。  次の例では、*myVMSS* という仮想マシン スケール セットから値 `{"identity":{"type":"None"}}` によって要求本文で指定されたシステム割り当て ID を無効にします。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   > [!IMPORTANT]
   > 仮想マシン スケール セットに割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当て ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"` ユーザー割り当て ID が仮想マシン スケール セットに割り当てられている場合は、仮想マシン スケール セットでシステム割り当て ID を削除している間、ユーザー割り当て ID を保持する方法を示す手順 3 にスキップします。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. ユーザー割り当て ID を持つ仮想マシン スケール セットからシステム割り当て ID を削除するには、`UserAssigned` 値と、仮想マシン スケール セットに割り当てられるユーザー割り当て ID を定義する `identityIds` 配列を維持したまま、`{"identity":{"type:" "}}` 値から `SystemAssigned` を削除します。

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットでユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にユーザー割り当て ID を割り当てる

1. Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 仮想マシン スケール セット用の[ネットワーク インターフェイス](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 「[Create a user assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」 (ユーザー割り当てマネージド ID を作成する) に示されている手順を使用して、ユーザー割り当て ID を作成します。

5. Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、仮想マシン スケール セットを作成します。 次の例では、値 `"identity":{"type":"UserAssigned"}` からの要求本文で指定されたユーザー割り当て ID `ID1` を使用して、*myResourceGroup* リソース グループ内の *myVMSS* という仮想マシン スケール セットを作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにユーザー割り当て ID を割り当てる

1. Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  「[Create a user assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」 (ユーザー割り当てマネージド ID を作成する) に示されている手順を使用して、ユーザー割り当て ID を作成します。

3. 仮想マシン スケール セットに割り当てられている既存のユーザー割り当てマネージド ID やシステム割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用して、仮想マシン スケール セットに割り当てられている ID の種類を一覧表示する必要があります。 仮想マシン スケール セットにマネージド ID が割り当てられている場合、`identity` 値に一覧表示されます。
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. 仮想マシン スケール セットにユーザー割り当て ID またはシステム割り当て ID が割り当てられていない場合は、仮想マシン スケール セットに最初のユーザー割り当て ID を割り当てるために Azure Resource Manager REST エンドポイントを呼び出す次の CURL コマンドを使用します。  ユーザー割り当て ID またはシステム割り当て ID が仮想マシン スケール セットに割り当てられている場合は、システム割り当て ID を保持しながら、仮想マシン スケール セットに複数のユーザー割り当て ID を追加する方法を示す手順 5 にスキップします。

   次の例では、*myResourceGroup* リソース グループ内の *myVMSS* という仮想マシン スケール セットに、ユーザー割り当て ID `ID1` を割り当てます。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. ユーザー割り当て ID またはシステム割り当て ID がご利用の仮想マシン スケール セットに割り当てられている場合は、`identityIDs` 配列に新しいユーザー割り当て ID を追加する必要があります。その間も、仮想マシン スケール セットに現在割り当てられているユーザー割り当て ID およびシステム割り当て ID を保持します。

   たとえば、現在、仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID `ID1` が割り当てられており、これにユーザー ID `ID2` を追加する場合は、次の CURL コマンドを使用します。 `<ACCESS TOKEN>` は、Bearer アクセス トークンを要求したときの手順で受け取った値に置き換え、`<SUBSCRIPTION ID>` 値は、ご利用の環境に合わせて置き換えます。

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>仮想マシン スケール セットからユーザー割り当て ID を削除する

1. Bearer アクセス トークンを取得します。このトークンは、次の手順で、Authorization ヘッダーでシステム割り当てマネージド ID を使用して仮想マシン スケール セットを作成するときに使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 仮想マシン スケール セットに割り当てたままにする既存のユーザー割り当てマネージド ID を削除しない、またはシステム割り当て ID を削除しないようにするには、次の CURL コマンドを使用して、マネージド ID を一覧表示する必要があります。 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   VM にマネージド ID が割り当てられている場合、応答の `identity` 値に一覧表示されます。 
    
   たとえば、ご利用の仮想マシン スケール セットにユーザー割り当て ID `ID1` と `ID2` が割り当てられている状態で、`ID1` を割り当てたままにし、システム割り当て ID を保持する場合は、`ID1` 値のみを維持しながら、仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てるときと同じ CURL コマンドを使用して、`SystemAssigned` 値を保持します。 これにより、システム割り当て ID を保持したまま、仮想マシン スケールから `ID2` ユーザー割り当て ID が削除されます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID の両方がある場合は、次のコマンドを使用してシステム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
仮想マシン スケール セットにユーザー割り当て ID のみがあり、そのすべてを削除する場合は、次のコマンドを使用します。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>次の手順

REST を使用してユーザー割り当て ID を作成、一覧表示、削除する方法については、以下を参照してください。

- [REST API 呼び出しを使用して、ユーザー割り当て ID を作成、一覧表示、削除する](how-to-manage-ua-identity-rest.md)