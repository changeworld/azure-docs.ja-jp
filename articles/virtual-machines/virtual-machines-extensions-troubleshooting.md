<properties
   pageTitle="Azure VM 拡張機能のエラーのトラブルシューティング | Microsoft Azure"
   description="Azure VM 拡張機能のエラーのトラブルシューティングについて説明します。"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Azure VM 拡張機能のエラーのトラブルシューティング

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。


## Azure リソース マネージャー テンプレートの概要

Azure リソース マネージャー テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。


拡張機能を使用するためのテンプレートの作成の詳細については、記事「[Authoring Extension Templates (拡張機能テンプレートの作成)](virtual-machines-extensions-authoring-templates.md)」を参照してください。

この記事では、VM 拡張機能で発生するいくつかの一般的なエラーのトラブルシューティングについて説明します。

## 拡張機能の状態表示:
Azure リソース マネージャー テンプレートは、Azure Powershell または Azure CLI から実行できます。このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。次に例をいくつか示します。

Azure CLI:

      azure vm get-instance-view

Azure Powershell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

出力例を次に示します。

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## 拡張機能のエラーのトラブルシューティング:

### VM での拡張機能の再実行:

VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートに戻ることです。注: 将来的には、拡張機能のアンインストールが不要になるように機能強化が行われるる予定です。

#### Azure CLI から拡張機能を削除する:

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

ここで、"publsher-name" は "azure vm get-instance-view" の出力の拡張機能の種類に対応し、name はテンプレートの拡張機能のリソース名に対応します。

#### Azure Powershell から拡張機能を削除する:

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。

<!---HONumber=Oct15_HO3-->