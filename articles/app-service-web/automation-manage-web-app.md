<properties
	pageTitle="Azure Automation を使用した Azure Web App の管理"
	description="Azure Automation サービスを使用して、Azure Web App を管理する方法について説明します。"
	services="app-service\web, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="csand"/>



#Azure Automation を使用した Azure Web App の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Web App の管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT と DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。


## Azure Automation を Azure Web App の管理に役立てる方法

[Azure PowerShell ツール](https://msdn.microsoft.com/library/azure/jj156055.aspx)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で Azure Web App を管理できます。Azure Automation には、このような Azure Web App PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての Web App 管理タスクを実行できます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスとサード パーティ システム全体の複雑なタスクを自動化することもできます。


## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Azure Web App を管理する方法について説明しました。Azure Automation の詳細については、これらのリンクをご覧ください。

* [Azure Automation の使用](../automation-intro.md) (Azure Automation の作業開始のチュートリアル)
 

<!---HONumber=AcomDC_0128_2016-->