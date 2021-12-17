---
title: Azure CLI を使用して VM 拡張機能を有効にする
description: この記事では、Azure CLI を使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7294285cd6e4fa7c2bbfac859c3fec8dfa3a474
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440414"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI を使用して Azure VM 拡張機能を有効にする

この記事では、Azure CLI を使用して、Linux または Windows ハイブリッド マシンに対し、Azure Arc 対応サーバーでサポートされている VM 拡張機能のデプロイ、アップグレード、更新、アンインストールを行う方法を示します。

> [!NOTE]
> Azure Arc 対応サーバーでは、Azure 仮想マシンへの VM 拡張機能のデプロイと管理はサポートされていません。 Azure VM については、次の [VM 拡張機能の概要](../../virtual-machines/extensions/overview.md) に関する記事をご覧ください。

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

ConnectedMachine コマンドは、Azure CLI の一部として付属していません。 Azure CLI を使用して、Azure に接続し、Azure Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に ConnectedMachine 拡張機能を読み込む必要があります。 これらの管理操作はワークステーションから実行できます。Azure Arc 対応サーバー上で実行する必要はありません。

次のコマンドを実行して取得します。

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>拡張機能を有効にする

Azure Arc 対応サーバーで VM 拡張機能を有効にするには、`--machine-name`、`--extension-name`、`--location`、`--type`、`settings`、`--publisher` の各パラメーターと共に [az connectedmachine extension create](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) を使用します。

次の例では、Azure Arc 対応サーバーで Log Analytics VM 拡張機能を有効にします。

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "regionName" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

次の例では、Azure Arc 対応サーバーでカスタム スクリプト拡張機能を有効にします。

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "regionName" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

次の例では、Azure Arc 対応サーバーで Key Vault VM 拡張機能を有効にします。

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

次の例では、Azure Arc 対応 Windows サーバーで Microsoft Antimalware 拡張機能を有効にします。

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.Security" --type "IaaSAntimalware" --name "IaaSAntimalware" --settings '{"AntimalwareEnabled": true}'
```

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Azure Arc 対応サーバー上の VM 拡張機能の一覧を取得するには、`--machine-name` および `--resource-group` パラメーターと共に [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) を使用します。

例:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

既定では、Azure CLI コマンドの出力形式は JSON (JavaScript Object Notation) です。 既定の出力をリストまたはテーブルなどに変更するには、[az config set core.output=table](/cli/azure/reference-index) を使用します。 出力形式で 1 回のみ変更するために `--output` を任意のコマンドに追加することもできます。

次の例に、`az connectedmachine extension -list` コマンドの JSON 形式の出力の一部を示します。

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "regionName",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="update-extension-configuration"></a>拡張機能の構成を更新する

VM 拡張機能の中には、Arc 対応サーバーにインストールするために構成設定が必要になるものがあります。たとえば、カスタム スクリプト拡張機能や Log Analytics エージェント VM 拡張機能が該当します。 拡張機能の構成をアップグレードするには、[az connectedmachine extension update](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_update) を使用してください。

次に示したのは、カスタム スクリプト拡張機能を構成する方法の例です。

```azurecli
az connectedmachine extension update --name "CustomScriptExtension" --type "CustomScriptExtension" --publisher "Microsoft.HybridCompute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -lt 100 }\\\"\"}" --type-handler-version "1.10" --machine-name "myMachine" --resource-group "myResourceGroup"
```

## <a name="upgrade-extensions"></a>拡張機能のアップグレード

サポートされている VM 拡張機能は、新しいバージョンがリリースされた時点でその最新リリースにアップグレードできます。 VM 拡張機能をアップグレードするには、[az connectedmachine upgrade-extension](/cli/azure/connectedmachine) と、`--machine-name`、`--resource-group`、`--extension-targets` の各パラメーターを使用します。

`--extension-targets` パラメーターには、拡張機能と提供されている最新バージョンを指定する必要があります。 提供されている最新バージョンを調べるには、選択した Arc 対応サーバーの **[拡張機能]** ページを Azure portal で確認するか、[az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list) を実行してこの情報を入手します。

Windows 用の Log Analytics エージェント拡張機能を新しいバージョンにアップグレードするには、次のコマンドを実行します。

```azurecli
az connectedmachine upgrade-extension --machine-name "myMachineName" --resource-group "myResourceGroup --extension-targets  --extension-targets "{\"MicrosoftMonitoringAgent\":{\"targetVersion\":\"1.0.18053.0\"}}"" 
```

インストールされている VM 拡張機能のバージョンは、[az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) コマンドを実行することでいつでも確認できます。 拡張機能のバージョンは、`typeHandlerVersion` プロパティの値によって表されます。

## <a name="remove-extensions"></a>拡張機能を削除する

Azure Arc 対応サーバーにインストールされている VM 拡張機能を削除するには、`--extension-name`、`--machine-name`、`--resource-group` の各パラメーターと共に [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) を使用します。

たとえば、Linux 用の Log Analytics VM 拡張機能を削除するには、次のコマンドを実行します。

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

- [Azure portal](manage-vm-extensions-portal.md) で [Azure PowerShell](manage-vm-extensions-powershell.md) を使用するか、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。

- コマンドの詳細については、Azure CLI VM 拡張機能の[概要](/cli/azure/connectedmachine/extension)の記事を参照してください。
