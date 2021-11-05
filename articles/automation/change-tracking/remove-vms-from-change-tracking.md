---
title: Azure Automation の変更履歴とインベントリから マシンを削除する
description: この記事では、Azure マシンと Azure 以外のマシンを変更履歴とインベントリから削除する方法を説明しています。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 810b2ac388b0f387183f735437ccc2b2a3918cfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026301"
---
# <a name="remove-machines-from-change-tracking-and-inventory"></a>変更履歴とインベントリからマシンを削除する

環境内の Azure マシンまたは Azure 以外のマシン に対する変更の追跡が完了したら、[変更履歴とインベントリ](overview.md)機能を使用してそれらの管理を停止できます。 この管理を停止するには、保存された検索クエリ `MicrosoftDefaultComputerGroup` を、自分の Automation アカウントにリンクされた Log Analytics ワークスペースで編集します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="to-remove-your-machines"></a>マシンを削除するには

1. Azure portal で、Azure portal の上部のナビゲーションから **[Cloud Shell]** を起動します。 Azure Cloud Shell について初めて学ぶ場合は、「[Azure Cloud Shell の概要](../../cloud-shell/overview.md)」を参照してください。

2. 次の方法を使用して、管理から削除する Azure 仮想マシンまたは Azure 以外のマシンの UUID を特定します。

   # <a name="azure-vm"></a>[Azure VM](#tab/azure-vm)

   ```azurecli
   az vm show -g MyResourceGroup -n MyVm -d
   ```

   # <a name="non-azure-machine"></a>[Azure 以外のマシン](#tab/non-azure-machine)

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30d)
   | where ComputerEnvironment == "Non-Azure"
   | summarize by Computer, VMUUID
   ```

   ---

3. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

4. Log Analytics ワークスペースで、左側のメニューから **[コンピューター グループ]** を選択します。

5. 右側のウィンドウの **[コンピューター グループ]** では、 **[保存済みグループ]** タブが既定で表示されています。

6. テーブルで、**レガシ カテゴリ** の値が **ChangeTracking** の項目 **MicrosoftDefaultComputerGroup** の右側にある **[クエリの実行]** アイコンをクリックします。

7. クエリ エディターで、クエリを確認して VM の UUID を見つけます。 VM の UUID を削除して、削除する他の VM にもこの手順を繰り返します。

   > [!NOTE]
   > 保護を強化するには、編集を行う前に、クエリのコピーを作成します。 これにより、問題が発生した場合に、復元することができます。

   元のクエリで開始し、クリーンアップまたはメンテナンスのアクティビティをサポートするマシンを再追加する場合は、次のクエリをコピーします。

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. 編集が完了したら、上部のバーから **[保存] > [関数として保存]** を選択して、保存された検索を保存します。 メッセージが表示されたら、次のとおり指定します。

    * **名前**: ChangeTracking__MicrosoftDefaultComputerGroup
    * **[Save as computer group]\(コンピューター グループとして保存\)** を選択
    * **レガシ カテゴリ**: ChangeTracking

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内に評価されたすべてのマシンについて報告するためです。 マシンを削除した後、マシンが一覧に表示されなくなるには、24 時間待つ必要があります。

## <a name="next-steps"></a>次のステップ

この機能を再度有効にするには、「[Automation アカウントから変更履歴とインベントリを有効にする](enable-from-automation-account.md)」、[Azure portal の参照による変更履歴とインベントリの有効化](enable-from-portal.md)に関する記事、「[Runbook から変更履歴とインベントリを有効にする](enable-from-runbook.md)」、または「[Azure VM から変更履歴とインベントリを有効にする](enable-from-vm.md)」を参照してください。