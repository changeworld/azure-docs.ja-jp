---
title: Azure Lab Services を使用してクラスルーム ラボを設定する | Microsoft Docs
description: このチュートリアルでは、Azure Lab Services を使用し、クラスの学生が使用する仮想マシンでクラスルーム ラボを設定します。
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719179"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>チュートリアル:クラスルーム ラボを設定する 
このチュートリアルでは、クラスルームで学生が使用する仮想マシンで、クラスルーム ラボを設定します。  

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * クラスルーム ラボを作成する
> * ラボへのユーザーの追加
> * ラボのスケジュールを設定する
> * 招待メールを学生に送信する

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおいて、所有者、ラボの作成者、または共同作成者ロールのメンバーである必要があります。 ラボ アカウントを作成するために使用したアカウントは、所有者ロールに自動的に追加されます。

ラボの所有者は、他のユーザーを**ラボの作成者**ロールに追加できます。 たとえば、ラボの所有者は、ラボの作成者ロールに教授たちを追加します。 その後、教授は、クラス用に VM を使用してラボを作成します。 学生は、教授から受け取った登録リンクを使用してラボに登録します。 登録された学生は、ラボの VM を使用してクラスの課題や宿題をすることができます。 ラボの作成者ロールにユーザーを追加するための詳細な手順については、「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」を参照してください。


## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New lab]\(新しいラボ\)** を選択します。 
    
    ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定し、 **[次へ]** を選択します。  

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Linux イメージを選択した場合は、リモート デスクトップ接続を有効にするためのオプションが表示されます。 詳細については、[Linux のリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。
    2. **[Virtual machine credentials]\(仮想マシンの資格情報\)** ページで、ラボ内のすべての VM 用の既定の資格情報を指定します。 ユーザーの**名前**と**パスワード**を指定し、 **[次へ]** を選択します。  

        ![新しいラボのウィンドウ](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **[Lab policies]\(ラボのポリシー\)** ページで、ラボに対してスケジュールされた時間以外に各ユーザーに割り当てられる時間数を入力し ( **[Quota for each user]\(各ユーザーのクォータ\)** )、 **[完了]** を選択します。 

        ![[Quota for each user]\(各ユーザーのクォータ\)](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. テンプレート VM の作成の状態を示す次の画面が表示されます。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレート VM の作成の状態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **[テンプレート]** ページで、次の手順を実行します。これらの手順は、チュートリアルでは**省略可能**です。

    2. **[接続]** を選択してテンプレート VM に接続します。 Linux のテンプレート VM の場合は、SSH と RDP のどちらを使用して接続するかを選択します (RDP が有効な場合)。
    1. **[パスワードのリセット]** を選択して、VM のパスワードをリセットします。 
    1. テンプレート VM にソフトウェアをインストールして構成します。 
    1. VM を**停止**します。  
    1. テンプレートの**説明**を入力します。
10. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 

    ![テンプレートの発行ボタン](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 一度発行すると、再発行することはできません。 
8. **[Publish template]\(テンプレートの発行\)** ページで、ラボに作成する仮想マシンの数を入力し、 **[発行]** を選択します。 

    ![テンプレートの発行 - VM の数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. ページには、テンプレートの**発行の状態**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 左側のメニューで [仮想マシン] を選択するか、[仮想マシン] タイルを選択して、 **[仮想マシン プール]** ページに切り替えます。 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    次のタスクはこのページで実行します (このチュートリアルでは、これらの手順を実行しないでください。 これらの手順は情報提供のみを目的としています)。 
    
    1. ラボの容量 (ラボ内の VM の数) を変更するには、ツール バーの **[Lab capacity]\(ラボの容量\)** を選択します。
    2. すべての VM を一度に起動するには、ツール バーの **[Start all]\(すべて起動\)** を選択します。 
    3. 特定の VM を起動するには、 **[状態]** の下向き矢印を選択し、 **[開始]** を選択します。 また、最初の列で VM を選択した後にツール バーの **[Start]\(起動\)** を選択して、1 台の VM を起動することもできます。

    テンプレートの作成と管理、および学生の仮想マシンの設定と管理について詳しくは、次の記事を参照してください。 
    
    - [クラスルーム ラボ テンプレートを作成し、管理する](how-to-create-manage-template.md)
    - [仮想マシン プールを設定および管理する](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加

1. 左側のメニューの **[ユーザー]** を選択します。 既定では、 **[アクセスを制限する]** オプションが有効になっています。 この設定がオンの場合、ユーザーは、ユーザーの一覧に存在しない限り、登録リンクを持っていてもラボに登録できません。 リストに存在するユーザーだけが、受け取った登録リンクを使用してラボに登録できます。 この手順では、ユーザーをリストに追加します。 または、 **[アクセスを制限する]** をオフにすることもできます。その場合ユーザーは、登録リンクさえあればラボに登録することができます。 
2. ツール バーの **[ユーザーの追加]** を選択し、 **[Add by email addresses]\(メール アドレスで追加\)** を選択します。 

    ![[ユーザーの追加] ボタン](../media/how-to-configure-student-usage/add-users-button.png)
1. **[ユーザーの追加]** ページで、ユーザーのメール アドレスを入力します。複数のメール アドレスは別々の行に入力するか、1 つの行にセミコロンで区切って入力します。 

    ![ユーザーのメール アドレスを追加](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **[保存]** を選択します。 ユーザーのメール アドレスとそのステータス (登録または未登録) がリストに表示されます。 

    ![ユーザー リスト](../media/how-to-configure-student-usage/users-list-new.png)

    ラボに登録されたユーザーの名前が一覧に表示されます。 
    
## <a name="set-a-schedule-for-the-lab"></a>ラボのスケジュールを設定する
ラボ内の VM が特定の時刻に自動的に起動または停止されるように、ラボ用にスケジュール化されたイベントを作成します。 前に指定したユーザー クォータは、このスケジュールされた時間以外に各ユーザーに割り当てられる追加時間です。 

1. **[スケジュール]** ページに切り替えて、ツール バーの **[Add scheduled event]\(スケジュール化されたイベントの追加\)** を選択します。 

    ![[スケジュール] ページの [スケジュールの追加] ボタン](../media/how-to-create-schedules/add-schedule-button.png)
2. **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページで、次の手順を実行します。
    1. **[Event type]\(イベントの種類\)** で **[Standard]\(標準\)** が選択されていることを確認します。  
    2. クラスの**開始日**を指定します。 
    4. VM を起動する**開始時刻**を指定します。
    5. VM をシャットダウンする**停止時刻**を指定します。 
    6. 指定した開始および停止時刻の**タイム ゾーン**を指定します。 
3. 同じ **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページの **[Repeat]\(繰り返し\)** セクションで、現在のスケジュールを選択します。  

    ![[スケジュール] ページの [スケジュールの追加] ボタン](../media/how-to-create-schedules/select-current-schedule.png)
5. **[Repeat]\(繰り返し\)** ダイアログ ボックスで、次の手順を実行します。
    1. **[Repeat]\(繰り返し\)** フィールドに **[毎週]** が設定されていることを確認します。 
    2. スケジュールを有効にする曜日を選択します。 次の例では、月曜日から金曜日までが選択されています。 
    3. スケジュールの**終了日**を選択します。
    8. **[保存]** を選択します。 

        ![繰り返しのスケジュールを設定する](../media/how-to-create-schedules/set-repeat-schedule.png)

3. 次に、 **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページの **[メモ (省略可能)]** に、スケジュールの説明またはメモを入力します。 
4. **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページで、 **[保存]** を選択します。 

    ![週単位のスケジュール](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. カレンダーで開始日に移動して、スケジュールが設定されていることを確認します。
    
    ![カレンダーでスケジュールを設定する](../media/how-to-create-schedules/schedule-calendar.png)

    クラスのスケジュールの作成と管理について詳しくは、[クラスルーム ラボのスケジュールを作成して管理する方法](how-to-create-schedules.md)に関するページを参照してください。

## <a name="send-invitation-emails-to-students"></a>招待メールを学生に送信する

1. まだこのページが表示されていない場合は **[ユーザー]** ビューに切り替え、ツール バーの **[Invite all]\(全員を招待\)** を選択します。 

    ![学生の選択](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. **[Send invitation by email]\(メールで招待を送信する\)** ページで、オプションのメッセージを入力し、 **[送信]** を選択します。 電子メールには、登録リンクが自動的に含まれます。 この登録リンクを取得するには、ツール バーの **... (省略記号)** 、 **[Registration link]\(登録リンク\)** の順に選択します。 

    ![登録リンクを電子メールで送信する](../media/tutorial-setup-classroom-lab/send-email.png)
4. **[ユーザー]** の一覧に**招待**の状態が表示されます。 この状態は、 **[送信中]** に変更された後、 **[Sent on &lt;date&gt;]\(<日付> に送信済み\)** に変更されます。 

    クラスへの学生の追加と学生によるラボの使用の管理について詳しくは、[学生の使用を構成する方法](how-to-configure-student-usage.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、クラスルーム ラボを作成し、ラボを構成しました。 学生が登録リンクを使ってラボの VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボの VM に接続する](tutorial-connect-virtual-machine-classroom-lab.md)

