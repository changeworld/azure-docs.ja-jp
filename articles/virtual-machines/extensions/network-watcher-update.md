---
title: Network Watcher 拡張機能を最新バージョンに更新する
description: Network Watcher 拡張機能を最新バージョンに更新する方法について説明します
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: fd3fff2d438bbf804e35f04db0cfae15eea5e782
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973341"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Network Watcher 拡張機能を最新バージョンに更新する方法 

## <a name="overview"></a>概要

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) は、Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン拡張機能は、オンデマンドでネットワーク トラフィックをキャプチャするためと、Azure 仮想マシンに関するその他の高度な機能を使用するための要件です。 Network Watcher 拡張機能は、接続モニター、接続モニター (プレビュー)、接続のトラブルシューティング、パケット キャプチャなどの機能で使用されています。   

## <a name="prerequisites"></a>前提条件
このドキュメントでは、Network Watcher 拡張機能が仮想マシンにインストールされている前提の下、これを最新バージョンに更新するための手順について説明します。 

## <a name="latest-version"></a>最新バージョン
現在の Network Watcher 拡張機能の最新バージョンは `1.4.1654.1` です。

## <a name="updating-your-extension"></a>拡張機能を更新する 

### <a name="check-your-extension-version"></a>拡張機能のバージョンを確認する  

**Azure portal を使用する**

1. Azure portal で VM の [拡張機能] ブレードにアクセスします。   
2. "AzureNetworkWatcher" 拡張機能をクリックすると、詳細ウィンドウが表示されます。  
3. [バージョン] フィールドに記載されているバージョン番号を見つけます。  

**Azure CLI を使用する** Azure CLI プロンプトから次のコマンドを実行します。   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

出力から AzureNetworkWatcher 拡張機能を見つけて、出力の "TypeHandlerVersion" フィールドからバージョン番号を特定します。  


**Powershell を使用する** PowerShell プロンプトから次のコマンドを実行します。   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

出力から AzureNetworkWatcher 拡張機能を見つけて、出力の "TypeHandlerVersion" フィールドからバージョン番号を特定します。   


### <a name="update-your-extension"></a>拡張機能を更新する

バージョンが `1.4.1654.1` (最新バージョン) よりも古い場合は、次のいずれかのオプションを使用して拡張機能を更新します。 

**オプション 1: PowerShell を使用する**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**オプション 2: Azure CLI を使用する**  

強制アップグレード 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

それでもうまくいかない場合。 次の手順に従って、拡張機能を削除してからもう一度インストールします。 これにより、最新バージョンが自動的に追加されます。 

拡張機能の削除 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

拡張機能の再インストール

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**オプション 3:VM を再起動する**

NetworkWatcher 拡張機能の自動アップグレードが true に設定されている場合は、 VM を再起動すると、最新の拡張機能がインストールされます。


## <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、Network Watcher 拡張機能のドキュメント ([Linux](./network-watcher-linux.md)、[Windows](./network-watcher-windows.md)) を参照してください。また、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせいただくこともできます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。