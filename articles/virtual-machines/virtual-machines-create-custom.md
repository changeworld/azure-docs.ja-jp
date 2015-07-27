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
	ms.topic="get-started-article" 
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#カスタム仮想マシンの作成方法

*カスタム*仮想マシンとは、**[ギャラリーから]** オプションで作成する仮想マシンのことです。**[簡易作成]** オプションよりも構成の選択肢が多いことから、このように呼びます。選択できるオプションは次のとおりです。

- 仮想ネットワークと VM との接続
- VM エージェントとマルウェア対策用などの拡張機能のインストール
- 既存のクラウド サービスへの VM の追加
- 既存のストレージ アカウントへの VM の追加
- 可用性セットへの VM の追加

**重要**: 仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるようにするには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]
 

<!---HONumber=July15_HO3-->