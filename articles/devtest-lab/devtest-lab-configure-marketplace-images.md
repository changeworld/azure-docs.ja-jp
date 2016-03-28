<properties
	pageTitle="DevTest ラボで Azure Marketplace イメージの設定を構成する | Microsoft Azure"
	description="DevTest ラボで VM を作成する場合にどの Azure Marketplace イメージを使用できるようにするかを構成する"
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
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# DevTest ラボで Azure Marketplace イメージの設定を構成する

## 概要

DevTest ラボでは、実際のラボで使用する Azure Marketplace イメージの構成方法に応じて、Azure Marketplace イメージに基づく VM を新規に作成することができます。この記事では、DevTest ラボで新しい VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法を説明します。

## VM を作成する際に使用できるようにする Azure Marketplace イメージを選択する

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. 選択したラボの **[設定]** ブレードが表示されます。

1. **[設定]** ブレードで、**[Marketplace イメージ]** をタップします。

	![Azure Marketplace イメージの使用方法を構成する](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. 対象となるすべての Azure Marketplace イメージを新しい VM のベースとして利用できるようにするかどうかを指定します。**[はい]** を選択すると、次の条件をすべて満たす Azure Marketplace イメージのすべてがラボで使用できるようになります。

	- イメージは 1 つの VM を作成する、**かつ**
	- イメージは Azure Resource Manager を使用して VM をプロビジョニングする、**かつ**
	- イメージは追加のライセンス プランの購入を必要としない。
	
	イメージの使用を許可しない場合、または使用できるようにするイメージを指定する場合は、**[いいえ]** を選択します。
 
	![すべての Marketplace イメージを VM のベース イメージとして使用できるようにするオプション](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. 前の手順で **[いいえ]** を選択した場合は、**[許可するイメージ/すべて選択]** チェック ボックスがオンになります。このオプションを検索ボックスと組み合わせて使用すると、一覧に表示されたすべてのアイテムをすばやく選択または選択解除することができます。Azure Marketplace イメージに対応するチェック ボックスをオンにすることで、VM の作成で使用できるようにするイメージを個別に選択することもできます。どの Azure Marketplace イメージもラボでの使用を許可しない場合は、一覧で何も選択しないでください。

	![どの Azure Marketplace イメージを VM のベース イメージとして使用できるようにするかを指定できる](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## 次のステップ

VM を作成するときに使用できるようにする Azure Marketplace イメージを構成したら、次に [VM を Azure DevTest ラボに追加](./devtest-lab-add-vm-with-artifacts.md)します。

<!---HONumber=AcomDC_0316_2016-->