---
title: "Windows 用の OMS Azure 仮想マシン拡張機能 | Microsoft Docs"
description: "仮想マシン拡張機能を使用して、Windows 仮想マシンに OMS エージェントをデプロイします。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: d65505182267bec053fca0ab5045cbcae9381ac7
ms.openlocfilehash: 245e49d36142ef8e927a5e494113e4dd7809fc16


---
# <a name="oms-virtual-machine-extension-for-windows"></a>Windows 用の OMS 仮想マシン拡張機能

## <a name="overview"></a>概要

Operations Management Suite (OMS) は、クラウドとオンプレミスの資産全体にまたがる監視、アラート、アラート修復の機能を提供します。 Windows 用の OMS Agent 仮想マシン拡張機能は、Microsoft によって発行およびサポートされています。 この拡張機能は、Azure 仮想マシンに OMS エージェントをインストールし、仮想マシンを既存の OMS ワークスペースに登録します。 このドキュメントでは、Windows 用の OMS 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

Azure 仮想マシン拡張機能の全般的な情報については、[仮想マシン拡張機能の概要](./virtual-machines-windows-extensions-features.md)に関するページを参照してください。

Operations Management Suite の詳細については、[Operations Management Suite の概要](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Windows 用の OMS Agent 拡張機能は、Windows Server 2012、2012 R2、2016 の各リリースで実行できます。

### <a name="connectivity"></a>接続

Windows 用の OMS Agent 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-configuration"></a>拡張機能の構成

Windows 用の OMS Agent 仮想マシン拡張機能では、ターゲット OMS ワークスペースのワークスペース ID とワークスペース キーが必要です。 ワークスペース キーは機密データとして取り扱う必要があるため、保護された構成に格納されます。 Azure VM 拡張機能の保護された構成データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 パブリック構成とプライベート構成はデプロイ時に指定します。詳細については、このドキュメントの後続のセクションで詳しく説明します。

### <a name="public-configuration"></a>パブリック構成

パブリック構成のスキーマ:

- workspaceId: (必須、string) 仮想マシンのオンボード先の OMS ワークスペース ID。

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>プライベート構成

プライベート構成のスキーマ:

- workspaceKey: (必須、string) ワークスペースのプライマリ/セカンダリの共有キー。

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成 (OMS へのオンボードなど) が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 OMS Agent VM 拡張機能を含む Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)にあります。 

このサンプルは、次のボタンを使用してこのドキュメントからデプロイできます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-windows-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

OMS Agent VM 拡張機能のデプロイに使用する JSON は、次の例のようになります。

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceKey"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceId"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzureRmVMExtension` コマンドを使用して、OMS Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

`
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
`

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/en-us/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。



<!--HONumber=Dec16_HO1-->


