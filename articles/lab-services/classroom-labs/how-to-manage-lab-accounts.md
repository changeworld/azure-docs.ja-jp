---
title: Azure Lab Services でラボ アカウントを管理する | Microsoft Docs
description: Azure サブスクリプションに対するラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法を説明します。
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
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715328"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Azure Lab Services でラボ アカウントを管理する 
Azure Lab Services では、ラボ アカウントは、クラスルーム ラボなどのマネージド ラボのコンテナーです。 管理者は、Azure Lab Services を使用してラボ アカウントを設定し、アカウントにラボを作成できるラボ所有者にアクセスを提供します。 この記事では、ラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法について説明します。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメイン メニューで、**[リソースの作成]** を選択します。
3. Azure Marketplace で **Lab Services** を検索し、ボックスの一覧から **[Lab Services]** を選択します。 
4. フィルター適用後のサービスの一覧で、**[Lab Services (プレビュー)]** を選択します。 
5. **[Create a lab account]** ウィンドウで、**[作成]** を選びます。
7. **[Lab account]\(ラボ アカウント\)** ウィンドウで、次のようにします。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。 
    5. **[作成]** を選択します。 

        ![ラボ アカウントの作成ウィンドウ](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. ラボ アカウント ページが表示されない場合は、**[通知]** ボタンを選択し、通知内の **[リソースに移動]** ボタンをクリックします。 

    ![ラボ アカウントの作成ウィンドウ](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
クラスのラボを作成するアクセス許可を教師に与えるには、教師をラボの作成者ロールに追加します。

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ 追加]** をクリックします。 

    ![[Lab account]\(ラボ アカウント\) ページ](../media/tutorial-setup-lab-account/access-control.png)
2. **[アクセス許可の追加]** ページで、**[ロール]** から **[ラボの作成者]** を選び、ラボの作成者ロールに追加するユーザーを選んだ後、**[保存]** をクリックします。 

    ![ユーザーをラボ作成者ロールに追加する](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>ラボ アカウントを表示する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. メニューから **[すべてのリソース]** を選択します。 
3. **[種類]** で **[Lab Services]** を選択します。 
    サブスクリプション、リソース グループ、場所、およびタグでフィルター処理することもできます。 

## <a name="delete-a-lab-account"></a>ラボ アカウントを削除する
前のセクションの指示に従って、一覧にラボ アカウントを表示します。 次の手順を使用して、ラボ アカウントを削除します。 

1. 削除する**ラボ アカウント**を選択します。 
2. ツールバーから **[削除]** を選択します。 
3. **[はい]** を選択して確認します。
4. **[削除]** を選択します。 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してラボの設定を開始します。

- [クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)
- [ラボを設定する](../tutorial-create-custom-lab.md)
