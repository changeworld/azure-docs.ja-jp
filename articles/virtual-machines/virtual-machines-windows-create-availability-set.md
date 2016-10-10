<properties
	pageTitle="VM の可用性セットを作成する | Microsoft Azure"
	description="Azure ポータル、またはResource Manager のデプロイ モデルを使用した PowerShell を使用して仮想マシンに対する可用性セットを作成する方法について説明します。"
	keywords="可用性セット"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>


# 可用性セットの作成 

ポータルを使用しているときに、VM を可用性セットに含める場合は、まずその可用性セットを作成する必要があります。

可用性セットの作成と使用の詳細については、「[仮想マシンの可用性管理](virtual-machines-windows-manage-availability.md)」を参照してください。


## ポータルを使用して、VM を作成する前に可用性セットを作成する

1. ハブ メニューで **[参照]** をクリックして、**[可用性セット]** を選択します。

2. **[可用性セットブレード]** で、**[追加]** をクリックします。

	![新しい可用性セットを作成するための [追加] ボタンを示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. **可用性セットの作成** ブレードで、セットの情報を入力します。

	![可用性セットの作成のために、入力する必要がある情報を示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

	- **名前** - 名前は 1 から 80 文字の数字、文字、ピリオド、アンダー スコアおよびダッシュで構成する必要があります。先頭の文字は、文字または数字にする必要があります。末尾の文字は、文字、数字、またはアンダースコアにする必要があります。
	- **障害ドメイン** - 障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。既定で、VM は最大 3 つの障害ドメイン間で分散され、1 から 3 の間で変更することができます。
	- **更新ドメイン** - 5 つの更新ドメインが既定で割り当てられ、1 から 20 に設定することができます。更新ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。たとえば、5 つの更新ドメインを指定する場合、1 つの可用性セット内に 5 つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは 1 つ目の仮想マシンと同じ更新ドメイン内に配置され、7 つ目は 2 つ目の仮想マシンと同じ UD 内に配置されるという方法で処理されます。再起動は順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。
	- **サブスクリプション** - 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。
	- **リソース グループ** - 矢印をクリックしてドロップダウンからリソース グループを選択することにより、既存のリソース グループを選択します。名前を入力して、新しいリソース グループを作成することもできます。名前には、次の文字を含めることができます。文字、数字、ピリオド、ダッシュ、アンダースコアおよびかっこです。名前の末尾を句点にすることはできません。可用性グループのすべての VM は、可用性セットと同じリソース グループ内に作成する必要があります。
	- **場所** -ドロップダウン から場所を選択します。

4. 情報の入力を完了したら、**[作成]** をクリックします。可用性グループを作成したら、ポータルの更新後に一覧で確認できます。

## ポータルを使用して、仮想マシンと可用性セットを同時に作成する

ポータルを使用して新しい VM を作成する場合は、新しい可用性セットの最初の VM の作成中に、VM の可用性セットも作成することができます。

![VM の作成時に、新しい可用性セットを作成するためのプロセスを示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## 既存の可用性セットに新しい VM を追加する

セットに属する各追加 VM が、同じ**リソース グループ** で作成されているかを確認し、手順 3 で既存の可用性セットを選択します。

![VM に使用する既存の可用性セットを選択する方法を示すスクリーン ショット。](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## PowerShell を使用して可用性セットを作成する

この例では、**West US** という場所の**RMResGroup** リソース グループに可用性セットを作成します。これは、可用性セットに最初の VM を作成する前に実行する必要があります。

	New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
	
詳細については、[New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx) を参照してください。


## トラブルシューティング

- VM を作成するときに、必要な可用性セットがポータルのドロップダウン リストにない場合は、可用性セットが別のリソース グループに作成された可能性があります。可用性セットのリソース グループがわからない場合は、ハブ メニューに移動し、[参照]、[可用性セット] をクリックして、可用性セットが属するリソース グループの一覧を確認します。


## 次のステップ

[データ ディスク](virtual-machines-windows-attach-disk-portal.md)を追加して、記憶域を VM にさらに追加します。

<!---HONumber=AcomDC_0928_2016-->