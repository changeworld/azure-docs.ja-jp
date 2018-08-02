---
title: NVIDIA GPU ドライバー拡張機能 - Azure Linux VM | Microsoft Docs
description: Linux を実行中の N シリーズのコンピューティング VM に NVIDIA GPU ドライバーをインストールする Microsoft Azure 拡張機能です。
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: roiyz
ms.openlocfilehash: d95a1b510411f913a05762494dd48d6a5b6f84fd
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413673"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Linux 用の NVIDIA GPU ドライバー拡張機能

## <a name="overview"></a>概要

この拡張機能は、Linux N シリーズ VM に NVIDIA GPU ドライバーをインストールします。 VM ファミリに応じて、この拡張機能では CUDA ドライバーまたは GRID ドライバーがインストールされます。 この拡張機能を使用して NVIDIA ドライバーをインストールする際は、NVIDIA のエンドユーザー使用許諾契約書の条項を受け入れ、同意します。 インストール プロセス中に、ドライバーのセットアップを完了するために仮想マシンが再起動することがあります。

NVIDIA GPU ドライバーを [Windows の N シリーズ VM](hpccompute-gpu-windows.md) にインストールする拡張機能も利用可能です。

NVIDIA のエンドユーザー使用許諾契約書の条項については、 https://go.microsoft.com/fwlink/?linkid=874330 を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能は､次の OS に対応しています｡

| ディストリビューション | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3、7.4 |
| Linux: CentOS | 7.3、7.4 |

### <a name="internet-connectivity"></a>インターネット接続

NVIDIA GPU ドライバー用の Microsoft Azure 拡張機能では、ターゲットの仮想マシンがインターネットに接続され、アクセスできるようになっている必要があります。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、拡張機能のスキーマを示しています。

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>プロパティ値

| Name | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | 文字列 |
| type | NvidiaGpuDriverLinux | 文字列 |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>Deployment


### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート 

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成が必要な仮想マシンを 1 つ以上デプロイするときに最適です。

仮想マシン拡張機能の JSON 構成は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON 構成の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/resource-manager-template-child-resource.md)に関する記事を参照してください。 

次の例では、拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータは、Azure portal から取得することも、Azure PowerShell、Azure CLI を使用して取得することもできます。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>終了コード

| 終了コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 1 | 拡張機能の使い方に誤りがあります。 | 実行の出力ログを添えてサポートにお問い合せください。 |
| 10 | Hyper-V と Azure 用の Linux Integration Services が使用できないかインストールされていません。 | lspci の出力を確認します。 |
| 11 | この VM サイズで NVIDIA GPU が見つかりません。 | [サポートされている VM サイズと OS](../linux/n-series-driver-setup.md) を使用します。 |
| 14 | 操作が失敗しました | |
| 21 | Ubuntu 上で更新できませんでした | "sudo apt-get update" の出力を確認します。 |


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。

N シリーズ VM の詳細については、「[GPU 最適化済み仮想マシンのサイズ](../linux/sizes-gpu.md)」を参照してください。