<properties
	pageTitle="Puppet および Azure の仮想マシンについて"
	description="Azure の仮想マシン上での Puppet のインストールおよび構成について説明します。"
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
	ms.date="05/20/2015"
	ms.author="kathydav"/>

#Puppet および Azure の仮想マシンについて


<p>Puppet Enterprise は、インフラストラクチャの構築、デプロイ、管理を行う自動化ソフトウェアです。このソフトウェアを使用すると、検出、プロビジョニング、OS とアプリケーションの構成管理、オーケストレーション、レポートなどを実施して、IT インフラストラクチャのライフサイクルを管理できます。

Puppet はクライアント サーバー システムです。Puppet Master と Puppet Enterprise Agent は、いずれも Microsoft Azure を介してインストールできます。

- Puppet Master は、Ubuntu サーバーにインストールされた構成済みのイメージとして利用できます。既存のサーバーに Puppet Enterprise をインストールすることもできますが、イメージを使用して開始する方法が最も簡単です。エージェントを設定するには、サーバーについての情報が必要です。

- Puppet Enterprise エージェントは仮想マシン拡張機能として利用でき、仮想マシンの作成時にインストールできるほか、既存の仮想マシンにインストールすることも可能です。

手順については、「[Microsoft Windows and Azure (Microsoft Windows と Azure)](http://puppetlabs.com/solutions/microsoft)」ページから、『Getting Started Guide (ファースト ステップ ガイド)』をダウンロードしてください。


##その他のリソース
[Microsoft Azure と Visual Studio との新しい統合]

[Windows Server が実行されている仮想マシンにログオンする方法]

[Linux を実行する仮想マシンにログオンする方法]

[拡張機能の管理]

<!--Link references-->
[Microsoft Azure と Visual Studio との新しい統合]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Windows Server が実行されている仮想マシンにログオンする方法]: virtual-machines-log-on-windows-server.md
[Linux を実行する仮想マシンにログオンする方法]: virtual-machines-linux-how-to-log-on.md
[Azure VM extensions and features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=August15_HO6-->