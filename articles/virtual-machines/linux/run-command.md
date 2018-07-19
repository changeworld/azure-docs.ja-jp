---
title: Azure 上の Linux VM でシェル スクリプトを実行する
description: このトピックでは、実行コマンドを使用して Azure Linux 仮想マシン内でスクリプトを実行する方法について説明します。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 850c5ac4df8ff3bd0e35567060b3b90dad7baacc
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342693"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>実行コマンドを使用して Linux VM でシェル スクリプトを実行する

実行コマンドは、VM エージェントを使用して Azure Linux VM 内でシェル スクリプトを実行します。 これらのスクリプトは、一般的なコンピューターまたはアプリケーションの管理に使用できるだけでなく、VM のアクセスやネットワークの問題をすばやく診断および修正し、その VM を正常な状態に戻すためにも使用できます。

## <a name="benefits"></a>メリット

仮想マシンにアクセスするために使用できるオプションは複数あります。 実行コマンドは、VM エージェントを使用して、リモートから仮想マシン上でスクリプトを実行できます。 実行コマンドは、Azure Portal、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)、[Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)、または [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) から使用できます。

この機能は、仮想マシン内でスクリプトを実行するすべてのシナリオで有効であり、誤ったネットワークまたは管理ユーザーの構成のために RDP または SSH ポートが開かれていない仮想マシンをトラブルシューティングして修正する最適な方法の 1 つです。

## <a name="restrictions"></a>制限

実行コマンドを使用する場合に存在する制限の一覧を次に示します。

* 出力は最後の 4096 バイトに制限される
* スクリプトを実行するための最小時間は約 20 秒
* スクリプトは Linux 上で既定では管理者特権で実行される
* 同時に実行できるスクリプトは 1 つ
* 情報の入力を求めるスクリプト (対話モード) はサポートされていません。
* スクリプトの実行を取り消すことはできない
* スクリプトを実行できる最大時間は 90 分であり、その後タイムアウトになる
* スクリプトの結果を返すために、VM からの送信接続が必要

## <a name="azure-cli"></a>Azure CLI

[az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) コマンドを使用して Azure Linux VM 上でシェル スクリプトを実行する例を次に示します。

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 別のユーザーとしてコマンドを実行するには、`sudo -u` を使用して、使用するユーザー アカウントを指定できます。

## <a name="azure-portal"></a>Azure ポータル

[Azure](https://portal.azure.com) で VM に移動し、**[操作]** で **[実行コマンド]** を選択します。 VM 上で実行できるコマンドの一覧が表示されます。

![実行コマンドの一覧](./media/run-command/run-command-list.png)

実行するコマンドを選択します。 コマンドによっては、省略可能または必須の入力パラメーターがある場合があります。 これらのコマンドの場合、パラメーターは、入力値を指定するためのテキスト フィールドとして表示されます。 コマンドごとに、**[スクリプトの表示]** を展開することによって、実行されるスクリプトを表示できます。 **RunShellScript** は、独自のカスタム スクリプトを指定できる点で他のコマンドとは異なります。 

> [!NOTE]
> 組み込みコマンドは編集できません。

コマンドを選択したら、**[実行]** をクリックしてスクリプトを実行します。 スクリプトが実行され、完了すると、出力ウィンドウで出力および発生したエラーが返されます。 次のスクリーンショットは、**ifconfig** コマンドの実行の出力例を示しています。

![実行コマンド スクリプトの出力](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>使用可能なコマンド

この表は、Linux VM で使用可能なコマンドの一覧を示しています。 **RunShellScript** コマンドを使用すると、必要な任意のカスタム スクリプトを実行できます。

|**名前**|**説明**|
|---|---|
|**RunShellScript**|Linux シェル スクリプトを実行します。|
|**ifconfig**| すべてのネットワーク インターフェイスの構成を取得します。|

## <a name="limiting-access-to-run-command"></a>実行コマンドへのアクセスの制限

実行コマンドを一覧表示したり、コマンドの詳細を表示したりするには、組み込みの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール以上が持っている `Microsoft.Compute/locations/runCommands/read` アクセス許可が必要です。

コマンドを実行するには、[共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロール以上が持っている `Microsoft.Compute/virtualMachines/runCommand/action` アクセス許可が必要です。

実行コマンドを使用するには、いずれかの[組み込み](../../role-based-access-control/built-in-roles.md)ロールを使用するか、または[カスタム](../../role-based-access-control/custom-roles.md) ロールを作成することができます。

## <a name="next-steps"></a>次の手順

VM 内でリモートからスクリプトやコマンドを実行するためのその他の方法の詳細については、[Linux VM でのスクリプトの実行](run-scripts-in-vm.md)に関するページを参照してください。
