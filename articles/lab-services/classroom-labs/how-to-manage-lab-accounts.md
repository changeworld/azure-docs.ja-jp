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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: 20412efac553458f3028f873bcc6d918a673f261
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838819"
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
    5. **作成**を選択します。 

        ![ラボ アカウントの作成ウィンドウ](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. ラボ アカウント ページが表示されない場合は、**[通知]** ボタンを選択し、通知内の **[リソースに移動]** ボタンをクリックします。 

    ![ラボ アカウントの作成ウィンドウ](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 クラスルーム ラボの作成に、同じユーザー アカウントを使用する場合は、この手順をスキップすることができます。 クラスルーム ラボの作成に、別のユーザー アカウントを使用する場合は、次の手順に従います。 

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ ロール割り当ての追加]** をクリックします。 
2. **[アクセス許可の追加]** ページで、**[ロール]** から **[ラボの作成者]** を選び、ラボの作成者ロールに追加するユーザーを選んだ後、**[保存]** をクリックします。

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

## <a name="view-and-manage-labs-in-the-lab-account"></a>ラボ アカウント内のラボの表示と管理

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボ]** を選択します。

    ![アカウント内のラボ](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. アカウント内の**ラボの一覧**に、次の情報が表示されます。 
    1. ラボの名前。
    2. ラボが作成された日付。 
    3. ラボを作成したユーザーのメール アドレス。 
    4. ラボ内で許可されるユーザーの最大数。 
    5. ラボの状態。 

## <a name="delete-a-lab-in-the-lab-account"></a>ラボ アカウント内のラボを削除する
前のセクションの手順に従って、ラボ アカウント内のラボの一覧を表示します。

1. **... (省略記号)** を選択し、**[削除]** を選択します。 

    ![ラボを削除する - ボタン](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 警告メッセージで **[はい]** を選択します。 



## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)