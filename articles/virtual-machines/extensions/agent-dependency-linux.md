---
title: Linux 用 Azure Monitor Dependency 仮想マシン拡張機能
description: 仮想マシン拡張機能を使用して Linux 仮想マシンに Azure Monitor Dependency Agent をデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: ce4b7ee2fea9d5b2b7c92b5ade1b3ad146382214
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744980"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Linux 用 Azure Monitor Dependency 仮想マシン拡張機能

Azure Monitor for VMs のマップ機能では、Microsoft Dependency Agent からデータが取得されます。 Linux 用 Azure VM Dependency Agent 仮想マシン拡張機能は、Microsoft が公開し、サポートを行っています。 この拡張機能によって、Azure 仮想マシンに Dependency Agent がインストールされます。 このドキュメントでは、Linux 用 Azure VM Dependency Agent 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Linux 用 Azure VM Dependency Agent 拡張機能は、Azure Monitor for VMs のデプロイに関する記事の「[サポートされているオペレーティング システム](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)」セクションに記載されたサポート対象オペレーティング システムに対して実行できます。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Azure Linux VM に対する Azure VM Dependency Agent 拡張機能のスキーマを示したものです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に Azure VM Dependency Agent 拡張機能を実行できます。

仮想マシン拡張機能の JSON は、仮想マシン リソース内に入れ子にすることができます。 または、Resource Manager JSON テンプレートのルートまたは最上位に配置することができます。 JSON の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/templates/child-resource-name-type.md)に関する記事を参照してください。

次の例では、Dependency Agent 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

拡張子 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれます。 種類には、入れ子にされた構成が反映されます。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Dependency Agent VM 拡張機能を既存の仮想マシンにデプロイすることができます。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5
```

## <a name="automatic-extension-upgrade"></a>拡張機能の自動アップグレード
依存関係拡張機能の[マイナー バージョンを自動的にアップグレード](../automatic-extension-upgrade.md)する新しい機能が利用できるようになりました。

拡張機能の自動アップグレードを拡張機能に対して有効にするには、プロパティ `enableAutomaticUpgrade` が `true` に設定され、拡張機能テンプレートに追加されているようにする必要があります。 このプロパティは、すべての VM または VM スケール セットで個別に有効にする必要があります。 [有効化](../automatic-extension-upgrade.md#enabling-automatic-extension-upgrade)のセクションで説明されているいずれかの方法を使用して、VM または VM スケール セットに対してこの機能を有効にします。

VM または VM スケール セットで拡張機能の自動アップグレードが有効になっていると、拡張機能の発行元がその拡張機能の新しいバージョンをリリースするたびに、拡張機能が自動的にアップグレードされます。 このアップグレードは、[こちら](../automatic-extension-upgrade.md#how-does-automatic-extension-upgrade-work)で説明されているように、可用性優先の原則に従って安全に適用されます。

`enableAutomaticUpgrade` 属性の機能は、`autoUpgradeMinorVersion` の機能とは異なります。 拡張機能の発行元が新しいバージョンがリリースしたとき、`autoUpgradeMinorVersion` 属性はマイナー バージョンの更新を自動的にトリガーしません。 `autoUpgradeMinorVersion` 属性は、デプロイ時に新しいマイナー バージョンが使用可能な場合に、それを拡張機能で使用する必要があるかどうかを示します。 ただし、デプロイされると、このプロパティが true に設定されていても、再デプロイされない限り、拡張機能でマイナー バージョンのアップグレードは行われません。

拡張機能のバージョンを最新の状態に保つために、拡張機能のデプロイで `enableAutomaticUpgrade` を使用することをお勧めします。

> [!IMPORTANT]
> テンプレートに `enableAutomaticUpgrade` を追加する場合は、必ず API バージョン 2019-12-01 以降で使用してください。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
/opt/microsoft/dependency-agent/log/install.log
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせてください。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
