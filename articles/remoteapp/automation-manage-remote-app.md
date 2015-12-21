<properties
	pageTitle="Azure Automation を使用した Azure RemoteApp の管理 | Microsoft Azure"
	description="Azure Automation サービスを使用して Azure RemoteApp を管理する方法について説明します。"
	services="remoteapp, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="remoteapp"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="csand"/>



#Azure Automation を使用した Azure RemoteApp の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure RemoteApp の管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](http://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。


## Azure Automation を Azure RemoteApp の管理に役立てる方法

[Azure PowerShell ツール](https://msdn.microsoft.com/library/azure/jj156055.aspx)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で RemoteApp を管理できます。Azure Automation には、このような RemoteApp PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての RemoteApp 管理タスクを実行することができます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。


## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Azure RemoteApp を管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

* [Azure Automation の使用](../automation-create-runbook-from-samples.md) (Azure Automation の作業開始のチュートリアル)

<!---HONumber=AcomDC_1210_2015-->