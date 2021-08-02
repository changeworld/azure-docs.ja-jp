---
title: Azure Automation の Update Management から VM を削除する
description: この記事では、Update Management で管理されているマシンを削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 06/03/2021
ms.custom: mvc
ms.openlocfilehash: 621367ba04893e7a8030b4a284125a6247c5d091
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854974"
---
# <a name="remove-vms-from-update-management"></a>Update Management から VM を削除する

環境内の VM に対する更新プログラムの管理が完了したら、[Update Management](overview.md) 機能を使用して VM の管理を停止できます。 この管理を停止するには、保存された検索クエリ `MicrosoftDefaultComputerGroup` を、自分の Automation アカウントにリンクされた Log Analytics ワークスペースで編集します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Azure portal で、Azure portal の上部のナビゲーションから **[Cloud Shell]** を起動します。 Azure Cloud Shell について初めて学ぶ場合は、「[Azure Cloud Shell の概要](../../cloud-shell/overview.md)」を参照してください。

2. 次のコマンドを使用して、管理から削除するマシンの UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

4. Log Analytics ワークスペースで、左側のメニューから **[コンピューター グループ]** を選択します。

5. 右側のウィンドウの **[コンピューター グループ]** では、 **[保存済みグループ]** タブが既定で表示されています。

6. テーブルで、**レガシ カテゴリ** の値が **Updates** の項目 **MicrosoftDefaultComputerGroup** の右側にある **[クエリの実行]** アイコンをクリックします。

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

    * **名前**: Updates__MicrosoftDefaultComputerGroup
    * **[コンピューター グループとして保存]** を選択
    * **レガシ カテゴリ**: 更新

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内に評価されたすべてのマシンについて報告するためです。 マシンを削除した後、マシンが一覧に表示されなくなるには、24 時間待つ必要があります。

## <a name="next-steps"></a>次のステップ

仮想マシンの管理を再度有効にするには、[Azure portal の参照による Update Management の有効化](enable-from-portal.md)に関するページまたは「[Azure VM から Update Management を有効にする](enable-from-vm.md)」を参照してください。