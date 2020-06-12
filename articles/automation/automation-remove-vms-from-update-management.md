---
title: Azure Automation の Update Management から VM を削除する
description: この記事では、Update Management から VM を削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 796cf18ae4dbab50eb7f968bda065ae0351f2ae8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169408"
---
# <a name="remove-vms-from-update-management"></a>Update Management から VM を削除する

環境内の VM に対する更新プログラムのデプロイが完了したら、それらを [Update Management](automation-update-management.md) 機能から削除できます。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Automation アカウントから、 **[更新の管理]** で **[Update Management]** を選択します。

2. 次のコマンドを使用して、管理から削除する VM の UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Log Analytics ワークスペースの **[全般]** で、保存したスコープの構成 `MicrosoftDefaultScopeConfig-Updates` の検索にアクセスします。

4. 保存された検索 `MicrosoftDefaultComputerGroup` で、右側の省略記号をクリックし、 **[編集]** を選択します。 

5. VM の UUID を削除します。

6. 削除する他の VM についても、この手順を繰り返します。

7. 編集が完了したら、保存された検索を保存します。 

## <a name="next-steps"></a>次のステップ

* Update Management で引き続き作業する場合は、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* 一般的な機能の問題を解決するには、[Update Management に関する問題のトラブルシューティング](troubleshoot/update-management.md)に関する記事を参照してください。
* Windows Update エージェントに関する問題については、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題については、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。