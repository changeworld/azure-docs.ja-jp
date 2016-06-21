<properties
   pageTitle="Windows VM 拡張機能のエラーのトラブルシューティング | Microsoft Azure"
   description="Azure Windows VM 拡張機能のエラーのトラブルシューティングについて説明します"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Windows VM 拡張機能のエラーのトラブルシューティング

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## 拡張機能の状態表示
Azure リソース マネージャー テンプレートは、Azure Powershell から実行できます。このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。

たとえば次のようになります。

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

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

## 拡張機能のエラーのトラブルシューティング

### VM での拡張機能の再実行

VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートに戻ることです。注: 将来的には、拡張機能のアンインストールが不要になるように機能強化が行われる予定です。


#### Azure Powershell から拡張機能を削除する

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。

<!---HONumber=AcomDC_0608_2016-->