---
title: Azure Automation の Update Management から VM を削除する
description: この記事では、Update Management から VM を削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836653"
---
# <a name="remove-vms-from-update-management"></a>Update Management から VM を削除する

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

環境内の VM に対する更新プログラムのデプロイが完了したら、それらを [Update Management](automation-update-management.md) 機能から削除できます。

1. Automation アカウントから、 **[更新の管理]** で **[Update Management]** を選択します。

2. 次のコマンドを使用して、管理から削除する VM の UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Log Analytics ワークスペースの **[全般]** で、保存された検索条件にアクセスします。

4. 保存された検索条件 `MicrosoftDefaultComputerGroup` で、右側の省略記号をクリックし、 **[編集]** を選択します。 

5. VM の UUID を削除します。

6. 削除する他の VM についても、この手順を繰り返します。

7. 編集が完了したら、保存された検索条件を保存します。 

## <a name="next-steps"></a>次のステップ

* Update Management で引き続き作業する場合は、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* 一般的な機能の問題を解決するには、[Update Management に関する問題のトラブルシューティング](troubleshoot/update-management.md)に関する記事を参照してください。
* Windows Update エージェントに関する問題については、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題については、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。