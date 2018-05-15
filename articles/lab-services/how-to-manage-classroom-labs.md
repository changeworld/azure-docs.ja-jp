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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Azure Lab Services (旧 Azure DevTest Labs) でクラスルーム ラボを管理する
この記事では、クラスルーム ラボの作成と構成、すべてのクラスルーム ラボの表示、またはラボの削除を実行する方法を説明します。

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。
2. **[新しいラボ]** ウィンドウで、次の操作を行います。 
    1. クラスルーム ラボの **[名前]** を指定します。 
    2. 教室で使用する予定の仮想マシンの **[サイズ]** を選択します。
    3. 仮想マシンの作成に使用する **[イメージ]** を選択します。
    4. ラボ内の仮想マシンにログインするために使用する **[既定の資格情報]** を指定します。
    7. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. ラボの**ホーム ページ**が表示されます。 
    
    ![クラスルーム ラボのホーム ページ](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>利用ポリシーを構成する

1. **[利用ポリシー]** を選択します。 
2. **[利用ポリシー]** の設定で、ラボの使用を許可する **[ユーザー数]** を入力します。
3. **[保存]** を選択します。 

    ![利用ポリシー](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>テンプレートを設定する
すべてのユーザーの仮想マシンは、ラボ内のテンプレートから作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示するテンプレートの名前と説明を入力でき、可視性を [パブリック] に設定して、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにすることができます。  

## <a name="set-template-title-and-description"></a>テンプレートのタイトルと説明を設定する
1. **[テンプレート]** セクションで、テンプレート用の **[編集]**(鉛筆のアイコン) を選択します。 
2. **[ユーザー ビュー]** ウィンドウで、テンプレートの **[タイトル]** を入力します。
3. テンプレートの **[説明]** を入力します。
4. **[保存]** を選択します。

    ![クラスルーム ラボの説明](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>テンプレートのインスタンスをパブリックにする 
テンプレートの可視性が **[パブリック]** に設定されると、Azure Lab Services は、テンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。  

1. **[テンプレート]** セクションで **[可視性]** を選択します。 
2. **[可用性]** ページで、**[パブリック]** を選択します。
    
    > [!IMPORTANT]
    > テンプレートがパブリックに利用できるようになった後で、そのアクセスをプライベートに変更することはできません。 
3. **[保存]** を選択します。

    ![可用性](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>登録リンクを学生に送信する

1. **[ユーザー登録]** タイルを選択します。
2. **[ユーザー登録]** ダイアログ ボックスで、**[コピー]** ボタンをクリックします。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>すべてのクラスルーム ラボを表示する
1. [Azure Lab Services ポータル](https://labs.azure.com)に移動します。
2. 選択したラボ アカウント内にすべてのラボが表示されることを確認します。 

    ![すべてのラボ](./media/how-to-manage-classroom-labs/all-labs.png)
3. 上部にあるドロップダウン リストを使用して、別のラボ アカウントを選択できます。 選択したラボ アカウントのラボが表示されます。 

## <a name="delete-a-classroom-lab"></a>クラスルーム ラボを削除する
1. ラボ用のタイルで、隅にある 3 つのドット (...) を選択します。 

    ![ラボの選択](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. **[削除]** を選択します。 

    ![[削除] ボタン](./media/how-to-manage-classroom-labs/delete-button.png)
3. **[ラボの削除]** ダイアログ ボックスで、**[削除]** を選択します。 

    ![[削除] ダイアログ ボックス](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用したラボの設定の開始:

- [クラスルーム ラボを設定する](how-to-manage-classroom-labs.md)
- [カスタム ラボを設定する](tutorial-create-custom-lab.md)
