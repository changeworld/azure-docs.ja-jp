<properties 
	pageTitle="Chef と Azure Virtual Machines について" 
	description="Azure の VM での Chef のインストールと構成について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="kathydav"/>

# Chef と Azure Virtual Machines について

Chef は、インフラストラクチャの構築、デプロイ、管理を行う自動化システムです。リソースの管理は、レシピという再利用可能な定義を使用して、Web サーバー構成などのタスクに関する指示を出すことで実行します。   

Chef はクライアント サーバー システムです。Chef サーバーを使用するためのオプションについては、「[Choose your installation (インストールの種類を選択してください)](http://www.getchef.com/chef/choose-your-version/)」をご参照ください。クライアントを設定するには、Chef サーバーについての情報が必要です。 

Azure 仮想マシンに Chef クライアントをインストールするには、以下の方法を利用します。

- Azure 管理ポータルを使用して、Windows Server 2012 または Windows Server 2012 R2 を実行する仮想マシンを作成する際に Chef クライアントをインストールする。手順については、[Microsoft Azure ポータル](https://docs.chef.io/azure_portal.html)に関するページをご覧ください。
- Windows PowerShell を使用して、既存の仮想マシンに Chef クライアントをインストールする。Github でサンプル [script](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) が公開されています。
- [knife-azure](http://docs.getchef.com/plugin_knife_azure.html) という Chef プラグインを使用して、仮想マシンのインスタンスを作成し、Chef クライアントをインストールする。   


## その他のリソース
[Chef と Microsoft Azure に関するページ]

[Windows Server が実行されている仮想マシンにログオンする方法]

[Linux を実行する仮想マシンにログオンする方法]

[拡張機能の管理]

<!--Link references-->
[Chef と Microsoft Azure に関するページ]: http://www.getchef.com/solutions/azure/
[Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
[Linux を実行する仮想マシンにログオンする方法]: ../virtual-machines-linux-how-to-log-on
[拡張機能の管理]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=47-->
 