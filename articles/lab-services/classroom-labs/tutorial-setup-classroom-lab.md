---
title: Azure Lab Services を使用してクラスルーム ラボを設定する | Microsoft Docs
description: このチュートリアルでは、クラスルームで使用するためのラボを設定します。
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: babff55d6684feb1f0414970616260be96b994f4
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706009"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>チュートリアル: クラスルーム ラボを設定する 
このチュートリアルでは、クラスルームで学生が使用する仮想マシンで、クラスルーム ラボを設定します。  

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * クラスルーム ラボを作成する
> * クラスルーム ラボを構成する
> * 登録リンクを学生に送信する

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 ラボの作成者ロールには、ラボ所有者が「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」の記事の手順に従って他のユーザーを追加できます。


## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. ラボ内で許可される**ユーザーの最大数**を指定します。 
    6. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Select virtual machine specifications]** \(仮想マシンの仕様の選択\) ページで、次の手順を実行します。
    1. ラボで作成する仮想マシン (VM) の **[サイズ]** を選択します。 
    2. VM を作成する**リージョン**を選択します。 
    3. ラボで VM の作成に使用する **VM イメージ**を選択します。 
    4. **[次へ]** を選択します。

        ![VM 仕様の指定](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **[資格情報の設定]** ページで、ラボ内のすべての VM に使う既定の資格情報を指定します。 
    1. ラボ内のすべての VM に使う**ユーザーの名前**を指定します。
    2. ユーザーの**パスワード**を指定します。 

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **作成**を選択します。 

        ![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **[Configure template**]\(テンプレートの構成\) ページで、ラボの作成プロセスの状態を確認します。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレートの構成](../media/tutorial-setup-classroom-lab/configure-template.png)
7. テンプレートの構成が完了すると、次のページが表示されます。 

    ![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. このチュートリアルでは、以下の手順は省略可能です。 
    1. **[開始]** を選択してテンプレート VM を開始します。
    2. **[接続]** を選択してテンプレート VM に接続します。 
    3. テンプレート VM にソフトウェアをインストールして構成します。 
    4. VM を**停止**します。  
    5. テンプレートの**説明**を入力します。

        ![[Configure template]\(テンプレートの構成\) ページの [次へ]](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. [Configure template]\(テンプレートの構成\) ページの **[次へ]** を選択します。 
10. **[Publish the template]** \(テンプレートの発行\) ページで、次の操作を行います。 
    1. テンプレートをすぐに発行するには、*[I understand I can't modify the template after publishing.This process can only be done once and can take up to an hour]* \(発効したらテンプレートを変更できなくなること、このプロセスは一度のみ実行でき最大 1 時間かかる可能性があることを理解しています\) チェックボックスをオンにし、**[発行]** を選択します。  

        > [!WARNING]
        > 一度発行すると、再発行することはできません。 
    2. 後で発行する場合は、**[後のために保存]** を選択します。 ウィザードが完了した後に、テンプレート VM を発行することができます。 ウィザード完了後の構成および発行方法の詳細については、「[クラスルーム ラボの管理](how-to-manage-classroom-labs.md)」記事の「[テンプレートを発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

        ![テンプレートを発行する](../media/tutorial-setup-classroom-lab/publish-template.png)
11. テンプレートの**発行に関する進行状況**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. テンプレートが正常に発行されると、次のページが表示されます。 **[完了]** を選択します。

    ![テンプレートの発行 - 正常](../media/tutorial-setup-classroom-lab/publish-success.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. **[仮想マシン]** ページに切り替えて、**[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加

1. 左側のメニューの **[ユーザー]** を選択します。 既定では、**[アクセスを制限する]** オプションが有効になっています。 この設定がオンの場合、ユーザーは、ユーザーの一覧に存在しない限り、登録リンクを持っていてもラボに登録できません。 リストに存在するユーザーだけが、受け取った登録リンクを使用してラボに登録できます。 この手順では、ユーザーをリストに追加します。 または、**[アクセスを制限する]** をオフにすることもできます。その場合ユーザーは、登録リンクさえあればラボに登録することができます。 
2. ツール バーの **[ユーザーの追加]** を選択します。 
3. **[ユーザーの追加]** ページで、ユーザーのメール アドレスを入力します。複数のメール アドレスは別々の行に入力するか、1 つの行にセミコロンで区切って入力します。 

    ![ユーザーのメール アドレスを追加](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **[保存]** を選択します。 ユーザーのメール アドレスとそのステータス (登録または未登録) がリストに表示されます。 

    ![ユーザー リスト](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-registration-link-to-students"></a>登録リンクを学生に送信する

1. このページ上で、まだ **[ユーザー]** ビューが表示されていない場合は、[ユーザー] ビューに切り替えます。 
2. **[Get registration link]\(登録リンクの取得\)** タイルを選択します。

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、**[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスの **[閉じる]** を選択します。 
4. 学生がクラスに登録できるように、登録リンクを学生と共有します。 **制限オプション**設定が有効になっていて、なおかつ一連のユーザーがリストに存在する場合は、次の操作を行います。
    1. ユーザーの**メール アドレス**をリストで選択します。 
    2. 既定のメール プログラムのウィンドウが、**宛先**アドレスが入力された状態で表示されます。 
    3. 先ほどコピーした**登録 URL** を貼り付けます。 
    4. **メール**を送信します。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、クラスルーム ラボを作成し、ラボを構成しました。 学生が登録リンクを使ってラボの VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボの VM に接続する](tutorial-connect-virtual-machine-classroom-lab.md)

