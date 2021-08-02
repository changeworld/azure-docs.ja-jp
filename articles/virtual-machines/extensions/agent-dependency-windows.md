---
title: Windows 用 Azure Monitor Dependency 仮想マシン拡張機能
description: 仮想マシン拡張機能を使用して Windows 仮想マシンに Azure Monitor Dependency Agent をデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: windows
ms.date: 06/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1de4facc6cc945b5cada2201d3da667efae793aa
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797359"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Windows 用 Azure Monitor Dependency 仮想マシン拡張機能

Azure Monitor for VMs のマップ機能では、Microsoft Dependency Agent からデータが取得されます。 Windows 用 Azure VM Dependency Agent 仮想マシン拡張機能は、Microsoft が公開し、サポートを行っています。 この拡張機能によって、Azure 仮想マシンに Dependency Agent がインストールされます。 このドキュメントでは、Windows 用 Azure VM Dependency Agent 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="operating-system"></a>オペレーティング システム

Windows 用 Azure VM Dependency Agent 拡張機能は、Azure Monitor for VMs のデプロイに関する記事の「[サポートされているオペレーティング システム](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)」セクションに記載されたサポート対象オペレーティング システムに対して実行できます。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Azure Windows VM に対する Azure VM Dependency Agent 拡張機能のスキーマを示したものです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
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
        "type": "DependencyAgentWindows",
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
| type | DependencyAgentWindows |
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
        "type": "DependencyAgentWindows",
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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMExtension` コマンドを使用して、Dependency Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="automatic-upgrade-preview"></a>自動アップグレード (プレビュー)
マイナー バージョンの依存関係拡張機能を自動的にアップグレードする新しい機能が、パブリック プレビューで利用できるようになりました。 この機能を有効にするには、次の構成変更を実行する必要があります。

-   「[プレビュー アクセスの有効化](../automatic-extension-upgrade.md#enabling-preview-access)」に示されているいずれかの方法を使用して、サブスクリプションの機能を有効にします。
- `enableAutomaticUpgrade` 属性をテンプレートに追加します。

Dependency Agent 拡張機能バージョン管理スキームは、次の形式に従います。

```
<MM.mm.bb.rr> where M = Major version number, m = minor version number, b = bug number, r = revision number.
```

`enableAutomaticUpgrade` と `autoUpgradeMinorVersion` の属性の組み合わせによって、サブスクリプション内の仮想マシンのアップグレードの処理方法が決定されます。

| enableAutomaticUpgrade | autoUpgradeMinorVersion | 結果 |
|:---|:---|:---|
| true | false | 新しいバージョンの bb.rr が存在する場合は、依存関係エージェントをアップグレードします。 たとえば、9.6.0.1355 を実行しており、新しいバージョンが 9.6.2.1366 になる場合、有効なサブスクリプションの仮想マシンは 9.6.2.1366 にアップグレードされます。 |
| true | true |  これにより、新しいバージョンの mm.bb.rr または bb.rr が存在する場合、依存関係エージェントがアップグレードされます。 たとえば、9.6.0.1355 を実行しており、新しいバージョンが 9.7.1.1416 になる場合、有効なサブスクリプションの仮想マシンは 9.7.1.1416 にアップグレードされます。 また、9.6.0.1355 を実行しており、新しいバージョンが 9.6.2.1366 になる場合、有効なサブスクリプションの仮想マシンは 9.6.2.1366 にアップグレードされます。 |
| false | true または false | 自動アップグレードが無効です。

> [!IMPORTANT]
> テンプレートに `enableAutomaticUpgrade` を追加する場合は、API バージョン 2019-12-01 以降を使用してください。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure portal または Azure PowerShell モジュールを使用します。 特定の VM での拡張機能のデプロイ状態を確認するには、Azure PowerShell モジュールを使用して次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
