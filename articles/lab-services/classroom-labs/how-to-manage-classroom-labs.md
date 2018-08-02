---
title: Azure Lab Services でクラスルーム ラボを管理する | Microsoft Docs
description: クラスルーム ラボの作成と構成、すべてのクラスルーム ラボの表示、ラボ ユーザーとのリンクの共有、またはラボの削除を実行する方法を説明します。
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 48056d6e2988dd674351aca83526032175c355b6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214395"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボを管理する 
この記事では、クラスルーム ラボの作成と構成、すべてのクラスルーム ラボの表示、またはラボの削除を実行する方法を説明します。

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 ラボの作成者ロールには、ラボ所有者が「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」の記事の手順に従って他のユーザーを追加できます。

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. クラスルーム ラボの**名前**を指定します。 
    2. クラスルームで使う仮想マシンの**サイズ**を選びます。
    3. 仮想マシンの作成に使用する **[イメージ]** を選択します。
    4. ラボ内の仮想マシンにログインするために使用する **[既定の資格情報]** を指定します。
    7. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>利用ポリシーを構成する

1. **[Usage policy]\(利用ポリシー\)** を選びます。 
2. **[Usage policy]\(利用ポリシー\)** の設定で、ラボの使用を許可する**ユーザーの数**を入力します。
3. **[保存]** を選択します。 

    ![利用ポリシー](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>テンプレートを設定する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 テンプレートを発行して、対象のラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。  

### <a name="set-template-title-and-description"></a>テンプレートのタイトルと説明を設定する
1. **[テンプレート]** セクションで、テンプレート用の **[編集]**(鉛筆のアイコン) を選択します。 
2. **[User view]\(ユーザー ビュー\)** ウィンドウで、テンプレートの**タイトル**を入力します。
3. テンプレートの**説明**を入力します。
4. **[保存]** を選択します。

    ![クラスルーム ラボの説明](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>テンプレート VM を設定する
 テンプレート VM を学生に提供する前に、そのテンプレート VM に接続して必要なソフトウェアをインストールします。 

1. テンプレート仮想マシンの準備が完了するまで待ちます。 準備が完了すると、**[開始]** ボタンが有効になります。 VM を起動するには、**[開始]** を選択します。

    ![テンプレート VM を起動する](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. VM に接続するには、**[接続]** を選択して指示に従います。 

    ![テンプレート VM に接続する](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 学生がラボの作業を行うために必要なソフトウェア (Visual Studio、Azure Storage Explorer など) をインストールします。 
2. テンプレート VM から切断 (リモート デスクトップ セッションを終了) します。 
3. **[停止]** を選択してテンプレート VM を**停止**します。 

    ![テンプレート VM を停止する](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>テンプレートを発行する 
テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。 

1. **[テンプレート]** セクションの **[発行]** を選択します。 

    ![テンプレート VM を発行する](../media/tutorial-setup-classroom-lab/public-access.png)
1. **[発行]** ウィンドウの **[発行済み]** オプションを選択します。 
2. 次に、**[発行]** ボタンをクリックします。 作成する VM の数 (ラボへのアクセスが許可されるユーザーと同数) によっては、このプロセスにしばらく時間がかかる場合があります。
    
    > [!IMPORTANT]
    > いったん発行したテンプレートの発行を取り消すことはできません。 
4. **[仮想マシン]** ページに切り替えて、**[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 

    ![仮想マシン](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. VM が作成されるまで待ちます。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>登録リンクを学生に送信する

1. **[ダッシュボード]** ビューに切り替えます。 
2. **[User registration]\(ユーザー登録\)** タイルを選択します。

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、**[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスの **[閉じる]** を選択します。 

## <a name="view-all-classroom-labs"></a>すべてのクラスルーム ラボを表示する
1. [Azure Lab Services ポータル](https://labs.azure.com)に移動します。
2. 選択したラボ アカウント内にすべてのラボが表示されることを確認します。 

    ![すべてのラボ](../media/how-to-manage-classroom-labs/all-labs.png)
3. 上部にあるドロップダウン リストを使用して、別のラボ アカウントを選択できます。 選択したラボ アカウントのラボが表示されます。 

## <a name="delete-a-classroom-lab"></a>クラスルーム ラボを削除する
1. ラボ用のタイルで、隅にある 3 つのドット (...) を選択します。 

    ![ラボを選ぶ](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. **[削除]** を選択します。 

    ![[削除] ボタン](../media/how-to-manage-classroom-labs/delete-button.png)
3. **[ラボの削除]** ダイアログ ボックスで、**[削除]** を選択します。 

    ![[削除] ダイアログ ボックス](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>学生の VM を管理する
提供された登録リンクを使用して学生が Azure Lab Services に登録すると、**[仮想マシン]** タブに学生に割り当てられた VM が表示されます。 

![学生に割り当てられた仮想マシン](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

学生の VM 上では、次のタスクを実行できます。 

- VM が実行中の場合は、VM を停止します。 
- VM が停止している場合は、VM を起動します。 
- VM に接続します。 
- VM を削除します。 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してラボの設定を開始します。

- [クラスルーム ラボを設定する](how-to-manage-classroom-labs.md)
- [ラボを設定する](../tutorial-create-custom-lab.md)
