---
title: Azure DevTest Labs を使用してカスタム ラボを作成する | Microsoft Docs
description: このクイック スタートでは、Azure DevTest Labs を使ってカスタム ラボを作成します。
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
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: df70322a2d6562fce8da17fde652dd0dfbfcaec4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-custom-lab-by-using-azure-devtest-labs"></a>チュートリアル: Azure DevTest Labs を使用してカスタム ラボを設定する
このチュートリアルでは、Azure portal を使ってカスタム ラボを作成します。 ラボの管理者は、組織のラボを設定し、ラボに VM を作成して、ポリシーを構成します。 ラボのユーザー (開発者やテスト担当者など) は、ラボの VM を要求し、VM に接続して使用します。 

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * カスタム ラボを作成する
> * 仮想マシン (VM) をラボに追加する
> * ユーザーをラボのユーザー ロールに追加する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-custom-lab"></a>カスタム ラボを作成する
次の手順は、Azure Portal を使用して Azure DevTest Labs でラボを作成する方法を示しています。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメイン メニューから、**[リソースの作成]** (一覧の先頭にあります) を選択し、**[Developer tools]** をポイントし、**[DevTest Labs]** をクリックします。 

    ![新しい DevTest Lab メニュー](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. **[DevTest Lab の作成]** ウィンドウで、次のようにします。 
    1. **[ラボ名]** に、ラボの名前を入力します。 
    2. **[サブスクリプション]** で、ラボを作成するサブスクリプションを選びます。 
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。 
    4. **[場所]** で、ラボを作成する場所/リージョンを選びます。 
    5. **[作成]** を選択します。 
    6. **[ダッシュボードにピン留めする]** をオンにします。 ラボの作成が済むと、ダッシュボードにラボが表示されます。 

        ![DevTest Labs のラボの作成セクション](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>VM をラボに追加する

1. **[DevTest ラボ]** ページで、ツール バーの **[+ 追加]** を選びます。 

    ![[追加] ボタン](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. **[ベースの選択]** ページで、**Ubuntu** キーワードを検索し、ベース イメージの一覧から 1 つを選びます。 
1. **[仮想マシン]** ページで、次のようにします。 
    1. **[仮想マシン名]** に、仮想マシンの名前を入力します。 
    2. **[ユーザー名]** に、仮想マシンにアクセスできるユーザーの名前を入力します。 
    3. **[値を入力します]** に、ユーザーのパスワードを入力します。 
    4. **[詳細設定]** を選択します。
    5. **[このマシンを要求可能にする]** で、**[はい]** を選びます。
    6. **インスタンス数**が **1** に設定されていることを確認します。 **2** に設定すると、`<base image name>00' and <base image name>01` という名前で 2 つの VM が作成されます。 たとえば、`win10vm00` と `win10vm01` などです。 
    7. **[詳細]** ページを閉じるには、**[OK]** をクリックします。 
    8. **[作成]** を選択します。 

        ![ベースの選択](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. **[要求可能な仮想マシン]** の一覧に、VM の状態が表示されます。 仮想マシンの作成には、25 分前後かかる場合があります。 VM は別の Azure リソース グループに作成されます。リソース グループの名前は、現在ラボが含まれているリソース グループの名前で始まります。 たとえば、ラボが `labrg` にある場合、VM が作成されるリソース グループは `labrg3988722144002` などとなります。 

        ![VM の作成の状態](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. 作成が済んだ VM は、**[要求可能な仮想マシン]** の一覧に表示されます。 

## <a name="add-a-user-to-the-lab-user-role"></a>ユーザーをラボのユーザー ロールに追加する

1. 左側のメニューで、**[構成とポリシー]** を選びます。 

    ![構成とポリシー](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. メニューから **[アクセス制御 (IAM)]** を選び、ツール バーの **[+ 追加]** を選びます。 

    ![アクセス制御 - ユーザーの追加ボタン](./media/tutorial-create-custom-lab/access-control-add.png)
1. **[アクセス許可の追加]** ページで、次のようにします。
    1. **[ロール]** で、**[DevTest Labs ユーザー]** を選びます。 
    2. 追加する**ユーザー**を選びます。 
    3. **[保存]** を選択します。

        ![Add permissions](./media/tutorial-create-custom-lab/add-lab-user.png)
4. **[構成とポリシー] - [アクセス制御 (IAM)]** を閉じるには、右上隅の **[X]** を選びます。 

## <a name="cleanup-resources"></a>リソースをクリーンアップする
次のチュートリアルでは、ラボ ユーザーがラボ内の VM を要求して接続する方法を説明します。 そのチュートリアルを行わず、このチュートリアルで作成したリソースをクリーンアップする場合は、次の手順のようにします。 

1. Azure portal で、メニューの **[リソース グループ]** を選びます。 
2. ラボを作成したリソース グループを選びます。 
3. ツール バーから **[リソース グループの削除]** を選びます。 リソース グループを削除すると、ラボを含め、グループ内のすべてのリソースが削除されます。 
4. 同じ手順を繰り返し、自動的に作成された `<your resource group name><random numbers>` という名前の追加リソース グループを削除します。 たとえば、「 `splab3988722144001`」のように入力します。 VM は、ラボが存在するリソース グループではなく、このリソース グループに作成されます。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、VM を含むカスタム ラボを作成し、ユーザーにラボへのアクセス許可を与えました。 ラボ ユーザーとしてラボにアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: カスタム ラボにアクセスする](tutorial-use-custom-lab.md)

