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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>チュートリアル: クラスルーム ラボを設定する 
このチュートリアルでは、クラスルームで学生が使用する仮想マシンのセットで、クラスルーム ラボを設定します。  

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * クラスルーム ラボを作成する
> * クラスルーム ラボを構成する
> * 登録リンクを学生に送信する

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。
2. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. クラスルーム ラボの**名前**を指定します。 
    2. クラスルームで使う仮想マシンの**サイズ**を選びます。
    3. 仮想マシンの作成に使う**イメージ**を選びます。
    4. ラボの仮想マシンへのログインに使う**既定の資格情報**を指定します。 
    7. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. ラボの**ホーム ページ**が表示されます。 
    
    ![クラスルーム ラボのホーム ページ](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>利用ポリシーを構成する

1. **[Usage policy]\(利用ポリシー\)** を選びます。 
2. **[Usage policy]\(利用ポリシー\)** の設定で、ラボの使用を許可する**ユーザーの数**を入力します。
3. **[保存]** を選択します。 

    ![利用ポリシー](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>テンプレートを設定する 
すべてのユーザーの仮想マシンは、ラボ内のテンプレートから作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示するテンプレートの名前と説明を入力でき、可視性を [パブリック] に設定して、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにすることができます。 

### <a name="set-title-and-description"></a>タイトルと説明を設定する
1. **[テンプレート]** セクションで、テンプレートの **[編集]** (鉛筆のアイコン) を選択します。 
2. **[User view]\(ユーザー ビュー\)** ウィンドウで、テンプレートの**タイトル**を入力します。
3. テンプレートの**説明**を入力します。
4. **[保存]** を選択します。

    ![クラスルーム ラボの説明](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>テンプレートのインスタンスをパブリックにする
テンプレートの可視性が **[パブリック]** に設定されると、Azure Lab Services は、テンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。 

1. **[テンプレート]** セクションで **[可視性]** を選択します。 
2. **[可用性]** ページで、**[パブリック]** を選択します。
    
    > [!IMPORTANT]
    > テンプレートがパブリックに利用できるようになった後で、そのアクセスをプライベートに変更することはできません。 
3. **[保存]** を選択します。

    ![可用性](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>登録リンクを学生に送信する

1. **[User registration]\(ユーザー登録\)** タイルを選択します。
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、**[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、クラスルーム ラボを作成し、ラボを構成しました。 学生が登録リンクを使ってラボの VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボの VM に接続する](tutorial-connect-virtual-machine-classroom-lab.md)

