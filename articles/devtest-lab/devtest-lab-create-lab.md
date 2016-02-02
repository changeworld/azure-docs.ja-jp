    <properties
	pageTitle="Create a DevTest Lab | Microsoft Azure"
	description="Create a new DevTest Lab lab for virtual machines"
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
	ms.topic="get-started-article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Azure DevTest ラボの作成

## 前提条件

DevTest ラボを作成するには、次のものが必要です。

- Azure サブスクリプション。Azure 購入オプションの詳細は、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または「[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。ラボを作成するには、サブスクリプションの所有者である必要があります。
- ラボ用の Azure リソース グループ。「[Azure リソース マネージャーの概要](resource-group-overview.md)」と「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。


## ラボを作成する

1. [Azure プレビュー ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップします。

1. 一覧から **[DevTest ラボ]** をタップします。

1. **[DevTest ラボ]** ブレードで **[追加]** をタップします。

    ![DevTest ラボの追加](./media/devtest-lab-create-lab/add-lab-button.png)

1. **[DevTest ラボの作成]** ブレードで:

    1. 新しいラボの **[ラボ名]** を入力します。
    1. ラボに関連付ける **[サブスクリプション]** を選択します。
    1. ラボを格納する **[場所]** を選択します。
    1. **[作成]** をタップします。

    ![DevTest ラボ ブレードの作成](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## 次のステップ

ラボを作成した後は、次の手順を考慮します。

- [DevTest ラボへのアクセスを保護します](devtest-lab-add-devtest-user.md)。

- [ラボのポリシーを設定します](devtest-lab-set-lab-policy.md)。

- [ラボ テンプレートを作成します](devtest-lab-create-template.md)。

- [VM のカスタム アーティファクトを作成します](devtest-lab-artifact-author.md)。

- [アーティファクトを含む VM を Azure DevTest ラボに追加します](devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0128_2016-->