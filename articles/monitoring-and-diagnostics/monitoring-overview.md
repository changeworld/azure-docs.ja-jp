---
title: "Microsoft Azure での監視 | Microsoft Docs"
description: "Microsoft Azure で監視する場合の選択肢。 Azure Monitor, Application Insights Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure での監視の概要
この記事では、Microsoft Azure を監視するために使用できるツールの概要を説明します。 この記事は次のものに当てはまります。 
- Microsoft Azure で実行されている監視アプリケーション 
- Azure 内のオブジェクトを監視できる、Azure の外部で実行されるツールやサービス 

ここでは、利用可能な各種製品やサービスのほか、これらがどのように連携するかについて説明します。 これは、どのツールがどの状況で最も適しているかを判断するのに役立ちます。  

## <a name="why-use-monitoring-and-diagnostics"></a>監視と診断を使用する理由

クラウド アプリのパフォーマンスの問題は、ビジネスに影響を及ぼす場合があります。 複数の相互接続されたコンポーネントや頻繁なリリースにより、いつでもパフォーマンスの低下が生じる可能性があります。 さらに、アプリを開発している場合、通常、テストで見つからなかった問題はユーザーによって発見されます。 このような問題について即座に把握し、問題を診断して修正するためのツールを用意しておく必要があります。 Microsoft Azure には、このような問題を特定するためのさまざまなツールがあります。

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>自身の Azure クラウド アプリを監視する方法

Azure のアプリケーションとサービスを監視するためにさまざまなツールがあります。 それらの機能の一部は重複しています。 これは、歴史的な理由によるものもあれば、アプリケーションの開発と運用の間のずれによるものもあります。 

主なツールを次に示します。

-    **Azure Monitor** は、Azure 上で実行されているサービスを監視するための基本的なツールです。 サービスのスループットと周辺環境に関するインフラストラクチャ レベルのデータが示されます。 お使いのアプリすべてを Azure で管理している場合は、リソースをスケールアップするかスケールダウンするかを決定すると、Azure Monitor により、開始するために使用する情報が提示されます。

-    **Application Insights** は、開発に使用することも、運用環境の監視ソリューションとして使用することもできます。 これは、アプリにパッケージをインストールすると動作します。これにより、状況のより詳しい情報が示されます。 Application Insights のデータには、依存関係、例外トレース、スナップショットのデバッグ、実行プロファイルの応答時間が含まれます。 Application Insights には、このテレメトリすべてを分析するための強力な洗練されたツールが用意されているため、アプリをデバッグする場合にも、ユーザーがそのアプリで行っている操作を把握する場合にも役立ちます。 応答時間の急激な増加の原因がアプリにあるのか、外部リソースの問題にあるのかを見分けることができます。 Visual Studio を使用していて、アプリに問題がある場合は、その問題を修正できるように問題のコード行にすぐに移動されることがあります。  

-    **Log Analytics** は、運用環境で実行されているアプリケーションのパフォーマンスの調整とメンテナンスの計画を必要とするユーザー向けです。 これは Azure に基づいています。 多くのソースからデータを収集して集計しますが、10 ～ 15 分の待ち時間を伴います。 Log Analytics では、Azure、オンプレミス、サード パーティ製のクラウドベースのインフラストラクチャ (Amazon ウェブ サービスなど) に対応する包括的な IT 管理ソリューションを提供しています。 また、多くのソース間でデータを分析するための豊富なツールを提供し、すべてのログを対象とした複雑なクエリを可能にして、指定された条件で事前に通知することができます。  カスタム データも、照会および視覚化できるように中央リポジトリに収集できます。 

-    **System Center Operations Manager (SCOM)** は、大規模なクラウド インストールの管理と監視向けです。 オンプレミスの Windows Sever および Hyper-V ベースのクラウド用の管理ツールとして既に使い慣れているかもしれませんが、Azure アプリと統合したり Azure アプリを管理したりすることもできます。 特に重要なこととして、既存のライブ アプリに Application Insights をインストールできます。  アプリがダウンすると、すぐに通知されます。 Log Analytics は SCOM に代わるものではないことに注意してください。 これらは組み合わせて使用するとうまく機能します。  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Azure Portal での監視へのアクセス
Azure のすべての監視サービスは、1 つの UI ウィンドウで使用できるようになりました。 この領域へのアクセス方法の詳細については、「[Azure Monitor の使用](monitoring-get-started.md)」を参照してください。 

また、特定のリソースの監視機能にもアクセスできます。それには、そのリソースを強調表示し、監視オプションの詳細を確認します。 

## <a name="examples-of-when-to-use-which-tool"></a>使用するツールとそのタイミングの例 

以降のセクションでは、いくつかの基本的なシナリオと、一緒に使用すべきツールを紹介します。 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>シナリオ 1 - 開発中の Azure アプリケーションのエラーを修正する   

**最善の方法は、Application Insights、Azure Monitor、Visual Studio を一緒に使用することです**

現在、Azure では、クラウド内で Visual Studio デバッガーの機能を最大限に活用できます。 テレメトリを Application Insights に送信するよう Azure Monitor を構成します。 Visual Studio で Application Insights SDK をアプリケーションに含めることができるようにします。 Application Insights では 1 回、アプリケーション マップを使用して、実行中のアプリケーションのどの部分が正常でないかどうかを視覚的に見つけることができます。 このような正常でない部分については、既にエラーや例外が探索できるようになっています。 Application Insights でさまざまな分析を使用すると、さらに詳しく調べることができます。 エラーがよくわからない場合は、Visual Studio デバッガーを使用してコードをトレースし、問題をさらに正確に指摘することができます。 

詳細については、[Web アプリの監視](../application-insights/app-insights-azure-web-apps.md)に関する記事を参照し、各種アプリおよび言語に関する指示については、左側の目次を参照してください。  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>シナリオ 2 - 運用環境のみで表示されるエラーについて Azure .NET Web アプリケーションをデバッグする 

> [!NOTE]
> これらの機能はプレビュー段階です。 

**最善の方法は、Application Insights を使用し、可能であれば完全にデバッグできるように Visual Studio を使用することです。**

アプリをデバッグするには、Application Insights スナップショット デバッガーを使用します。 運用環境のコンポーネントで特定のエラーのしきい値が発生すると、システムは、"スナップショット" と呼ばれる時間枠内のテレメトリを自動的にキャプチャします。 キャプチャされる量は、パフォーマンスに影響しない程度に小さく、トレースを許可するには十分なため、運用しているクラウドにとっては安全です。  システムは複数のスナップショットをキャプチャできます。 Azure Portal 内で特定の時点を確認したり、完全なエクスペリエンスを実現するために Visual Studio を使用したりできます。 Visual Studio を使用すると、開発者はリアルタイムでデバッグしているかのようにそのスナップショットを確認できます。 ローカル変数、パラメーター、メモリ、フレームはすべて利用可能です。 開発者は、RBAC ロールによってこの運用データへのアクセスが許可されている必要があります。  

詳細については、[スナップショットのデバッグ](../application-insights/app-insights-snapshot-debugger.md)に関する記事を参照してください。 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>シナリオ 3 - コンテナーまたはマイクロサービスを使用する Azure アプリケーションをデバッグする 

**シナリオ 1 と同じ。Application Insights、Azure Monitor、Visual Studio を一緒に使用します。**Application Insights では、コンテナー内で実行中のプロセスからのテレメトリの収集とマイクロサービス (Kubernetes、Docker、Azure Service Fabric) からのテレメトリの収集もサポートしています。 詳細については、[コンテナーとマイクロサービスのデバッグに関するこちらのビデオをご覧ください](https://go.microsoft.com/fwlink/?linkid=848184)。 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>シナリオ 4 - Azure アプリケーションでパフォーマンスの問題を修正する

[Application Insights プロファイラー](../application-insights/app-insights-profiler.md)は、このような種類の問題のトラブルシューティングに役立つように設計されています。 App Services (Web Apps、Logic Apps、Mobile Apps、API Apps) で実行されているアプリケーションやその他のコンピューティング リソース (仮想マシン、仮想マシン スケールセット (VMSS)、クラウド サービス、Service Fabric など) のパフォーマンスの問題を特定し、トラブルシューティングを行うことができます。 

> [!NOTE]
> 仮想マシン、仮想マシン スケール セット (VMSS)、クラウド サービス、Services Fabric をプロファイルする機能はプレビュー段階です。   

さらに、スマート検出ツールにより、特定の種類のエラー (ページの読み込みが遅いなど) については電子メールで事前に通知されます。  このツールでは構成を行う必要はありません。 詳細については、「[スマート検出 - パフォーマンスの異常](../application-insights/app-insights-proactive-performance-diagnostics.md)」と[スマート検出によるパフォーマンスの異常](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview)に関するブログを参照してください。



## <a name="next-steps"></a>次のステップ
項目ごとに詳しい情報を確認できます。

* [Azure Monitor の紹介ビデオ (Ignite 2016 より)](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor の概要](monitoring-get-started.md)
* [Azure 診断](../azure-diagnostics.md) (クラウド サービス、仮想マシン、仮想マシン スケール セット、または Service Fabric アプリケーションの問題を診断する場合)。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) と [Operations Management Suite](https://www.microsoft.com/oms/) の運用監視ソリューション
