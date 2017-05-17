---
title: "Linux 用の OMS Azure 仮想マシン拡張機能 | Microsoft Docs"
description: "仮想マシン拡張機能を使用して、Linux 仮想マシンに OMS エージェントをデプロイします。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 05f823955eb5c47ce024c2b7d246e361e1302d78
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux 用の OMS 仮想マシン拡張機能

## <a name="overview"></a>概要

Operations Management Suite (OMS) は、クラウドとオンプレミスの資産全体にまたがる監視、アラート、アラート修復の機能を提供します。 Linux 用の OMS Agent 仮想マシン拡張機能は、Microsoft によって発行およびサポートされています。 この拡張機能は、Azure 仮想マシンに OMS エージェントをインストールし、仮想マシンを既存の OMS ワークスペースに登録します。 このドキュメントでは、Linux 用の OMS 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

OMS Agent 拡張機能は、次の Linux ディストリビューションに対して実行することができます。

| ディストリビューション | バージョン |
|---|---|
| CentOS Linux | 5、6 および 7 |
| Oracle Linux | 5、6 および 7 |
| Red Hat Enterprise Linux Server | 5、6 および 7 |
| Debian GNU/Linux | 6、7、8 |
| Ubuntu | 12.04 LTS、14.04 LTS、15.04、15.10、16.04 LTS |
| SUSE Linux Enterprise Server | 11、12 |

### <a name="internet-connectivity"></a>インターネット接続

Linux 用の OMS Agent 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、OMS Agent 拡張機能のスキーマを示しています。 この拡張機能では、ターゲット OMS ワークスペースのワークスペース ID とワークスペース キーが必要です (これらの値は OMS ポータルで確認できます)。 ワークスペース キーは機密データとして取り扱う必要があるため、保護された設定構成に格納される必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 **workspaceId** と **workspaceKey** の大文字と小文字は区別されることに注意してください。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.3 |
| workspaceId (例) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成 (OMS へのオンボードなど) が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 OMS Agent VM 拡張機能を含む Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)にあります。 

仮想マシン拡張機能の JSON は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/resource-manager-template-child-resource.md)に関する記事を参照してください。 

次の例では、OMS 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

拡張機能 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれて、種類は入れ子になっている構成を反映します。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、OMS Agent VM 拡張機能を既存の仮想マシンにデプロイすることができます。 OMS キーと OMS ID は、実際の OMS ワークスペースのものに置き換えてください。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.0 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>エラー コードとその意味

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 2 | シェル バンドルに提供されたオプションが無効です | |
| 3 | シェル バンドルにオプションが提供されていません | |
| 4 | パッケージの種類が無効です | |
| 5 | シェル バンドルはルートとして実行する必要があります | |
| 6 | パッケージ アーキテクチャが無効です | |
| 10 | VM は既に OMS ワークスペースに接続されています | 拡張スキーマに指定されたワークスペースに VM を接続するには、パブリック設定で stopOnMultipleConnections を false に設定するか、このプロパティを削除します。 この VM は、各ワークスペースに接続された後、課金されます。 |
| 11 | 拡張機能に提供された構成が無効です | 前の例に従って、デプロイするために必要なすべてのプロパティ値を設定します。 |
| 20 | SCX/OMIのインストールに失敗しました | |
| 21 | SCX/プロバイダー キットのインストールに失敗しました | |
| 22 | バンドルされているパッケージのインストールに失敗しました | |
| 23 | SCX または OMI パッケージは既にインストールされています | |
| 30 | バンドルの内部エラー | |
| 51 | VM のオペレーティング システムでは、この拡張機能はサポートされていません | |
| 60 | OpenSSL のバージョンがサポートされていません。 | [パッケージ要件](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#package-requirements)に適合する OpenSSL のバージョンをインストールします。 |
| 61 | Python ctypes ライブラリが不足しています | Python ctypes ライブラリまたはパッケージ (python-ctypes) をインストールします。 |
| 62 | tar プログラムが不足しています | tar をインストールします。 |
| 63 | sed プログラムが不足しています | sed をインストールします。 |

その他のトラブルシューティング情報については、「[Troubleshooting Guide for OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#)」(Linux 用の OMS エージェントに関するトラブルシューティン グガイド) を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/en-us/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。

