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
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660039"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボを管理する 
この記事では、クラスルーム ラボの作成と構成、すべてのクラスルーム ラボの表示、またはラボの削除を実行する方法を説明します。

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。
2. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. クラスルーム ラボの**名前**を指定します。 
    2. クラスルームで使う仮想マシンの**サイズ**を選びます。
    3. 仮想マシンの作成に使用する **[イメージ]** を選択します。
    4. ラボ内の仮想マシンにログインするために使用する **[既定の資格情報]** を指定します。
    7. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. ラボの**ホーム ページ**が表示されます。 
    
    ![クラスルーム ラボのホーム ページ](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>利用ポリシーを構成する

1. **[Usage policy]\(利用ポリシー\)** を選びます。 
2. **[Usage policy]\(利用ポリシー\)** の設定で、ラボの使用を許可する**ユーザーの数**を入力します。
3. **[保存]** を選択します。 

    ![利用ポリシー](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>テンプレートを設定する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 テンプレートの可視性を "パブリック" に設定して、対象のラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。  

### <a name="set-template-title-and-description"></a>テンプレートのタイトルと説明を設定する
1. **[テンプレート]** セクションで、テンプレート用の **[編集]**(鉛筆のアイコン) を選択します。 
2. **[User view]\(ユーザー ビュー\)** ウィンドウで、テンプレートの**タイトル**を入力します。
3. テンプレートの**説明**を入力します。
4. **[保存]** を選択します。

    ![クラスルーム ラボの説明](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>テンプレートのインスタンスをパブリックにする 
テンプレートの可視性が **[パブリック]** に設定されると、Azure Lab Services は、テンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。  

1. **[テンプレート]** セクションで **[可視性]** を選択します。 
2. **[可用性]** ページで、**[パブリック]** を選択します。
    
    > [!IMPORTANT]
    > テンプレートがパブリックに利用できるようになった後で、そのアクセスをプライベートに変更することはできません。 
3. **[保存]** を選択します。

    ![可用性](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>登録リンクを学生に送信する

1. **[User registration]\(ユーザー登録\)** タイルを選択します。
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、**[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](../media/how-to-manage-classroom-labs/registration-link.png)

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
 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してラボの設定を開始します。

- [クラスルーム ラボを設定する](how-to-manage-classroom-labs.md)
- [ラボを設定する](../tutorial-create-custom-lab.md)
