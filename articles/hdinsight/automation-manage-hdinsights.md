<properties
	pageTitle="Azure Automation を使用した Azure HDInsight の管理"
	description="Azure Automation サービスを使用して、Azure HDInsight を管理する方法について説明します。"
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="elcooper"/>



#Azure Automation を使用した Azure HDInsight の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure HDInsight でのクラスターの管理を簡略化し、一般的なタスクを自動化する方法について紹介します。

## Azure Automation とは
[Azure Automation](http://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウドとデータセンターの管理を簡略化するための Azure サービスです。Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動的に実行するようスケジューリングすることにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。


## Azure Automation を HDInsight の管理に役立てる方法

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) ツールで利用可能な [Azure HDInsight コマンドレット](https://msdn.microsoft.com/library/azure/dn479228.aspx)を使用することにより、Azure Automation で HDInsight を管理できます。Azure Automation には、こうしたコマンドレットがあらかじめ用意されており、サービス内でHDInsight 管理タスクを実行することができます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

Azure HDInsight コマンドレットでは、Linux または Windows での HDInsight クラスターのプロビジョニング、クラスターの拡大/縮小、クラスターの管理、および MapReduce ジョブの送信などのタスクを自動化できます。これらは、Azure Automation の PowerShell を使用して自動化できる多数のタスクのほんの一部です。


## 次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure HDInsight を管理する方法について説明しました。Azure Automation の詳細については、こちらのリンクをご覧ください。

* Azure Automation の[作業開始のチュートリアル](../automation-create-runbook-from-samples.md)に関するページを参照してください。
* サンプルについては、[スクリプト センター](http://aka.ms/scriptcentergallery)を参照してください。  

 

<!---HONumber=Sept15_HO2-->