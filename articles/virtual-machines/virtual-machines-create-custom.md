<properties
	pageTitle="Azure でのカスタム仮想マシンの作成"
	description="Azure 上でのカスタム仮想マシンの作成方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="kathydav"/>

#カスタム仮想マシンを作成する方法

*カスタム*仮想マシンとは、**[ギャラリーから]** オプションで作成する仮想マシンのことです。**[簡易作成]** オプションよりも構成の選択肢が多いことから、このように呼びます。選択できるオプションは次のとおりです。

- 仮想ネットワークへ仮想マシンを接続する
- マルウェア対策としてなどで Azure Virtual Machine Agent や Azure Virtual Machine Extensions をインストールする。
- 仮想マシンを既存のクラウド サービスに追加する。
- 仮想マシンを既存のストレージ アカウントに追加する。
- 仮想マシンを可用性セットに追加する。

**重要**: 仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるようにするには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」をご覧ください。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

<!---HONumber=August15_HO6-->