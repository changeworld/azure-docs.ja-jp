---
title: "Microsoft Azure での監視 | Microsoft Docs"
description: "Microsoft Azure で監視する場合の選択肢。 Azure Monitor、Application Insights、および Log Analytics"
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
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure での監視の概要
この記事では、Microsoft Azure を包括的に監視する処理に関連するツールとサービスの概要について説明します。 この記事は次の場合に適用されます。
- Azure サービスを使用して Azure インフラストラクチャとアプリケーションを監視する
- Azure サービスを使用してハイブリッドおよび Azure 以外のインフラストラクチャとアプリケーションを監視する
- Azure 以外のサービスを使用して Azure インフラストラクチャとアプリケーションを監視する

ここでは、利用可能な各種製品やサービスのほか、これらがどのように連携するかについて説明します。 これは、どのツールがどの状況で最も適しているかを判断するのに役立ちます。  

## <a name="why-use-azures-monitoring-services"></a>Azure の監視サービスを使用する理由

クラウド アプリのパフォーマンスの問題は、ビジネスに影響を及ぼす場合があります。 複数の相互接続されたコンポーネントや頻繁なリリースにより、いつでもパフォーマンスの低下が生じる可能性があります。 さらに、アプリを開発している場合、通常、テストで見つからなかった問題はユーザーによって発見されます。 このような問題について即座に把握し、問題を診断して修正するためのツールを用意しておく必要があります。 さらに、アプリケーションの問題は、アプリケーションが実行されている基礎となるインフラストラクチャが原因の場合が多いため、アプリケーションとインフラストラクチャの包括的に把握することは、Azure 環境を監視するために重要です。 Microsoft Azure には、このような問題を特定し、解決するためのさまざまなツールがあります。

## <a name="how-do-i-monitor-my-azure-environment"></a>Azure 環境を監視する方法

Azure 上で実行されるアプリケーション コードから、そのコードを実行するサービスとインフラストラクチャまで、Azure 環境を監視するさまざまなツールがあります。 これらのツールを一緒に使用することで、包括的なクラウド監視を実現できます。以下のようなツールがあります。

-   **Azure Monitor** - Azure サービスのすべてのデータ監視について、統合されたパイプラインとして機能する Azure サービス。 使用している Azure インフラストラクチャとすべての Azure サービスの操作を示すパフォーマンス メトリックとイベントにアクセスできます。 Azure Monitor は、Azure 環境用のデータ監視パイプラインです。このデータは、Log Analytics に直接提供できるだけでなく、データを分析してオンプレミスまたは他のクラウド リソースのデータと結合できるサードパーティのツールにも利用できます。

-   **Application Insights** - アプリケーション パフォーマンスの監視とユーザー分析を提供する Azure サービスです。 作成したコードと、Azure またはオンプレミス/他のクラウドにデプロイしたアプリケーションを監視します。 Application Insights SDK を使用してアプリケーションをインストルメント化することで、依存関係の応答時間、例外のトレース、デバッグのスナップショット、実行プロファイルなどの幅広いデータにアクセスできます。 アプリケーションの開発時と操作時に、このアプリケーションのテレメトリを分析できる強力なツールが用意されています。 Visual Studio と深く統合されているため、問題のコード行を特定し、修正することができます。また、使用状況の分析機能を使用して、製品マネージャー向けにユーザーのアプリケーション使用状況を分析することもできます。

-   **Log Analytics** - 旧称は OMS Log Analytics です。Azure サービス (Azure Monitor 経由)、Azure AM、オンプレミス、または他のクラウド インフラストラクチャのログ データとメトリック データを取り込み、そのデータに対して柔軟なログ検索機能とすぐに利用できる分析機能を提供する Azure サービスです。 また、多くのソース間でデータを分析するための豊富なツールを提供し、すべてのログを対象とした複雑なクエリを可能にして、指定された条件で事前に通知することができます。  カスタム データも、照会および視覚化できるように中央リポジトリに収集できます。 また、Logic Apps の組み込みのソリューションを利用して、インフラストラクチャのセキュリティと機能をすぐに洞察することもできます。

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

さらに、スマート検出ツールにより、特定の種類のエラー (ページの読み込みが遅いなど) については電子メールで事前に通知されます。  このツールでは構成を行う必要はありません。 詳細については、「[スマート検出 - パフォーマンスの異常](../application-insights/app-insights-proactive-performance-diagnostics.md)」を参照してください。



## <a name="next-steps"></a>次のステップ
項目ごとに詳しい情報を確認できます。

* [Azure Monitor の紹介ビデオ (Ignite 2016 より)](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor の概要](monitoring-get-started.md)
* [Azure 診断](../azure-diagnostics.md) (クラウド サービス、仮想マシン、仮想マシン スケール セット、または Service Fabric アプリケーションの問題を診断する場合)。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) と [Operations Management Suite](https://www.microsoft.com/oms/) の運用監視ソリューション
