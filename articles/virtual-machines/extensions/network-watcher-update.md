---
title: Network Watcher 拡張機能を最新バージョンに更新する
description: Azure Network Watcher 拡張機能を最新バージョンに更新する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c427a206e0422e66cb526a29a462d8b6bdf6818e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965937"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Network Watcher 拡張機能を最新バージョンに更新する

## <a name="overview"></a>概要

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) は、Azure ネットワークを監視する、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン (VM) 拡張機能は、オンデマンドでネットワーク トラフィックをキャプチャするためと、Azure VM に関するその他の高度な機能を使用するための要件です。 Network Watcher 拡張機能は、接続モニター、接続モニター (プレビュー)、接続のトラブルシューティング、パケット キャプチャなどの機能で使用されています。

## <a name="prerequisites"></a>前提条件

この記事では、Network Watcher 拡張機能が VM にインストールされていることを前提としています。

## <a name="latest-version"></a>最新バージョン

現在の Network Watcher 拡張機能の最新バージョンは `1.4.1654.1` です。

## <a name="update-your-extension"></a>拡張機能を更新する

拡張機能を更新するには、拡張機能のバージョンを把握しておく必要があります。

### <a name="check-your-extension-version"></a>拡張機能のバージョンを確認する

拡張機能のバージョンは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。

#### <a name="usetheazureportal"></a>Azure portal を使用する

1. Azure portal で VM の **[拡張機能]** ペインにアクセスします。
1. **AzureNetworkWatcher** 拡張機能を選択して、詳細ペインを表示します。
1. **[バージョン]** フィールドに記載されているバージョン番号を見つけます。  

#### <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure CLI のプロンプトで、次のコマンドを実行します。

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
出力から **"AzureNetworkWatcherExtension"** を見つけて、出力の *“TypeHandlerVersion”* フィールドからバージョン番号を特定します。  注意:拡張機能に関する情報は、JSON 出力に複数回出現します。 "extensions" ブロックの下を確認してください。拡張機能の完全なバージョン番号が表示されるはずです。 

次のように表示されます。![Azure CLI のスクリーンショット](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>PowerShell を使用する

PowerShell プロンプトから、次のコマンドを実行します。

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
出力から Azure Network Watcher 拡張機能を見つけて、出力の *“TypeHandlerVersion”* フィールドからバージョン番号を特定します。   

次のように表示されます。![PowerShell のスクリーンショット](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>拡張機能を更新する

お使いのバージョンが、現在の最新バージョンである `1.4.1654.1` よりも古い場合は、次のいずれかのオプションを使用して拡張機能を更新します。

#### <a name="option-1-use-powershell"></a>オプション 1: PowerShell の使用

次のコマンドを実行します。

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

それでもうまくいかない場合。 次の手順に従って、拡張機能を削除してからもう一度インストールします。 これにより、最新バージョンが自動的に追加されます。

拡張機能の削除 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

拡張機能の再インストール

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>オプション 2:Azure CLI の使用

強制的にアップグレードします。

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

それでもうまくいかない場合は、拡張機能を削除してから再度インストールし、これらの手順に従って最新バージョンを自動的に追加します。

拡張機能を削除します。

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

拡張機能を再度インストールします。

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>オプション 3:VM を再起動する

Network Watcher 拡張機能の自動アップグレードが true に設定されている場合は、VM のインストールを再起動して最新の拡張機能にします。

## <a name="support"></a>サポート

この記事のいずれかについてさらにヘルプが必要な場合は、[Linux](./network-watcher-linux.md) または [Windows](./network-watcher-windows.md) 向けの Network Watcher 拡張機能のドキュメントを参照してください。 また、[MSDN の Azure と Stack Overflow のフォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることもできます。 または、Azure サポート インシデントを送信してください。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
