<properties 
	pageTitle="Azure で Windows を実行するカスタム仮想マシンの作成" 
	description="Azure で Windows を実行するカスタム仮想マシンを作成する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="AZURE CLASSIC PORTAL"/>
	

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="kathydav"/>

# Windows を実行するカスタム仮想マシンを作成する方法

*カスタム*仮想マシンとは、**\[ギャラリーから\]** オプションで作成する仮想マシンのことです。**\[簡易作成\]** オプションよりも構成の選択肢が多いことから、このように呼びます。選択できるオプションは次のとおりです。

- 仮想ネットワークと VM との接続
- VM エージェントとマルウェア対策用などの拡張機能のインストール 
- 既存のクラウド サービスへの VM の追加
- 既存のストレージ アカウントへの VM の追加
- 可用性セットへの VM の追加

> [AZURE.IMPORTANT]仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]



<!--HONumber=52-->
