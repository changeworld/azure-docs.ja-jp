<properties
	pageTitle="Azure Automation を使用した Azure API Management の管理"
	description="Azure Automation サービスを使用して Azure API Management を管理する方法について説明します。"
	services="api-management, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#Azure Automation を使用した Azure API Management の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure API Management の管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](http://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。


## Azure Automation を Azure API Management の管理に役立てる方法

[API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) を使用することによって、Azure Automation で API Management を管理できます。Azure Automation 内では、REST API を使用して API Management タスクの多くを実行する PowerShell ワークフロー スクリプトを記述できます。このような Azure Automation 内の REST API 呼び出しと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスとサード パーティ システム全体に及ぶ複雑なタスクを自動化することもできます。


## 次のステップ

ここまでは、Azure Automation の基本と Azure Automation を使用して Azure API Management を管理する方法について説明しました。詳細については、次の各リンクを参照してください。

* Azure Automation の[作業開始のチュートリアル](automation-create-runbook-from-samples.md)に関するページを参照してください。
* [\#Azure API Management REST API の PowerShell モジュール](https://alexandrebrisebois.wordpress.com/2014/08/17/powershell-module-for-the-azure-api-management-rest-apis/)に関するコミュニティ ブログの投稿をお読みください。

<!--HONumber=54-->