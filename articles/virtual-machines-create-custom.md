<properties urlDisplayName="Create a custom VM" pageTitle=" Azure でのカスタム仮想マシンの作成" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/ja-jp/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />

#カスタム仮想マシンの作成方法

**[簡易作成]** オプションよりも多くの構成が選択できるため、*カスタム*仮想マシンとは **[ギャラリーから]** オプションを使用して作成する仮想マシンのことを単純に意味します。選択できるオプションは次のとおりです。

- 仮想ネットワークと VM との接続
- VM エージェントとマルウェア対策用などの拡張機能のインストール 
- 既存のクラウド サービスへの VM の追加 
- 可用性セットへの VM の追加

**重要**:仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]



<!--HONumber=35.1-->
