---
title: ラボにアクセスする
description: このチュートリアルでは、Azure DevTest Labs のラボにアクセスします。 仮想マシンを使用し、それを解放し、さらに要求します。
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: spelluru
ms.openlocfilehash: d5ace346c8f4b853e862b2e12b8b2adee55bab8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578542"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>チュートリアル: Azure DevTest Labs のラボにアクセスする

このチュートリアルでは、[チュートリアル: Azure DevTest Labs でラボを作成する](tutorial-create-custom-lab.md)の記事で作成したラボを使います。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボの VM に接続する
> * ラボの VM を解放する
> * ラボの仮想マシン (VM) を要求する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[Azure 仮想マシンを含む DevTest Labs 内のラボ](tutorial-create-custom-lab.md)。

## <a name="connect-to-the-lab-vm"></a>ラボの VM に接続する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **DevTest Labs** のラボに移動します。

1. **[自分の仮想マシン]** で自分の VM を選択します。

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines.png" alt-text="[自分の仮想マシン] の VM のスクリーンショット。":::

1. 上部のメニューで、 **[接続]** を選択します。 次に、マシンにダウンロードする `.rdp` ファイルを選択します。

    :::image type="content" source="./media/tutorial-use-custom-lab/vm-connect.png" alt-text="VM の [接続] ボタンのスクリーンショット。":::

1. **[リモート デスクトップ接続]** ダイアログ ボックスで、 **[接続]** を選択します

1. **[資格情報を入力してください]** ダイアログ ボックスでパスワードを入力し、 **[OK]** を選択します。

1. **[このリモート コンピューターの ID を識別できません。]** というダイアログ ボックスが表示されたら、 **[Don't ask me again for connections to this computer]\(今後このコンピューターへの接続についてメッセージを表示しない\)** のチェック ボックスを選択します。 次に、 **[はい]** を選択します。

    :::image type="content" source="./media/tutorial-use-custom-lab/remote-computer-verification.png" alt-text="リモート コンピューターの検証のスクリーンショット。":::

Linux VM に接続する手順については、[Azure での Linux VM への接続](../virtual-machines/linux/use-remote-desktop.md)に関するページを参照してください。 

## <a name="unclaim-the-lab-vm"></a>ラボの VM を解放する

VM の操作を完了したら、次の手順で VM を解放します。 

1. 前と同じ手順を使用して、DevTest Labs から VM を選択します。

1. **仮想マシン** のページで、上部のメニューから **[解放]** を選択します。 

    :::image type="content" source="./media/tutorial-use-custom-lab/virtual-machine-unclaim.png" alt-text="[解放] オプションのスクリーンショット。":::

1. VM は、解放される前にシャットダウンされます。 この動作の状態は、 **[通知]** で監視できます。

1. **仮想マシン** のページを閉じて、**DevTest Lab の [概要]** ページに戻ります。

1. **[個人用ラボ]** で、 **[要求可能な仮想マシン]** を選択します。 これで、その VM を要求できるようになります。

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines.png" alt-text="[要求可能な仮想マシン] のオプションのスクリーンショット。":::

## <a name="claim-a-lab-vm"></a>ラボの VM を要求する

VM を使用する必要がある場合は、VM を再度要求できます。

1. **[要求可能な仮想マシン]** の一覧で、**[...]** (省略記号) を選び、**[マシンの要求]** を選びます。

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines-claimed.png" alt-text="要求オプションのスクリーンショット。":::

1. **[自分の仮想マシン]** の一覧に VM が表示されることを確認します。

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines-2.png" alt-text="[自分の仮想マシン] に返された VM を示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースを削除して、Azure でラボと VM を実行するための課金が生じないようにします。 ラボ内の VM にアクセスするために次のチュートリアルを実行する場合は、そのチュートリアルを完了した後にリソースをクリーンアップできます。 それ以外の場合は、次の手順に従います。 

1. 作成したラボのホーム ページに戻ります。

1. 上部のメニューで **[削除]** を選択します。

   :::image type="content" source="./media/tutorial-use-custom-lab/portal-lab-delete.png" alt-text="ラボの [削除] ボタンのスクリーンショット。":::

1. **[それを削除してよろしいですか]** ページで、テキスト ボックスにラボの名前を入力し、 **[削除]** を選択します。

1. 削除中、画面の上部にある **[通知]** を選択すると進行状況を表示できます。 ラボの削除にはしばらく時間がかかります。 ラボが削除されたら、次の手順に進みます。

1. 既存のリソース グループ内にラボを作成した場合は、すべてのラボ リソースが削除されています。 このチュートリアル用に新しいリソース グループを作成した場合は、それが空になっていて削除できます。 ラボがまだ存在している場合にリソース グループが削除されることはありません。
    
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure DevTest Labs 内のラボにアクセスして使用する方法法を学びました。 ラボの VM にアクセスして使用する方法について詳しくは、次をご覧ください。

> [!div class="nextstepaction"]
> [方法: ラボでの VM の使用](devtest-lab-add-vm.md)
