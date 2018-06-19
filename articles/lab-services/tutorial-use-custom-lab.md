---
title: Azure DevTest Labs のラボへのアクセス | Microsoft Docs
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: cd623767c9627810afb64ca9185c991c5c9f3858
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638026"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>チュートリアル: Azure DevTest Labs のラボにアクセスする
このチュートリアルでは、[チュートリアル: Azure DevTest Labs でラボを作成する](tutorial-create-custom-lab.md)の記事で作成したラボを使います。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボの仮想マシン (VM) を要求する
> * VM に接続します
> * VM を解放する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="access-the-lab"></a>ラボにアクセスする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、**[すべてのリソース]** を選択します。 
3. リソースの種類として **[DevTest Labs]** を選びます。 
4. ラボを選びます。 

    ![ラボを選ぶ](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>VM を要求する

1. **[要求可能な仮想マシン]** の一覧で、**[...]** (省略記号) を選び、**[マシンの要求]** を選びます。

    ![仮想マシンを要求する](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **[自分の仮想マシン]** の一覧に VM が表示されることを確認します。

    ![自分の仮想マシン](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM に接続します

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
このチュートリアルでは、Azure DevTest Labs を使って作成されたラボにアクセスして使用する方法を示しました。 ラボの VM にアクセスして使用する方法について詳しくは、次をご覧ください 

> [!div class="nextstepaction"]
> [方法: ラボでの VM の使用](devtest-lab-add-vm.md)

