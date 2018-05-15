---
title: Azure DevTest Labs のカスタム ラボにアクセスする | Microsoft Docs
description: このチュートリアルでは、Azure DevTest Labs を使って作成されたラボにアクセスし、仮想マシンを要求して使用した後、それらを解放します。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: ce4522673bac56f73944413d102b7cb36cf93f30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>チュートリアル: Azure DevTest Labs のカスタム ラボにアクセスする
このチュートリアルでは、「[チュートリアル: カスタム ラボを作成する](tutorial-create-custom-lab.md)」で作成したカスタム ラボを使います。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * カスタム ラボの仮想マシン (VM) を要求する
> * VM に接続する
> * VM を解放する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="access-the-lab"></a>ラボにアクセスする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、**[すべてのリソース]** を選びます。 
3. リソースの種類として **[DevTest Labs]** を選びます。 
4. ラボを選びます。 

    ![ラボを選ぶ](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>VM を要求する

1. **[要求可能な仮想マシン]** の一覧で、**[...]** (省略記号) を選び、**[マシンの要求]** を選びます。

    ![仮想マシンを要求する](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **[自分の仮想マシン]** の一覧に VM が表示されることを確認します。

    ![自分の仮想マシン](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM に接続する

1. 一覧で VM を選びます。 選んだ VM の**仮想マシン ページ**が表示されます。 ツール バーの **[接続]** を選びます。

    ![仮想マシンへの接続](./media/tutorial-use-custom-lab/connect-button.png)
2. ダウンロードした **RDP** ファイルをハード ディスクに保存し、それを使って仮想マシンに接続します。 前のセクションで VM を作成するときに設定したユーザー名とパスワードを指定します。 

## <a name="unclaim-the-vm"></a>VM を解放する
VM を使い終わったら、次の手順で VM を解放します。 

1. 仮想マシンのページで、ツール バーの **[解放]** を選びます。 

    ![VM を解放する](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. VM は、解放される前にシャットダウンされます。 

    ![状態を解放する](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. 解放操作が完了すると、下部にある **[要求可能な仮想マシン]** の一覧に VM が表示されます。 
    
## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure DevTest Labs を使って作成されたカスタム ラボにアクセスして使用する方法を示しました。 カスタム ラボの VM にアクセスして使用する方法について詳しくは、次をご覧ください。 

> [!div class="nextstepaction"]
> [方法: カスタム ラボの VM を使用する](devtest-lab-add-vm.md)

