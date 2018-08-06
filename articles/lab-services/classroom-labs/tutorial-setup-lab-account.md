---
title: Azure Lab Services でラボ アカウントを設定する | Microsoft Docs
description: このチュートリアルでは、Azure Lab Services でラボ アカウントを設定します。
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
ms.date: 07/17/2018
ms.author: spelluru
ms.openlocfilehash: b60c1e84eb5b62bfce0eb2ba96129deeee2fc3c3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345310"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>チュートリアル: Azure Lab Services でラボ アカウントを設定する
Azure Lab Services では、ラボ アカウントは、それによって組織のラボが管理される中心的なアカウントとして機能します。 ラボ アカウントでは、ラボを作成する権限を他のユーザーに付与し、ラボ アカウントの管理下にあるすべてのラボに適用されるポリシーを設定します。 このチュートリアルでは、ラボ管理者としてラボ アカウントを作成する方法について説明します。 

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボ アカウントを作成する
> * ユーザーをラボの作成者ロールに追加する
> * ラボ所有者が利用できる Marketplace イメージを指定する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
次の手順では、Azure portal を使って Azure Lab Services でラボ アカウントを作成する方法を示します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメイン メニューで、**[リソースの作成]** を選択します。
3. Azure Marketplace で **Lab Services** を検索し、ボックスの一覧から **[Lab Services]** を選択します。 
4. フィルター適用後のサービスの一覧で、**[Lab Services (プレビュー)]** を選択します。 
1. **[Create a lab account]** ウィンドウで、**[作成]** を選びます。
2. **[Lab account]\(ラボ アカウント\)** ウィンドウで、次のようにします。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。 
    5. **[作成]** を選択します。 

        ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. ラボ アカウント ページが表示されない場合は、**[通知]** ボタンを選択し、通知内の **[リソースに移動]** ボタンをクリックします。 

    ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 クラスルーム ラボの作成に、同じユーザー アカウントを使用する場合は、この手順をスキップすることができます。 クラスルーム ラボの作成に、別のユーザー アカウントを使用する場合は、次の手順に従います。 

クラスのラボを作成するアクセス許可を教師に与えるには、教師を**ラボの作成者**ロールに追加します。

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ 追加]** をクリックします。 

    ![[Lab account]\(ラボ アカウント\) ページ](../media/tutorial-setup-lab-account/access-control.png)
2. **[アクセス許可の追加]** ページで、**[ロール]** から **[ラボの作成者]** を選び、ラボの作成者ロールに追加するユーザーを選んだ後、**[保存]** をクリックします。 

    ![ユーザーをラボの作成者ロールに追加する](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>ラボ所有者が利用できる Marketplace イメージを指定する
ラボ アカウント所有者は、ラボ作成者がラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定できます。 

1. 左側のメニューで、**[Marketplace イメージ]** を選択します。 既定では、イメージの完全な一覧 (有効なものと無効なものの両方) が表示されます。 上部のドロップダウン リストから **[有効のみ]**/**[無効のみ]** オプションを選択することにより、一覧をフィルター処理して、有効/無効のイメージのみを表示することができます。 
    
    ![Marketplace イメージのページ](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    一覧に表示される Marketplace イメージは、次の条件を満たすものだけです。
        
    - 単一の VM を作成する。
    - Azure Resource Manager を使用して VM をプロビジョニングする
    - 追加のライセンス プランの購入を必要としない
2. 有効になっている Marketplace イメージを**無効にする**には、次のいずれかの操作を行います。 
    1. 最後の列で **[...] (省略記号)** を選択し、**[Disable image]\(イメージの無効化\)** を選択します。 

        ![1 つのイメージを無効にする](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 一覧内のイメージ名の前のチェック ボックスをオンにして、一覧から 1 つ以上のイメージを選択し、**[Disable selected images]\(選択したイメージの無効化\)** を選択します。 

        ![複数のイメージを無効にする](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同様に、Marketplace イメージを**有効にする**には、次のいずれかの操作を行います。 
    1. 最後の列で **[...] (省略記号)** を選択し、**[Enable image]\(イメージの有効化\)** を選択します。 
    2. 一覧内のイメージ名の前のチェック ボックスをオンにして、一覧から 1 つ以上のイメージを選択し、**[Enable selected images]\(選択したイメージの有効化\)** を選択します。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、ラボ アカウントを作成しました。 専門職としてクラスルーム ラボを作成する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)

