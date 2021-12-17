---
title: マネージド実行コマンド (プレビュー) を使用して Azure の Linux VM でスクリプトを実行する
description: このトピックでは、更新されたマネージド実行コマンド機能を使用して Azure Linux 仮想マシン内でスクリプトを実行する方法について説明します
services: automation
ms.service: virtual-machines
ms.collection: linux
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71c08740161fd3df80757c6d86a10dec3e917198
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554813"
---
# <a name="preview-run-scripts-in-your-linux-vm-by-using-managed-run-commands"></a>プレビュー: マネージド実行コマンドを使用して Linux VM でスクリプトを実行する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

> [!IMPORTANT]
> **マネージド実行コマンド** は、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

実行コマンド機能では、仮想マシン (VM) エージェントを使用して Azure Linux VM 内でスクリプトが実行されます。 これらのスクリプトは、マシンやアプリケーションの一般的な管理に使用できます。 これらを使用すれば、VM のアクセスおよびネットワークの問題を迅速に診断して修正し、VM を良好な状態に戻すことができます。

*更新された* マネージド実行コマンドでは、同じ VM エージェント チャネルを使用してスクリプトを実行し、[元のアクション指向実行コマンド](run-command.md)に対して次の機能強化を実現します。 
- ARM デプロイ テンプレートを介して更新された実行コマンドをサポート 
- 複数のスクリプトの並列実行 
- スクリプトの順次実行 
- RunCommand スクリプトを取り消し可能  
- ユーザーが指定したスクリプトのタイムアウト 
- 実行時間が長い (時間または日単位) のスクリプトをサポート 
- 安全な方法でシークレット (パラメーター、パスワード) を渡す


## <a name="register-for-preview"></a>プレビューの利用登録

パブリック プレビュー中にマネージド実行コマンドを使用するためには、ご利用のサブスクリプションを登録する必要があります。 登録手順については、[Azure サブスクリプションでのプレビュー機能の設定](../../azure-resource-manager/management/preview-features.md)に関するページを参照し、機能名として `RunCommandPreview` を使用してください。

## <a name="azure-cli"></a>Azure CLI 

次の例では、[az vm run-command](/cli/azure/vm/run-command) を使用して、Azure Azure Linux VM 上でシェル スクリプトを実行します。

### <a name="execute-a-script-with-the-vm"></a>VM でスクリプトを実行する
このコマンドを実行すると、スクリプトが VM に配信されて実行され、キャプチャされた出力が返されます。

```azurecli-interactive
az vm run-command create --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "echo Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>VM 上にデプロイされているすべての RunCommand リソースを一覧表示する 
このコマンドからは、以前にデプロイされた実行コマンドと、それらのプロパティの完全な一覧が返されます。 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>実行の状態と結果を取得する 
このコマンドを実行すると、現在の実行の進行状況 (最新の出力、開始/終了時刻、終了コード、および実行の終了状態など) が取得されます。

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>VM から RunCommand リソースを削除する
VM に以前にデプロイされた RunCommand リソースを削除します。 スクリプトの実行がまだ進行中の場合は、実行が終了されます。 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>VM でスクリプトを実行する
このコマンドを実行すると、スクリプトが VM に配信されて実行され、キャプチャされた出力が返されます。

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" – Script "echo Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>VM 上にデプロイされているすべての RunCommand リソースを一覧表示する 
このコマンドからは、以前にデプロイされた実行コマンドと、それらのプロパティの完全な一覧が返されます。

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>実行の状態と結果を取得する 
このコマンドを実行すると、現在の実行の進行状況 (最新の出力、開始/終了時刻、終了コード、および実行の終了状態など) が取得されます。

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>VM から RunCommand リソースを削除する
VM に以前にデプロイされた RunCommand リソースを削除します。 スクリプトの実行がまだ進行中の場合は、実行が終了されます。 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName"
```
 


## <a name="rest-api"></a>REST API 

新しい実行コマンドをデプロイするには、VM 上で PUT を直接実行し、実行コマンド インスタンスに一意の名前を指定します。 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "echo Hello World", 
        "scriptUri": "<URI>",  
        "commandId": "<Id>"  
        }, 
    "parameters": [ 
        { 
            "name": "param1",
            "value": "value1" 
            }, 
        { 
            "name": "param2", 
            "value": "value2" 
            } 
        ], 
    "protectedParameters": [ 
        { 
            "name": "secret1", 
            "value": "value1" 
            }, 
        { 
            "name": "secret2", 
            "value": "value2" 
            } 
        ], 
    "runAsUser": "userName",
    "runAsPassword": "userPassword", 
    "timeoutInSeconds": 3600, 
    "outputBlobUri": "<URI>", 
    "errorBlobUri": "<URI>"  
    }
}
```

### <a name="notes"></a>Notes
 
- 入力ソースとして、インライン スクリプト、スクリプト URI、または組み込みのスクリプト [コマンド ID](run-command.md#available-commands) を指定できます。
- 1 回のコマンド実行でサポートされるソース入力の種類は 1 つだけです 
- 実行コマンドでは Storage BLOB への出力がサポートされています。これにより、大きなスクリプト出力を格納できます
- 実行コマンドでは、Storage BLOB へのエラー出力がサポートされています 

### <a name="list-running-instances-of-run-command-on-a-vm"></a>VM 上で実行中の、実行コマンドのインスタンスを一覧表示する 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>特定の実行コマンドのデプロイについて出力の詳細を取得する 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>特定の実行コマンドのデプロイを取り消す 

実行中のデプロイを取り消すには、実行コマンドの実行中のインスタンスに対して PUT または PATCH を実行し、要求本文に空のスクリプトを指定します。 これにより、進行中の実行が取り消されます。 

実行コマンドのインスタンスを削除することもできます。  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>順序付けられたシーケンスでスクリプトをデプロイする 

スクリプトを順番にデプロイするには、デプロイ テンプレートを使用して、シーケンシャル スクリプト間に `dependsOn` リレーションシップを指定します。 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "echo Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>複数の実行コマンドを順番に実行する 

デプロイ テンプレートを使用して複数の RunCommand リソースをデプロイする場合、既定では、それらは VM 上で同時に実行されます。 スクリプト同士に依存関係があり、実行の優先順序が設定されている場合は、`dependsOn` プロパティを使用してそれらを順番に実行することができます。 

この例では、**firstRunCommand** が実行されてから、**secondRunCommand** が実行されます。 

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "resources":[
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/firstRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
         ],
         "properties":{
            "source":{
               "script":"echo First: Hello World!"
            },
            "parameters":[
               {
                  "name":"param1",
                  "value":"value1"
               },
               {
                  "name":"param2",
                  "value":"value2"
               }
            ],
            "timeoutInSeconds":20
         }
      },
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/secondRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'),'runcommands/firstRunCommand')]"
         ],
         "properties":{
            "source":{
               "scriptUrl":"http://github.com/myscript.ps1"
            },
            "timeoutInSeconds":60
         }
      }
   ]
}
```


## <a name="next-steps"></a>次のステップ

VM においてリモートでスクリプトやコマンドを実行するその他の方法の詳細については、「[Linux VM でスクリプトを実行する](run-scripts-in-vm.md)」を参照してください。
