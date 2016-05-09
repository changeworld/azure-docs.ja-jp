<properties
	pageTitle="VM を作成するための DevTest Labs 数式の管理 | Microsoft Azure"
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
	ms.date="04/22/2016"
	ms.author="tarcher"/>

# VM を作成するための DevTest Labs 数式の管理

## 概要

数式は、[カスタム イメージ](./devtest-lab-create-template.md)や [Marketplace イメージ](./devtest-lab-configure-marketplace-images.md)と同じく、VM を短時間でプロビジョニングするための手法です。DevTest Labs における数式とは、ラボ VM の作成に使用できる既定のプロパティ値の一覧です。数式から VM を作成する際に、この既定値をそのまま使用することも変更することもできます。

この記事では、次のタスクを実行する方法について説明します。

- [数式の新規作成](#create-a-new-formula)
- [数式を使用した VM の新規作成](#use-a-formula-to-create-a-new-vm)
- [数式の変更](#modify-a-formula)
- [数式の削除](#delete-a-formula)

> [AZURE.NOTE] 数式は、VM のプロビジョニングに使用する基本イメージを VHD から作成できるという点で、[カスタム イメージ](./devtest-lab-create-template.md)に似ています。どちらの方法がご使用の環境に適しているかを判断するには、記事「[Comparing Custom Images and Formulas in DevTest Labs (DevTest Labs のカスタム イメージと数式の比較)](./devtest-lab-comparing-vm-base-image-types.md)」を参照してください。

## 数式の新規作成
DevTest Labs の*ユーザー* アクセス許可が付与されているユーザーは、ラボで数式をベースとして使用して仮想マシンを作成できます。数式を作成する方法は 2 つあります。

- From scratch (ゼロから) - 数式のすべての特性を最初から定義するときに使用します。
- From an existing lab VM (既存のラボ VM から) - 既存の VM の設定をベースとして数式を作成するときに使用します。

### ゼロから数式を新規作成する
新しい数式を最初から作成する手順は次のとおりです。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. 選択したラボの **[設定]** ブレードが表示されます。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) をタップします。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、**[+ 追加]** をタップします。

    ![Add a new formula](./media/devtest-lab-manage-formulas/add-formula.png)

1. **[Choose a base]** (ベースの選択) ブレードで、数式の作成のベースにするカスタム イメージまたは Marketplace イメージをタップします。

    ![Base list](./media/devtest-lab-manage-formulas/base-list.png)

1. **[Create formula]** (数式の作成) ブレードで、次の値を指定します。

	- **Formula name** (数式の名前) - 数式の名前を入力します。この値は、VM を作成する際に基本イメージの一覧に表示されます。入力した名前は検証され、有効でない場合は有効な名前の要件を示すメッセージが表示されます。
	- **説明** - 数式のわかりやすい説明を入力します。この値は、VM を作成する際に数式のコンテキスト メニューに表示されます。
	- **イメージ** - このフィールドには、前のブレードで選択した基本イメージの名前が表示されます。 
	- **VM サイズ** - 作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを、定義済みの項目から選んでタップして指定します。
	- **仮想ネットワーク** - 目的の仮想ネットワークを選んでタップします。
	- **サブネット** - 目的のサブネットを選んでタップします。
	- **パブリック IP アドレス** - 選択したサブネットに対してパブリック IP アドレスを許可するようにラボのポリシーが設定されている場合は、**[はい]** または **[いいえ]** を選択して IP アドレスをパブリックにするかどうかを指定します。パブリック IP アドレスを許可するように設定されていない場合、このオプションは無効になっており、**[いいえ]** が選択されています。
	- **アーティファクト** - 基本イメージに追加するアーティファクトをアーティファクトの一覧から選んでタップし、構成します。数式ではセキュリティで保護された文字列の値を保存しないため、セキュリティで保護された文字列であるアーティファクト パラメーターは表示されません。 

    	![Create formula](./media/devtest-lab-manage-formulas/create-formula.png)

1. **[作成]** をタップすると、数式が作成されます。作成された数式が、ポータルの **[Lab formulas]** (ラボ数式) ブレードに一覧表示されます。

	![Newly created formula](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### ラボ VM から数式を新規作成する
既存の VM をベースにして数式を作成する手順は次のとおりです。

> [AZURE.NOTE] ラボ VM からの数式の新規作成がサポートされるのは、2016 年 3 月 30 日以降に作成された VM のみです。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. [ラボ] ブレードの **[My VMs]** (マイ VM) というタイトルのセクションで、新しい数式の作成のベースにする VM をクリックします。

	![Labs VMs](./media/devtest-lab-manage-formulas/my-vms.png)

1. VM の **[設定]** ブレードで、**[Create Formula]** (数式の作成) をタップします。

	![Create Formula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. **[Create Formula]** (数式の作成) ブレードで、新しい数式の**名前**と**説明**を入力し、**[OK]** をタップします。作成された数式が、ポータルの **[Lab formulas]** (ラボ数式) ブレードに一覧表示されます。

	![Create Formula blade](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## 数式の変更
数式を変更するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) をタップします。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、変更する数式をタップします。

1. **[Update formula]** (数式の更新) ブレードで、必要な編集を行い、**[更新]** をタップします。

## 数式の削除 
数式を削除するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. ラボの **[設定]** ブレードで、**[Formulas]** (数式) をタップします。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **[Lab formulas]** (ラボ数式) ブレードで、削除する数式の右側にある省略記号をクリックします。

    ![Formula menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. 数式のコンテキスト メニューで、**[削除]** を選択します。

    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. 削除の確認のダイアログ ボックスで **[はい]** をタップします。

    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## 次のステップ
VM の作成時に使用する数式を作成できたら、次は [VM を DevTest Labs に追加](./devtest-lab-add-vm-with-artifacts.md)します。

<!---HONumber=AcomDC_0427_2016-->