---
title: Azure CLI または PowerShell から IoT Central を管理する | Microsoft Docs
description: この記事では、Azure CLI または PowerShell を使用して IoT Central アプリケーションを作成し、管理する方法について説明します。 これらのツールを使用して、アプリケーションを表示、変更、および削除できます。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: b2ba640fec6f9bf2cd09611456967e15ed8f7ade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732891"
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
| location          | このコマンドでは既定で、リソース グループの場所が使用されます。 現時点では、IoT Central アプリケーションは、**オーストラリア**、**アジア太平洋**、**ヨーロッパ**、または **米国**、**イギリス**、**日本** の地域で作成できます。 |
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
|場所 |既定で、このコマンドレットにはリソース グループの場所が使用されます。 現時点では、IoT Central アプリケーションは、**オーストラリア**、**アジア太平洋**、**ヨーロッパ**、または **米国**、**イギリス**、**日本** の地域で作成できます。 |
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

## <a name="next-steps"></a>次のステップ

ここでは、Azure CLI または PowerShell から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
