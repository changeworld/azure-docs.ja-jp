<properties
	pageTitle="Azure Automation を使用した Azure Service Bus の管理"
	description="Azure Automation サービスを使用して、Azure Service Bus を管理する方法について説明します。"
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="csand"/>



# Azure Automation を使用した Azure Service Bus の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Service Bus の管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](http://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。

## Azure Automation を Azure Service Bus の管理に役立てる方法

[Service Bus REST API](https://msdn.microsoft.com/library/azure/hh780717.aspx) を使用することによって、Azure Automation で Service Bus を管理できます。Azure Automation 内では、REST API を使用して Service Bus タスクの多くを実行する PowerShell ワークフロー スクリプトを記述できます。このような Azure Automation 内の REST API 呼び出しと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスとサード パーティ システム全体に及ぶ複雑なタスクを自動化することもできます。

## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Azure Service Bus を管理する方法について説明しました。Azure Automation の詳細については、以下のリンクを参照してください。

* [Azure オートメーションの使用](../automation-create-runbook-from-samples.md) (Azure Automation の作業開始のチュートリアル)
* [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md) (記事)
 

<!---HONumber=Oct15_HO3-->