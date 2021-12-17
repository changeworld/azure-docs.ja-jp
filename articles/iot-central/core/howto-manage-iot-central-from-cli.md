---
title: Azure CLI または PowerShell から IoT Central を管理する | Microsoft Docs
description: この記事では、Azure CLI または PowerShell を使用して IoT Central アプリケーションを作成し、管理する方法について説明します。 これらのツールを使用して、アプリケーションを表示、変更、および削除できます。 また、セキュリティで保護されたデータのエクスポートを設定するために使用できるマネージド システム ID を構成することもできます。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: 979da680c3a5a9b70973fa7da00613f7ffbcf86b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088078"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>Azure CLI または PowerShell から IoT Central を管理する

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で IoT Central アプリケーションを作成および管理するのではなく、[Azure CLI](/cli/azure/) または [Azure PowerShell](/powershell/azure/) を使用してアプリケーションを管理できます。

JavaScript、Python、C#、Ruby、Go などの言語を使用する場合は、[Azure IoT Central ARM SDK サンプル](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) リポジトリで、Azure IoT Central アプリケーションを作成、更新、一覧表示、削除する方法を示すコード サンプルを参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> PowerShell コマンドを別の Azure サブスクリプションで実行する必要がある場合は、「[アクティブなサブスクリプションを変更する](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription)」を参照してください。

次のコマンドを実行して、[IoT Central モジュール](/powershell/module/az.iotcentral/)がお使いの PowerShell 環境にインストールされていることを確認します。

```powershell
Get-InstalledModule -name Az.I*
```

インストール済みモジュールのリストに **Az.IotCentral** が含まれていない場合は、次のコマンドを実行します。

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>アプリケーションの作成

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) コマンドを使用して、Azure サブスクリプション内に IoT Central アプリケーションを作成します。 次に例を示します。

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

これらのコマンドでは、最初に、米国東部リージョンにアプリケーション用のリソース グループを作成します。 次の表では、**az iot central app create** コマンドに使用されるパラメーターを説明しています。

| パラメーター         | 説明 |
| ----------------- | ----------- |
| resource-group    | そのアプリケーションを含むリソース グループ。 サブスクリプションにこのリソース グループが既に存在している必要があります。 |
| location          | このコマンドでは既定で、リソース グループの場所が使用されます。 現在、IoT Central アプリケーションは **オーストラリア東部**、**米国中部**、**米国東部**、**米国東部 2**、**東日本**、**北ヨーロッパ**、**東南アジア**、**英国南部**、**西ヨーロッパ**、**米国西部** の各リージョンで作成できます。 |
| name              | Azure portal 内のアプリケーションの名前。 特殊文字は使用しないでください。代わりに、小文字 (a から z)、数字 (0 から 9)、ダッシュ (-) を使用します。|
| subdomain         | アプリケーションの URL のサブドメイン。 この例では、アプリケーションの URL は `https://mysubdomain.azureiotcentral.com` です。 |
| sku               | 現在、**ST1** または **ST2** のいずれかを使用できます。 「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 |
| template          | 使用するアプリケーション テンプレート。 詳細については、後の表を参照してください。 |
| display-name      | UI に表示されるアプリケーションの名前。 |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) コマンドレットを使用して、Azure サブスクリプションで IoT Central アプリケーションを作成します。 次に例を示します。

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

このスクリプトでは、まずアプリケーション用に米国東部リージョンにリソース グループを作成します。 次の表は、**New-AzIotCentralApp** コマンドで使用されるパラメーターの説明です。

|パラメーター         |説明 |
|------------------|------------|
|ResourceGroupName |そのアプリケーションを含むリソース グループ。 サブスクリプションにこのリソース グループが既に存在している必要があります。 |
|場所 |既定で、このコマンドレットにはリソース グループの場所が使用されます。 現在、IoT Central アプリケーションは **オーストラリア東部**、**米国中部**、**米国東部**、**米国東部 2**、**東日本**、**北ヨーロッパ**、**東南アジア**、**英国南部**、**西ヨーロッパ**、**米国西部** の各リージョンで作成できます。 |
|名前              |Azure portal 内のアプリケーションの名前。 特殊文字は使用しないでください。代わりに、小文字 (a から z)、数字 (0 から 9)、ダッシュ (-) を使用します。 |
|Subdomain         |アプリケーションの URL のサブドメイン。 この例では、アプリケーションの URL は `https://mysubdomain.azureiotcentral.com` です。 |
|Sku               |現在、**ST1** または **ST2** のいずれかを使用できます。 「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 |
|Template          | 使用するアプリケーション テンプレート。 詳細については、後の表を参照してください。 |
|DisplayName       |UI に表示されるアプリケーションの名前。 |

---

### <a name="application-templates"></a>アプリケーション テンプレート

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

独自のアプリケーション テンプレートを作成した場合は、それを使用して新しいアプリケーションを作成できます。 アプリケーション テンプレートの入力を求められたら、アプリの [[アプリケーション テンプレートのエクスポート]](howto-create-iot-central-application.md#create-and-use-a-custom-application-template) セクションにある、エクスポートされたアプリの URL の共有可能なリンクに表示されるアプリ ID を入力します。

## <a name="view-applications"></a>アプリケーションの表示

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) コマンドを使用して、IoT Central アプリケーションを一覧表示し、メタデータを表示します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) コマンドレットを使用して、IoT Central アプリケーションを一覧表示し、メタデータを表示します。

---

## <a name="modify-an-application"></a>アプリケーションの変更

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) コマンドを使用して、IoT Central アプリケーションのメタデータを更新します。 アプリケーションの表示名を変更する場合の例を次に示します。

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) コマンドレットを使用して、IoT Central アプリケーションのメタデータを更新します。 アプリケーションの表示名を変更する場合の例を次に示します。

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>アプリケーションの削除

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) コマンドを使用して、IoT Central アプリケーションを削除します。 次に例を示します。

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) コマンドレットを使用して、IoT Central アプリケーションを削除します。 次に例を示します。

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="configure-a-managed-identity"></a>マネージド ID の構成

IoT Central アプリケーションでは、システムに割り当てられた[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して、[データ エクスポート先](howto-export-data.md#connection-options)への接続をセキュリティで保護することができます。

マネージド ID を有効にするには、[Azure portal - マネージド ID の構成](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)または [REST API](howto-manage-iot-central-with-rest-api.md) のいずれかを使用します。

:::image type="content" source="media/howto-manage-iot-central-from-cli/managed-identity.png" alt-text="Azure portal のマネージド ID を示すスクリーンショット。":::

マネージド ID を有効にした後、CLI を使用してロールの割り当てを構成できます。

ロールの割り当てを作成するには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用します。 たとえば、次のコマンドでは、最初にマネージド ID のプリンシパル ID が取得されます。 2 番目のコマンドでは、`MyIoTCentralResourceGroup` リソース グループのスコープで、`Azure Event Hubs Data Sender` のロールがプリンシパル ID に割り当てられます。

```azurecli-interactive
spID=$(az resource list -n myiotcentralapp --query [*].identity.principalId --out tsv)
az role assignment create --assignee $spID --role "Azure Event Hubs Data Sender" \
  --scope /subscriptions/<your subscription id>/resourceGroups/MyIoTCentralResourceGroup
```

ロールの割り当ての詳細については、以下を参照してください。

- [Azure Event Hubs の組み込みのロール](../../event-hubs/authenticate-application.md#built-in-roles-for-azure-event-hubs)
- [Azure Service Bus 用の組み込みロール](../../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus)
- [Azure Storage Services の組み込みロール](/rest/api/storageservices/authorize-with-azure-active-directory#manage-access-rights-with-rbac)

## <a name="next-steps"></a>次のステップ

ここでは、Azure CLI または PowerShell から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
