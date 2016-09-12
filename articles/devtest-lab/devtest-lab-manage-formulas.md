<properties
	pageTitle="VM を作成するための DevTest ラボの数式の管理 | Microsoft Azure"
	description="DevTest Labs 数式を作成、更新、削除し、新しい VM の作成に使用する方法について説明します。"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# VM を作成するための DevTest ラボの数式の管理

## 概要

数式は、[カスタム イメージ](./devtest-lab-create-template.md)や [Marketplace イメージ](./devtest-lab-configure-marketplace-images.md)と同様に、VM を短時間でプロビジョニングするための手法です。DevTestラボにおける数式とは、ラボ VM の作成に使用できる既定のプロパティ値の一覧です。数式から VM を作成する際に、この既定値をそのまま使用することも変更することもできます。

この記事では、次のタスクを実行する方法について説明します。

- [新しい数式の作成](#create-a-new-formula)
- [数式を使用した VM の新規作成](#use-a-formula-to-create-a-new-vm)
- [数式の変更](#modify-a-formula)
- [数式の削除](#delete-a-formula)

> [AZURE.NOTE] 数式は、VM のプロビジョニングに使用する基本イメージを VHD から作成できるという点で、[カスタム イメージ](./devtest-lab-create-template.md)に似ています。どちらの方法がご使用の環境に適しているかを判断するには、「[DevTest ラボのカスタム イメージと数式の比較](./devtest-lab-comparing-vm-base-image-types.md)」の記事を参照してください。

## 新しい数式の作成
DevTest ラボの*ユーザー* アクセス許可が付与されていればだれでも、ラボで数式をベースとして使用して VM を作成することができます。数式を作成する方法は 2 つあります。

- 最初から - 数式のすべての特性を最初から定義するときに使用します。
- 既存のラボ VM から - 既存の VM の設定に基づいて数式を作成するときに使用します。

### 最初から新しい数式を作成する
新しい数式を最初から作成する手順は次のとおりです。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. 選択したラボの **[設定]** ブレードが表示されます。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) を選択します。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、**[+ Add]** (+ 追加) を選択します。

    ![Add a new formula](./media/devtest-lab-manage-formulas/add-formula.png)

1. **[Choose a base]** (ベースの選択) ブレードで、数式を作成する際のベースにするカスタム イメージまたは Marketplace イメージを選択します。

    ![Base list](./media/devtest-lab-manage-formulas/base-list.png)

1. **[Create formula]** (数式の作成) ブレードで、次の値を指定します。

	- **Formula name** (数式の名前) - 数式の名前を入力します。この値は、VM を作成する際に基本イメージの一覧に表示されます。入力した名前は検証され、有効でない場合は有効な名前の要件を示すメッセージが表示されます。
	- **説明** - 数式のわかりやすい説明を入力します。この値は、VM を作成する際に数式のコンテキスト メニューに表示されます。
	- **イメージ** - このフィールドには、前のブレードで選択した基本イメージの名前が表示されます。
	- **[VM サイズ]** - 定義済みの項目の中から、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定するものを選びます。
	- **[仮想ネットワーク]** - 目的の仮想ネットワークを選びます。
	- **[サブネット]** - 目的のサブネットを選びます。
	- **パブリック IP アドレス** - 選択したサブネットに対してパブリック IP アドレスを許可するようにラボのポリシーが設定されている場合は、**[はい]** または **[いいえ]** を選択して IP アドレスをパブリックにするかどうかを指定します。パブリック IP アドレスを許可するように設定されていない場合、このオプションは無効になっており、**[いいえ]** が選択されています。
	- **[アーティファクト]** - アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。数式ではセキュリティで保護された文字列の値を保存しないため、セキュリティで保護された文字列であるアーティファクト パラメーターは表示されないことに注意してください。

    	![Create formula](./media/devtest-lab-manage-formulas/create-formula.png)

1. **[作成]** を選択すると、数式が作成されます。数式が正常に作成されると、**[Lab formulas]** (ラボ数式) ブレードに一覧表示されます。

	![Newly created formula](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### ラボ VM から新しい数式を作成する
既存の VM に基づいて数式を作成する手順は次のとおりです。

> [AZURE.NOTE] ラボ VM からの新しい数式の作成がサポートされるのは、2016 年 3 月 30 日以降に作成された VM のみです。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボ ブレードの **[My VMs]** (マイ VM) というタイトルのセクションを見つけ、新しい数式の作成のベースにする VM を選択します。

	![Labs VMs](./media/devtest-lab-manage-formulas/my-vms.png)

1. VM の **[設定]** ブレードで、**[Create Formula]** (数式の作成) を選択します。

	![Create formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. **[Create Formula]** (数式の作成) ブレードで、新しい数式の**名前**と**説明**を入力し、**[OK]** を選択します。数式が正常に作成されると、**[Lab formulas]** (ラボ数式) ブレードに一覧表示されます。

	![[Create Formula] \(数式の作成) ブレード](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## 数式の変更
数式を変更するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) を選択します。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、変更する数式を選択します。

1. **[Update formula]** (数式の更新) ブレードで、必要な編集を行い、**[更新]** を選択します。

## 数式の削除 
数式を削除するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) を選択します。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、削除する数式の右側にある省略記号を選択します。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. 数式のコンテキスト メニューで、**[削除]** を選択します。

    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. 削除の確認のダイアログ ボックスで **[はい]** を選択します。

    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## 次のステップ
VM の作成時に使用する数式を作成したら、次は [VM をラボに追加](./devtest-lab-add-vm-with-artifacts.md)します。

<!---HONumber=AcomDC_0831_2016-->