<properties
	pageTitle="カスタム仮想マシンを作成する | Microsoft Azure"
	description="クラシック デプロイ モデルを使用して Azure ポータルからカスタム仮想マシンを作成する方法。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#カスタム仮想マシンを作成する方法


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。


*カスタム*仮想マシンとは、**[ギャラリーから]** オプションで作成する仮想マシンのことです。**[簡易作成]** オプションよりも構成の選択肢が多いことから、このように呼びます。選択できるオプションは次のとおりです。

- 仮想ネットワークへ仮想マシンを接続する
- マルウェア対策としてなどで Azure Virtual Machine Agent や Azure Virtual Machine Extensions をインストールする。
- 仮想マシンを既存のクラウド サービスに追加する。
- 仮想マシンを既存のストレージ アカウントに追加する。
- 仮想マシンを可用性セットに追加する。

> [AZURE.IMPORTANT]仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定をできるようにするには、仮想マシンの作成時に仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](virtual-networks-overview.md)」を参照してください。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Oct15_HO1-->