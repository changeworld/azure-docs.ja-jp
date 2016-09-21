<properties
	pageTitle="Microsoft Azure Virtual Machines、Cloud Services、および Web Apps での自動スケールの概要 | Microsoft Azure"
	description="Microsoft Azure での自動スケールの概要。Virtual Machines、Cloud Services、および Web Apps に適用されます。"
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="robb"/>

# Microsoft Azure Virtual Machines、Cloud Services、および Web Apps での自動スケールの概要

この記事では、Microsoft Azure 自動スケールの概要、利点、初歩的な使用方法について説明します。

Azure Insights の自動スケールは、以下のものだけに適用されます。

* [仮想マシン スケール セット](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [Cloud Services](https://azure.microsoft.com/services/cloud-services/)
* [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure には、2 つの自動スケールの方法があります。以前のバージョンの自動スケールは、Virtual Machines (可用性セット) に適用されます。この機能はサポートが限定されているので、より高速で信頼性の高い自動スケールのサポートを得るために、VM スケール セットに移行することをお勧めします。この記事には、古い技術を使用する方法についてのリンクも含まれています。


## 自動スケールとは 

自動スケールを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理することができます。アイドル状態のリソースに対して無駄な料金を支払わなくて済みます。この機能では、一連の規則に基づいて、コンピューティング リソースが自動的に追加または削除されます。図 1 に、この概念を示します。

![Autoscaling concept explained](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**図 1: 自動スケールの概念の説明**

自動スケールは、水平的なスケーリングだけを行います。つまり、VM インスタンスの数が増加 ("アウト") したり、減少 ("イン") したりします。水平スケーリングは、クラウド環境では負荷を処理するために数千の VM さえ実行できるため、より柔軟です。もう 1 つのスケールの種類は、垂直です。垂直スケーリングでは、VM の数は同じままですが、VM の能力を強めたり ("アップ") 弱めたり ("ダウン") します。能力は、メモリ、CPU 速度、ディスク領域などで計測します。垂直スケーリングには、より多くの制限があります。より大きなハードウェアを使用できるかどうかに依存しており、リージョンによって状況が異なります。また、すぐに上限に達してしまいます。垂直スケーリングでは、多くの場合、VM の停止と再起動も必要になります。詳細については、「[Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)」を参照してください。


自動スケールでは、リソースをスケール アウトまたはスケール インするタイミングに関する規則を作成する必要があります。スケール操作を制御するために設定できる条件には、次のようなものがあります。

* 実行するインスタンスの**最小**と**最大**の数。最小数ではアプリケーションが常に確実に実行されるようにし、最大数ではコストを制御します。
* 自動スケールの**規則または条件**。メトリックまたはスケジュール ベースのスケーリングにすることができます。
* **クール ダウン時間**。自動スケール イベントの後、別の自動スケール イベントの発生を許可するまでの待機時間です。この期間は、"フラッピング" と呼ばれる状態を防ぐためのものです。フラッピングは、数分という短時間に VM が追加されたり削除されたりした場合に発生する状態です。VM の起動または停止にはコストがかかります。フラッピングはコストの節約にならず、VM を起動したり停止したりしても有益な処理は行われません。そのため、単に VM を実行しておくよりも不利な結果になります。

   
構成可能な値の完全な一覧については、[自動スケール REST API](https://msdn.microsoft.com/library/dn931928.aspx) に関するページを参照してください。


## 概念図  
図 2 は、自動スケールの概念的な概要を示しています。その後に、図の各部分についての説明があります。

![アラートの追加](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**図 2: 自動スケール プロセスの概要**

## リソース メトリック 
リソースは、メトリックを出力します。このメトリックが、後で規則によって処理されます。メトリックは、さまざまな方法で取得されます。VM スケール セットは、Azure 診断エージェントからのテレメトリ データを使用します。一方、Web アプリおよびクラウド サービスのテレメトリは、Azure インフラストラクチャから直接取得されます。一般的に使用される統計情報として、CPU 使用率、メモリ使用量、スレッド数、キューの長さ、ディスク使用量などがあります。使用できるテレメトリ データの一覧については、[自動スケールの一般的なメトリック](insights-autoscale-common-metrics.md)に関するページを参照してください。

 
## Time
スケジュール ベースの規則は、UTC に基づいています。規則を設定する際に、タイム ゾーンを正しく設定する必要があります。

## 自動スケールの規則
次の図では 1 つの自動スケール規則だけを示していますが、複数の規則を使用できます。状況に応じて、複雑に重複する規則を作成することができます。規則の種類には、次のようなものがあります。
 
 - **メトリック ベース** - たとえば、CPU 使用率が 50% を超える場合に、この操作を実行します。
 - **時間ベース** - たとえば、特定のタイム ゾーンで毎週土曜の午前 8 時に webhook をトリガーします。

 
## 自動スケール操作と自動化

ルールは、1 つまたは複数の種類の操作をトリガーできます。

- **スケール** - VM をスケール インまたはアウトします。
- **電子メール** - サブスクリプションの管理者と共同管理者、および指定された追加の電子メール アドレスに電子メールを送信します。
- **webhook による自動化** - Azure の内部または外部で複数の複雑な操作をトリガーできる webhook を呼び出します。Azure 内で、Azure Automation Runbook、Azure 関数、または Azure ロジック アプリを開始できます。Azure 外部のサード パーティ URL の例として、Slack や Twilio のようなサービスがあります。


## Resource Manager の規則の詳細

自動スケール規則は、Azure Resource Manager テンプレートで、次の構造を持っています。

![Azure Resource Manager Autoscale Rule Structure](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

複数のプロファイルをリストに入れることができます。各プロファイルには、複数の規則を指定できます。通知方法と場所は、プロファイルの後に含められます。通知の例として、URI を持つ webhook や、電子メール アドレスを持つ電子メールがあります。

コード例については、次を参照してください。

* [VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [自動スケール REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## アクセスの方法 
以下のものを通じて、自動スケール規則を設定することができます。

- Azure ポータル
- PowerShell
- 共通ライブラリ インターフェイス (CLI)
- Insights REST API

## 自動スケールのチュートリアル

- [クラウド サービスの拡大/縮小](../cloud-services/cloud-services-how-to-scale-portal.md)
- [Web アプリのスケーリング](insights-how-to-scale.md)
- [従来の仮想マシン可用性セットのスケーリング](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [Windows での VM スケール セットのスケーリング](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [ での VM スケール セットのスケーリング](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## 次のステップ

自動スケールについてさらに学習するには、上の一覧にある自動スケールのチュートリアルを使用するか、以下のリソースを参照してください。

- [Azure Insights の自動スケールの一般的なメトリック](insights-autoscale-common-metrics.md)
- [Azure Insights の自動スケールのベスト プラクティス](insights-autoscale-best-practices.md)
- [自動スケール操作を使用して電子メールと Webhook アラート通知を送信する](insights-autoscale-to-webhook-email.md)
- [自動スケール REST API](https://msdn.microsoft.com/library/dn931953.aspx) - API 全体と、各フィールドと値の意味を参照してください。
- [仮想マシン スケール セットの自動スケールに関するトラブルシューティング](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->