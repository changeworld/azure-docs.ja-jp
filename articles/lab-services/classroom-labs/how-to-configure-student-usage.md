---
title: Azure Lab Services のクラスルーム ラボの使用設定を構成する | Microsoft Docs
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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142766"
---
# <a name="configure-usage-settings-and-policies"></a>使用設定とポリシーを構成する
この記事では、ラボのユーザー数の構成、それらのユーザーのラボへの登録、ユーザーが VM を使用できる時間数の制御などの方法について説明します。 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>ラボ内で許可されるユーザーの数を指定する

1. **[Usage policy]\(利用ポリシー\)** を選びます。 
2. **[Usage policy]\(利用ポリシー\)** の設定で、ラボの使用を許可する**ユーザーの数**を入力します。
3. **[保存]** を選択します。 

    ![利用ポリシー](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>登録リンクをユーザーに送信する

1. **[ダッシュボード]** ビューに切り替えます。 
2. **[User registration]\(ユーザー登録\)** タイルを選択します。

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、**[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 学生は、このリンクを使用して、ラボに登録する際に役立つページに移動します。 

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスの **[閉じる]** を選択します。 

## <a name="view-users-registered-with-the-lab"></a>ラボに登録されているユーザーを確認する

左側のメニューで **[ユーザー]** を選択して、ラボに登録されているユーザーの一覧を表示します。 

![ラボに登録されているユーザーの一覧](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>ユーザーごとのクォータを設定する

1. 左側のメニューで **[ユーザー設定]** を選択します。
2. **[Quta per user]\(ユーザーあたりのクォータ)** タイルを選択します。 
3. **[Limit the number of hours a user can use a VM]\(ユーザーが VM を使用できる時間数を制限する)** を選択します。 
4. **[How many hours do you want to give to each user?]\(各ユーザーに許可する時間数)** に時間数を入力し、**[保存]** を選択します。 

    ![ユーザーあたりの時間数](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. **[Quota per user]\(ユーザーあたりのクォータ)** タイルに時間数が表示されます。 

    ![ユーザーあたりのクォータ](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>ユーザー VM を管理する
提供された登録リンクを使用して学生が Azure Lab Services に登録すると、**[仮想マシン]** タブに学生に割り当てられた VM が表示されます。 

![学生に割り当てられた仮想マシン](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

学生の VM 上では、次のタスクを実行できます。 

- VM が実行中の場合は、VM を停止します。 
- VM が停止している場合は、VM を起動します。 
- VM に接続します。 
- VM を削除します。 
- ユーザーが仮想マシンを使用した時間数を表示します。 


## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してラボの設定を開始します。

- [クラスルーム ラボを設定する](how-to-manage-classroom-labs.md)
- [ラボを設定する](../tutorial-create-custom-lab.md)
