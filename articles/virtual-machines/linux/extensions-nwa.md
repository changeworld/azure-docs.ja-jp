---
title: Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能 | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Linux 仮想マシンに Network Watcher Agent をデプロイします。
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5a33f183470ec3879344f0cfe335bab38f9ff30f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778667"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux 用 Network Watcher Agent 仮想マシン拡張機能

## <a name="overview"></a>概要

[Azure Network Watcher](/azure/network-watcher/) は、Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン拡張機能は、Azure 仮想マシンの一部の Network Watcher 機能に必要です。 これには、ネットワーク トラフィックのオンデマンドでのキャプチャなどの高度な機能が含まれます。

このドキュメントでは、Linux 用 Network Watcher Agent 仮想マシン拡張機能でサポートされているプラットフォームとデプロイ オプションについて詳しく説明します エージェントのインストールによって、仮想マシンが中断されることも、再起動が必要になることもありません。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Network Watcher Agent 拡張機能は、次の Linux ディストリビューションに対して実行できます。

| ディストリビューション | バージョン |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS、12.04 LTS |
| Debian | 7、8 |
| RedHat | 6、7 |
| Oracle Linux | 6.8 以降、7 |
| SUSE Linux Enterprise Server | 11、12 |
| OpenSUSE Leap | 42.3 以降 |
| CentOS | 6.5 以降、7 |
| CoreOS | 899.17.0 以降 |

現時点では、CoreOS はサポートされていません。

### <a name="internet-connectivity"></a>インターネット接続

一部の Network Watcher Agent 機能では、ターゲット仮想マシンがインターネットに接続されている必要があります。 送信接続を確立できない場合、一部の Network Watcher Agent 機能が正しく動作しなかったり、使用できなくなったりすることがあります。 詳細については、[Network Watcher のドキュメント](/azure/network-watcher/)をご覧ください。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Network Watcher Agent 拡張機能のスキーマを示しています。 現時点では、この拡張機能はユーザーが指定した設定を必要とせず、サポートもしていません。既定の構成が使用されます。

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>プロパティ値

| Name | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| 型 | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に Network Watcher Agent 拡張機能を実行できます。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Network Watcher Agent VM 拡張機能を既存の仮想マシンにデプロイできます。

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshooting"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、Network Watcher のドキュメントをご覧ください。また、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることもできます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
