---
title: Azure Lab Services でクラスルーム ラボのテンプレートを管理する | Microsoft Docs
description: Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する方法について学習します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 08fbe9565356dc1b7db952fdd265770fef600ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989044"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 その後、テンプレートを発行し、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。

この記事では、Azure Lab Services のクラスルーム ラボでテンプレート仮想マシンを作成し、管理する方法について説明します。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>クラスルーム ラボを作成するとき、テンプレートを発行する
クラスルーム ラボの作成中、テンプレートを発行する方法については、「[クラスルーム ラボを作成する](how-to-manage-classroom-labs.md#create-a-classroom-lab)」を参照してください。
 
## <a name="set-or-update-template-title-and-description"></a>テンプレートのタイトルと説明を設定または更新する
タイトルと説明を初めて設定して後で更新する場合は、次の手順を実行します。 

1. **[テンプレート]** ページで、ラボの新しい**タイトル**を入力します。  
2. テンプレートの新しい**説明**を入力します。 テキスト ボックスからフォーカスを外すと、内容が自動的に保存されます。 

    ![テンプレートの名前と説明](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>テンプレート VM を更新する
テンプレート VM は次の手順で更新します。  

1. テンプレート VM が起動するまで待ってから、ツール バーで **[Connect to template]\(テンプレートに接続する\)** を選択してテンプレート VM に接続し、指示に従います。 Windows コンピューターの場合、RDP ファイルをダウンロードするオプションが表示されます。 
1. テンプレートに接続し、変更を加えると、ユーザーに最後に公開した仮想マシンと同じ設定ではなくなります。 再び公開するまで、ユーザーの既存の仮想マシンにはテンプレートの変更が反映されません。

    ![テンプレート VM に接続する](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. 学生がラボの作業を行うために必要なソフトウェア (Visual Studio、Azure Storage Explorer など) をインストールします。 
1. テンプレート VM から切断 (リモート デスクトップ セッションを終了) します。 
1. **[Stop template]\(テンプレートの停止\)** を選択してテンプレート VM を**停止**します。 
1. 更新後のテンプレート VM を**発行**するには、次のセクションの手順を行います。 

## <a name="publish-the-template-vm"></a>テンプレート VM を発行する  
ラボの作成時にテンプレートを発行しない場合、後で発行できます。 発行前に、テンプレート VM に接続し、ソフトウェアで更新することをお勧めします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成された VM の数は、初めて発行したときに指定した VM の数か、仮想マシン プール ページで指定した数です。 すべての仮想マシンの構成は、テンプレートと同じになります。 

1. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 
1. **[Publish the template]\(テンプレートの発行\)** メッセージ ボックスでメッセージを確認し、 **[発行]** を選択します。 作成する VM の数によっては、このプロセスにしばらく時間がかかる場合があります。

    ![[発行] ボタン](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > いったん発行したテンプレートの発行を取り消すことはできません。 ただし、テンプレートは再発行できます。 
1. [テンプレート] ページで、発行プロセスの状態を確認できます。 テンプレートの状態が **[発行済み]** に変わるまで待ちます。 

    ![発行済み状態](../media/how-to-create-manage-template/publish-status.png)
1. **[仮想マシン]** ページに切り替えて、 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 VM が作成されるまで待ちます。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
