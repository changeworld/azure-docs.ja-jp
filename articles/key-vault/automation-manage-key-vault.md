<properties
	pageTitle="Azure Automation を使用した Azure Key Vault の管理 | Microsoft Azure"
	description="Azure Automation サービスを使用して Azure Key Vault を管理する方法について説明します。"
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="csand"/>



#Azure Automation を使用した Azure Key Vault の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Key Vault でのキーとシークレットの管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。


## Azure Automation を Azure Key Vault の管理に役立てる方法

[PowerShell ギャラリー](https://azure.microsoft.com/blog/azps-1-0/)で提供されている [Azure Key Vault コマンドレット](https://msdn.microsoft.com/library/azure/dn868052.aspx)を使用することにより、Azure Automation で Key Vault を管理できます。このモジュールを Azure Automation にインポートして、サービス内で Key Vault 管理タスクの多くを実行することができます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

Azure Key Vault コマンドレットによって、キーの作成やインポート、シークレットの作成や更新、キーの属性の更新、キーやシークレットの取得、キーやシークレットの削除などのタスクを実行できます。


## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Azure Key Vault を管理する方法について説明しました。Azure Automation の詳細については、次の各リンクを参照してください。

* Azure Automation の[作業開始のチュートリアル](../automation/automation-first-runbook-graphical.md)に関するページを参照してください。
* [Azure Key Vault の PowerShell スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091)に関するページを参照してください。

<!---HONumber=AcomDC_0413_2016-->