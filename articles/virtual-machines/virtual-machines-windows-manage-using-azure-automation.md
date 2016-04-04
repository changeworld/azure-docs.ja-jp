<properties
	pageTitle="Azure Automation を使用した VM の管理| Microsoft Azure"
	description="Azure Automation サービスを使用して大規模に Azure 仮想マシンを管理する方法について説明します。"
	services="virtual-machines-windows, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="jolevy"/>



#Azure Automation を使用した Azure Virtual Machines の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure 仮想マシンの管理を簡略化する方法について紹介します。


## Azure Automation とは

[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。


## Azure Automation を Azure 仮想マシンの管理に役立てる方法

Virtual Machines は、[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) を使用して Azure Automation での管理できます。Azure Automation は Azure PowerShell コマンドレットを含むため、サービス内ですべての仮想マシン管理タスクを実行することができます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。


## 次のステップ

ここまでは、Azure Automation の基本と Azure Automation を使用して仮想マシンを管理する方法について説明しました。詳細については、次のリンクを参照してください。

- [Azure Automation Overview (Azure Automation の概要)](../automation/automation-intro.md)
- [初めての Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation の学習マップ](https://azure.microsoft.com/documentation/learning-paths/automation/)

<!---HONumber=AcomDC_0323_2016-->