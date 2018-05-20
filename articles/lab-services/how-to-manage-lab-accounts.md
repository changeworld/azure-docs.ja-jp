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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 09303a4d4fc730aae6fc7c04148d64b0f57e69ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Azure Lab Services (旧 Azure DevTest Labs) でラボ アカウントを管理する
この記事では、ラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法について説明します。

## <a name="prerequisites"></a>前提条件
現在、Azure Lab Services は、限定的なプレビュー段階にあります。 ラボ アカウントを作成するには、[プレビューにサインアップ](https://aka.ms/azlabspreviewsignup)します。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメイン メニューから、**[リソースの作成]** (一覧の先頭にあります) を選択し、**[開発者ツール]** をポイントし、**[Lab Services (プレビュー)]** をクリックします。
1. **[ラボ アカウントの作成]** ウィンドウで、**[作成]** を選択します。
2. **[ラボ アカウント]** ウィンドウで、次の操作を行います。 
    1. **[ラボ アカウント名]** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。 
    5. **[作成]** を選択します。 

        ![[ラボ アカウントの作成] ウィンドウ](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. ラボ アカウント ページが表示されない場合は、**[通知]** ボタンを選択し、通知内の **[リソースに移動]** ボタンをクリックします。 

    ![[ラボ アカウントの作成] ウィンドウ](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 次の **[ラボ アカウント]** ページが表示されます。

    ![[ラボ アカウント] ページ](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボ作成者ロールに追加する
クラスのラボを作成するアクセス許可を教育者に与えるには、教育者をラボ作成者ロールに追加します。

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ 追加]** をクリックします。 

    ![[ラボ アカウント] ページ](./media/tutorial-setup-lab-account/access-control.png)
2. **[アクセス許可の追加]** ページで、**[ロール]** で **[ラボ作成者]** を選択し、ラボ作成者ロールに追加するユーザーを選択した後、**[保存]** をクリックします。 

    ![ユーザーをラボ作成者ロールに追加する](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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
- [カスタム ラボを設定する](tutorial-create-custom-lab.md)
