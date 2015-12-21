<properties 
 pageTitle="Azure Automation を使用した Azure Traffic Manager の管理" 
 description="Azure Automation サービスを使用して Azure Traffic Manager を管理する方法について説明します。" 
 services="traffic-manager, automation" 
 documentationCenter="" 
 authors="joaoma" 
 manager="carmonm" 
 editor=""/>

<tags 
 ms.service="traffic-manager" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.date="11/12/2015" 
 ms.author="joaoma"/>


# Azure Automation を使用した Azure Traffic Manager の管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Traffic Manager の管理を簡略化する方法について紹介します。

## Azure Automation とは

[Azure Automation](http://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT/DevOps スタッフの負担が軽くなります。


## Azure Automation を Azure Traffic Manager の管理に役立てる方法

[Azure PowerShell ツール](https://msdn.microsoft.com/library/azure/jj156055.aspx)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で Traffic Manager を管理できます。Azure Automation はこのような PowerShell コマンドレットを呼び出すことができるため、サービス内ですべての Traffic Manager 管理タスクを実行することができます。Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。


## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Traffic Manager を管理する方法について説明しました。Azure Automation の詳細については、次のリンクを参照してください。

* [Azure Automation の使用](http://go.microsoft.com/fwlink/?LinkId=390560) (Azure Automation のファースト ステップ ガイド)
 

<!---HONumber=AcomDC_1210_2015-->