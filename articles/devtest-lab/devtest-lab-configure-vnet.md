<properties
	pageTitle="DevTest ラボ用の仮想ネットワークの構成 |Microsoft Azure"
	description="既存の仮想ネットワークとサブネットを構成し、それらを VM で使用する方法について説明します。"
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

# DevTest ラボ用の仮想ネットワークの構成

## 概要

記事「[アーティファクトを含む VM の Azure DevTest ラボへの追加](devtest-lab-add-vm-with-artifacts.md)」で説明しているように、ラボで VM を作成する際に、その VM に構成されている仮想ネットワーク (およびサブネット) を指定できます。これを行う 1 つのシナリオは、ExpressRoute またはサイト間 VPN で設定されている仮想ネットワークを使用して、VM から企業ネットワーク リソースにアクセスしたい場合です。以下のセクションでは、VM の作成時に選択できるように、ラボの Virtual Network の設定に既存の仮想ネットワークを追加する方法を説明します。

## Azure ポータルを使用して DevTest ラボの仮想ネットワークを構成する
次に、DevTest ラボに既存の仮想ネットワーク (およびサブネット) を追加し、同じラボで VM を作成するときにそれを使用できるようにするための手順を説明します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. 選択したラボの **[設定]** ブレードが表示されます。

1. **[仮想ネットワーク]** をタップします。

	![仮想ネットワークは、ラボの [設定] ブレードから構成できます。](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. **[仮想ネットワーク]** ブレードに、現在のラボに構成した仮想ネットワークのほか、ラボ用に作成される既定の仮想ネットワークの一覧が表示されます。

1. **[+ 追加]** をタップします。

	![既存の仮想ネットワークをラボに追加する](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. **[仮想ネットワーク]** ブレードで、**[仮想ネットワークの選択]** をタップします。

	![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. **[仮想ネットワークの選択]** ブレードで、目的の仮想ネットワークを選択します。ブレードには、サブスクリプションのラボと同じリージョン下にあるすべての仮想ネットワークが表示されます。

1. 仮想ネットワークを選択し、**[仮想ネットワーク]** ブレードに戻ると、いくつかのフィールドが有効になっていることがわかります。

	![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. 仮想ネットワーク/ラボの組み合わせの説明を指定します。

1. ラボ VM の作成にサブネットを使用できるようにするには、**[VM の作成に使用]** オプションをオンにします。

1. サブネットでパブリック IP アドレスを許可するには、**[パブリック IP の許可]** オプションをオンにします。

1. **[ユーザーあたりの最大 VM 数]** フィールドに、サブネットごとのユーザーあたりの最大 VM 数を指定します。無制限の VM 数が必要な場合は、このフィールドを空白のままにします。

1. **[保存]** をタップします。

1. 仮想ネットワークが構成されたので、新しい VM の作成時にそれを選択できます。これについては、記事「[アーティファクトを含む VM の Azure DevTest ラボへの追加](devtest-lab-add-vm-with-artifacts.md)」で説明しています。

## 次のステップ

ラボに目的の仮想ネットワークを追加したら、次の手順は[DevTest ラボに VM を追加する](devtest-lab-add-vm-with-artifacts.md)ことです。

<!---HONumber=AcomDC_0323_2016-->