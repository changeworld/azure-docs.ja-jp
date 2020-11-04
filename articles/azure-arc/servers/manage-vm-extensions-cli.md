---
title: Azure CLI を使用して VM 拡張機能を有効にする
description: この記事では、Azure CLI を使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462843"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI を使用して Azure VM 拡張機能を有効にする

この記事では、Azure CLI を使用して、Azure Arc 対応サーバーでサポートされている Azure VM 拡張機能を Linux または Windows ハイブリッド マシンにデプロイおよびアンインストールする方法を示します。

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>前提条件

[Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。

Azure CLI を使用して、Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に `ConnectedMachine` CLI 拡張機能をインストールする必要があります。 Arc 対応サーバーで次のコマンドを実行します。

```azurecli
az extension add connectedmachine
```

インストールが完了すると、次のメッセージが返されます。

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>拡張機能を有効にする

Arc 対応サーバーで VM 拡張機能を有効にするには、`--machine-name`、`--extension-name`、`--location`、`--type`、`settings`、`--publisher` の各パラメーターと共に [az connectedmachine machine-extension create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) を使用します。

次の例では、Arc 対応 Linux サーバーで Log Analytics VM 拡張機能を有効にします。

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

次の例では、Arc 対応 Linux サーバーでカスタム スクリプト拡張機能を有効にします。

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Arc 対応サーバー上の VM 拡張機能の一覧を取得するには、`--machine-name` および `--resource-group` パラメーターと共に [az connectedmachine machine-extension list](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) を使用します。

例:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

既定では、Azure CLI コマンドの出力形式は JSON (JavaScript Object Notation) です。 既定の出力をリストまたはテーブルなどに変更するには、[az configure --output](/cli/azure/reference-index) を使用します。 出力形式で 1 回のみ変更するために `--output` を任意のコマンドに追加することもできます。

次の例に、`az connectedmachine machine-extension -list` コマンドの JSON 形式の出力の一部を示します。

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>インストールされている拡張機能を削除する

Arc 対応サーバー上の VM 拡張機能を削除するには、`--extension-name`、`--machine-name`、`--resource-group` の各パラメーターと共に [az connectedmachine machine-extension delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) を使用します。

たとえば、Linux 用の Log Analytics VM 拡張機能を削除するには、次のコマンドを実行します。

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

- [PowerShell](manage-vm-extensions-powershell.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。
