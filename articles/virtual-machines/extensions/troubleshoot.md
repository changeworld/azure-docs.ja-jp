---
title: Windows VM 拡張機能のエラーのトラブルシューティング
description: Azure Windows VM 拡張機能のエラーのトラブルシューティングについて説明します
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292791"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 拡張機能のエラーのトラブルシューティング
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>拡張機能の状態表示
Azure リソース マネージャー テンプレートは、Azure Powershell から実行できます。 このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。

たとえば次のようになります。

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

出力例を次に示します。

```output
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
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>拡張機能のエラーのトラブルシューティング

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>VM エージェントが実行されていて、準備完了であることを確認する
拡張機能を管理、インストール、および実行するには、VM エージェントが必要です。 VM エージェントが実行されていない場合、または準備完了状態を Azure プラットフォームに報告できない場合、拡張機能は正しく機能しません。

VM エージェントのトラブルシューティングについては、次のページを参照してください。
- Windows VM の [Windows Azure ゲスト エージェントのトラブルシューティング](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)
- Linux VM の [Azure Linux エージェントのトラブルシューティング](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent)

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>特定の拡張機能のトラブルシューティング ガイドを確認する
一部の拡張機能には、トラブルシューティングの方法を説明する特定のページがあります。 これらの拡張機能とページの一覧は、「[拡張機能のトラブルシューティングを行う](./overview.md#troubleshoot-extensions)」で確認できます。

### <a name="view-the-extensions-status"></a>拡張機能の状態を表示する
上で説明したように、拡張機能の状態は、PowerShell コマンドレットを実行することで確認できます。
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

または CLI コマンドを使用します。
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

または、Azure portal で、VM ブレード / 設定 / 拡張機能にアクセスします。 拡張機能をクリックして、その状態とメッセージを確認できます。


### <a name="rerun-the-extension-on-the-vm"></a>VM での拡張機能の再実行
VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。 このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートを実行することです。
注:将来は、この機能が強化され、拡張機能をアンインストールする必要はなくなります。

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell から拡張機能を削除する
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。

### <a name="trigger-a-new-goalstate-to-the-vm"></a>新しい GoalState を VM にトリガーする
拡張機能が実行されていないか、"Windows Azure CRP Certificate Generator" がないために実行できないことに気づくことがあります (この証明書は、拡張機能の保護された設定の転送をセキュリティで保護するために使用されます)。
その証明書は、仮想マシン内から Windows ゲスト エージェントを再起動することで自動的に再生成されます。
- タスク マネージャーを開く
- [詳細] タブに移動する
- WindowsAzureGuestAgent.exe プロセスを見つける
- 右クリックして [タスクの終了] を選択する。 プロセスは自動的に再起動されます


"VM の再適用" を実行して、VM に新しい GoalState をトリガーすることもできます。 VM の[再適用](/rest/api/compute/virtualmachines/reapply)は、2020 年に導入された API であり、VM の状態を再適用します。 VM の短時間のダウンタイムを許容できるタイミングでこれを行うことをお勧めします。 再適用自体では VM は再起動されず、また再適用を呼び出しても大抵の場合は VM が再起動されることはありませんが、再適用によって新しい目標状態がトリガーされたときに VM モデルに対する他の一部の保留中の更新が適用され、他の変更で再起動が必要になるという非常に小さなリスクがあります。 

Azure portal:

ポータルで VM を選択し、左ペインの **[サポート + トラブルシューティング]** で、 **[Redeploy + reapply]\(再デプロイ + 再適用\)** を選択し、続いて **[再適用]** を選択します。


Azure PowerShell *(RG 名と VM 名を実際の値に置き換えてください)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(RG 名と VM 名を実際の値に置き換えてください)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

"VM の再適用" が機能しなかった場合は、Azure の管理ポータルから VM に新しい空のデータ ディスクを追加し、証明書が再び追加された後でそれを削除できます。


### <a name="look-at-the-extension-logs-inside-the-vm"></a>VM 内の拡張機能ログを確認する

前の手順が機能しなかった場合、拡張機能がまだ失敗の状態である場合は、次の手順として、仮想マシン内のログを確認します。

**Windows** VM では、拡張機能ログは通常、次に存在します 
```
C:\WindowsAzure\Logs\Plugins
```
拡張機能の設定と状態ファイルは、次に存在します 
```
C:\Packages\Plugins
```
<br/>

**Linux** VM では、拡張機能ログは通常、次に存在します 
```
/var/log/azure/
```
拡張機能の設定と状態ファイルは、次に存在します 
```
/var/lib/waagent/
```


拡張機能により異なりますが、通常は同様の原則に従います。

拡張機能パッケージとバイナリは VM にダウンロードされます (例: Linux の場合は _"/var/lib/waagent/custom-script/download/1"_ 、Windows の場合は _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0"_ )。 

それらの構成と設定は、VM エージェントを介して Azure Platform から拡張機能ハンドラーに渡されます (例: Linux の場合は _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config"_ 、Windows の場合は _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings"_ )

VM 内の拡張機能ハンドラーは、状態ファイル (例: Linux の場合は _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status"_ 、Windows の場合は _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status"_ ) に書き込み、それが Azure Platform に報告されます。 この状態は、PowerShell、CLI、または Azure portal の VM の拡張機能ブレードで報告されたものです。

また、実行の詳細なログも書き込まれます (例: Linux の場合は _"/var/log/azure/custom-script/handler.log"_ 、Windows の場合は _"C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log"_ )。


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>VM が既存の VM から再作成された場合

別の Azure VM から特殊化されたディスクに基づいて Azure VM を作成している可能性があります。 その場合、古い VM には拡張機能が含まれていた可能性があるので、バイナリ、ログ、状態ファイルが残ってしまうことになります。 新しい VM モデルでは、以前の VM の拡張機能の状態は認識されません。また、これらの拡張機能の状態が正しくないと報告される可能性があります。 新しい VM を作成する前に、古い VM から拡張機能を削除し、新しい VM の作成後にこれらの拡張機能を再インストールすることを強く推奨します。
同じことが、既存の Azure VM から汎用化イメージを作成するときに発生する可能性があります。 拡張機能から整合性のない状態を回避するために、拡張機能を削除することをお勧めします。