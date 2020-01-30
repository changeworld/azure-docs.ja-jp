---
title: Azure Lab Services のクラスルーム ラボの使用設定を構成する
description: ラボのユーザー数の構成、それらのユーザーのラボへの登録、ユーザーが VM を使用できる時間数の制御などの方法について説明します。
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 80e8bc47f6e6293d70bbc9fae888abdf5527fe93
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169223"
---
# <a name="add-and-manage-lab-users"></a>ラボ ユーザーを追加および管理する
この記事では、ラボへのユーザーの追加、それらのユーザーのラボへの登録、ユーザーが VM を使用できる時間数の制御などの方法について説明します。 


## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加

1. 左側のメニューの **[ユーザー]** を選択します。 既定では、 **[アクセスを制限する]** オプションが有効になっています。 この設定がオンの場合、ユーザーは、ユーザーの一覧に存在しない限り、登録リンクを持っていてもラボに登録できません。 リストに存在するユーザーだけが、受け取った登録リンクを使用してラボに登録できます。 この手順では、ユーザーをリストに追加します。 または、 **[アクセスを制限する]** をオフにすることもできます。その場合ユーザーは、登録リンクさえあればラボに登録することができます。 
2. ツール バーの **[ユーザーの追加]** を選択し、 **[Add by email addresses]\(メール アドレスで追加\)** を選択します。 

    ![[ユーザーの追加] ボタン](../media/how-to-configure-student-usage/add-users-button.png)
1. **[ユーザーの追加]** ページで、ユーザーのメール アドレスを入力します。複数のメール アドレスは別々の行に入力するか、1 つの行にセミコロンで区切って入力します。 

    ![ユーザーのメール アドレスを追加](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **[保存]** を選択します。 ユーザーのメール アドレスとそのステータス (登録または未登録) がリストに表示されます。 

    ![ユーザー リスト](../media/how-to-configure-student-usage/users-list-new.png)

    > [!NOTE]
    > ラボに登録されたユーザーの名前が一覧に表示されます。 一覧に表示される名前は、Azure Active Directory 内のユーザーの姓と名を使用して作成されます。 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV ファイルをアップロードしてユーザーを追加する
また、ユーザーのメール アドレスを指定した CSV ファイルをアップロードしてユーザーを追加することもできます。

1. CSV ファイルを作成し、1 つの列にユーザーの電子メール アドレスを列記します。

    ![ユーザーを含む CSV ファイル](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. ラボの **[ユーザー]** ページで、ツール バーの **[ユーザーの追加]** を選択してから、 **[CSV のアップロード]** を選択します。

    ![[CSV のアップロード] ボタン](../media/how-to-configure-student-usage/upload-csv-button.png)
3. ユーザーのメール アドレスを指定した CSV ファイルを選択します。 CSV ファイルを選択した後に **[開く]** を選択すると、次のような **[ユーザーの追加]** ウィンドウが表示されます。 電子メール アドレスの一覧には、CSV ファイルから取得された電子メール アドレスが表示されます。 

    ![CSV ファイルからの電子メール アドレスが表示された [ユーザーの追加] ウィンドウ](../media/how-to-configure-student-usage/add-users-window.png)
4. **[ユーザーの追加]** ウィンドウで **[保存]** を選択します。 
5. ユーザーの一覧にユーザーが表示されていることを確認します。 

    ![追加されたユーザーの一覧](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>招待をユーザーに送信する
登録リンクを学生に送信するには、以下のいずれかの方法を使用します。 最初の方法では、登録リンクとオプションのメッセージが含まれた電子メールを学生にどのように送信するかを示します。 2 番目の方法では、他のユーザーと任意の方法で共有できる登録リンクをどのように取得するかを示します。 

ラボの **[アクセスを制限する]** が有効になっている場合、登録リンクを使用してラボに登録できるのは、ユーザーの一覧に含まれるユーザーのみです。 このオプションは、既定で有効です。 

### <a name="invite-all-users"></a>すべてのユーザーを招待する
1. まだこのページが表示されていない場合は **[ユーザー]** ビューに切り替え、ツール バーの **[Invite all]\(全員を招待\)** を選択します。 

    ![学生の選択](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. **[Send invitation by email]\(メールで招待を送信する\)** ページで、オプションのメッセージを入力し、 **[送信]** を選択します。 電子メールには、登録リンクが自動的に含まれます。 この登録リンクを取得するには、ツール バーの **... (省略記号)** 、 **[Registration link]\(登録リンク\)** の順に選択します。 

    ![登録リンクを電子メールで送信する](../media/tutorial-setup-classroom-lab/send-email.png)
4. **[ユーザー]** の一覧に**招待**の状態が表示されます。 この状態は、 **[送信中]** に変更された後、 **[Sent on \<date>]\(<日付> に送信済み\)** に変更されます。 

    クラスへの学生の追加と学生によるラボの使用の管理について詳しくは、[学生の使用を構成する方法](how-to-configure-student-usage.md)に関するページを参照してください。

### <a name="invite-selected-users"></a>選択したユーザーを招待する

1. 一覧で 1 人または複数のユーザーを選択します。 
2. 次に、選択した行に表示される**封筒**のアイコンを選択するか、ツール バーの **[招待]** を選択します。 

    ![選択したユーザーを招待する](../media/how-to-configure-student-usage/invite-selected-users.png)
3. **[Send invitation by email]\(メールで招待を送信する\)** ウィンドウで、オプションの**メッセージ**を入力し、 **[送信]** を選択します。 

    ![選択したユーザーに電子メールを送信する](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    この操作の状態は、 **[ユーザー]** 一覧の **[招待]** 列に表示されます。 招待メールには、ユーザーがラボに登録するために使用できる登録リンクが含まれています。

1. ページがまだ表示されていない場合は、 **[ユーザー]** ビューに切り替えます。 

## <a name="get-registration-link"></a>登録リンクを取得する
ポータルから登録リンクを取得し、独自の電子メール クライアント アプリケーションを使用して送信することもできます。 

1. 左側のメニューの **[Users]\(ユーザー\)** を選択して **[Users]\(ユーザー\)** ビューに切り替えます。 
2. ツール バーの **[...] (省略記号)** を選択してから、 **[登録リンク]** を選択します。

    ![学生登録リンク](../media/how-to-configure-student-usage/registration-link-button.png)
1. **[ユーザー登録]** ダイアログ ボックスで、 **[コピー]** ボタンを選択します。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](../media/how-to-configure-student-usage/registration-link.png)
2. **[ユーザー登録]** ダイアログ ボックスの **[完了]** を選択します。 
4. 学生がクラスに登録できるように、**登録リンク**を学生に送信します。 

## <a name="view-users-registered-with-the-lab"></a>ラボに登録されているユーザーを確認する

左側のメニューで **[ユーザー]** を選択して、ラボに登録されているユーザーの一覧を表示します。 

![ラボに登録されているユーザーの一覧](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>ユーザーのクォータを設定する
次の手順を使用して、ユーザーごとのクォータを設定できます。 

1. ページがまだアクティブになっていない場合は、左側のメニューで **[ユーザー]** を選択します。 
2. ツール バーの **[ユーザーあたりのクォータ: \<数値> 時間]** を選択します。 
3. **[ユーザーあたりのクォータ]** ページで、予定されている授業時間外で各ユーザー (学生) に与える時間数を指定し、 **[保存]** を選択します。

    ![ユーザーあたりのクォータ](../media/how-to-configure-student-usage/quota-per-user.png)    
5. ツールバーに変更後の値が表示されます。 **[ユーザーあたりのクォータ: \<時間数>** ]。 

    ![ユーザーあたりのクォータ - 後](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [スケジュールされている VM の実行時間](how-to-create-schedules.md)は、ユーザーに割り当てられるクォータにカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。 

## <a name="set-additional-quota-for-a-specific-user"></a>特定のユーザーに対して追加のクォータを設定する
ユーザーに追加のクォータを指定できます。 このクォータは、前のセクションですべてのユーザーに対して設定された共通のクォータに加えられます。 たとえば、あなたがインストラクターとして、すべてのユーザーのクォータを 10 時間に設定し、特定のユーザーに対して 5 時間の追加のクォータを設定した場合、そのユーザーは 15 (10 + 5) 時間のクォータを取得します。 後に共通クォータをたとえば 15 時間に変更すると、ユーザーは 20 (15 + 5) 時間のクォータを取得します。 このクォータ全体が、スケジュールされた時間外にあることに注意してください。 スケジュール時間内に学生がラボ VM に費やした時間は、このクォータにはカウントされません。 

これを行うには、次のステップに従います。

1. **[ユーザー]** ページで、ユーザーの一覧からユーザー (学生) を選択します。
2. 次に、ツール バーの **[Adjust quota]\(クォータの調整\)** を選択します。 

    ![[Adjust quota]\(クォータの調整\) ボタン](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 選択した 1 人または複数のユーザーに対する **[additional hours]\(追加の時間\)** の数値を入力し、 **[適用]** を選択します。 

    ![ユーザーの追加クォータ](../media/how-to-configure-student-usage/additional-quota.png)
4. ユーザーについて更新された使用状況は、 **[使用状況]** 列に表示されます。 

    ![ユーザーに関する新しい使用状況](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生アカウント
学生をクラスルーム ラボに追加するには、電子メール アカウントを使用します。 次の種類のメール アカウントを使用できます。

- 大学の Office 365 Azure Active Directory (AAD) によって提供される学生用メール アカウント。 
- Microsoft のメール アカウント (`@outlook.com`、`@hotmail.com`、`@msn.com`、`@live.com` など)。
- Microsoft 以外のメール アカウント (Yahoo または Google によって提供されるものなど)。 ただし、これらの種類のアカウントは、Microsoft アカウントとリンクされている必要があります。
- GitHub アカウント。 このアカウントは、Microsoft アカウントとリンクされている必要があります。

### <a name="using-a-non-microsoft-email-account"></a>Microsoft 以外のメール アカウントの使用
学生は、Microsoft 以外のメール アカウントを使用して、クラスルーム ラボに登録してサインインすることができます。  ただし、登録を行うには、学生が Microsoft 以外のメール アドレスにリンクされている Microsoft アカウントを最初に作成する必要があります。

多くの学生は、Microsoft 以外のメール アドレスにリンクされた Microsoft アカウントを既に持っている可能性があります。 たとえば、学生は、Microsoft の他の製品やサービス (Office、Skype、OneDrive、Windows など) でメール アドレスを使用したことがある場合は、既に Microsoft アカウントを持っています。  

学生が登録 URL をクリックしてクラスルームにサインインすると、メール アドレスとパスワードの入力を求められます。 学生が、Microsoft アカウントがリンクされていない Microsoft 以外のアカウントでサインインしようとすると、学生は次のエラー メッセージを受け取ります。 

![エラー メッセージ](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft アカウントにサインアップするには、学生は [http://signup.live.com](http://signup.live.com) にアクセスする必要があります。  

> [!IMPORTANT]
> 学生がクラスルーム ラボにサインインすると、Microsoft アカウントを作成するオプションは付与されません。 そのため、Microsoft 以外のアカウントを使用している学生に送信する、クラスルーム ラボ登録メールにこのサインアップ リンクを含めることをお勧めします。

### <a name="using-a-github-account"></a>GitHub アカウントの使用
学生は、既存の GitHub アカウントを使用して、クラスルーム ラボに登録してサインインすることもできます。 学生が GitHub アカウントにリンクされている Microsoft アカウントを既に持っている場合は、前のセクションで示したように、サインインしてパスワードを入力できます。 まだ GitHub アカウントを Microsoft アカウントにリンクしていない場合は、 **[サインイン オプション]** を選択する必要があります。

![サインイン オプションのリンク](../media/how-to-configure-student-usage/signin-options.png)

**[サインイン オプション]** ページで、 **[GitHub でサインイン]** を選択します。

![GitHub リンクを使用してサインインする](../media/how-to-configure-student-usage/signin-github.png)

最後に、GitHub アカウントにリンクされている Microsoft アカウントを作成するように求められます。 これは、学生が **[次へ]** を選択したときに自動的に行われます。  学生はすぐにサインインし、クラスルーム ラボに接続されます。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
