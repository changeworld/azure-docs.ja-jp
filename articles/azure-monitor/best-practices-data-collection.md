---
title: Azure Monitor のベスト プラクティス - データ収集を構成する
description: Azure Monitor におけるデータ収集の構成に関するガイダンスと推奨事項。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b5522cb26f7f8fe486b4b690938a23c7e75c746a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315960"
---
# <a name="azure-monitor-best-practices---configure-data-collection"></a>Azure Monitor のベスト プラクティス - データ収集を構成する
この記事は、[Azure Monitor を構成する際の推奨事項](best-practices.md)に関する記事で紹介されているシナリオの一部です。 Azure やハイブリッド アプリケーション、リソースに対して Azure Monitor の機能を有効にするために必要なデータ収集を構成する際に推奨される手順を説明しています。

> [!IMPORTANT]
> Azure Monitor の機能とその構成は、ビジネス要件と、有効にする機能のコストとのバランスによって異なります。 以下の各ステップでは、潜在的なコストがあるかどうかが示されており、作業を進める前にこれらのコストを評価する必要があります。 価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成
[Azure Monitor Logs](logs/data-platform-logs.md) を有効にするには、少なくとも 1 つの Log Analytics ワークスペースが必要です。これは、Azure リソースからのログなどのデータの収集、Azure 仮想マシンのゲスト オペレーティング システムからのデータの収集、およびほとんどの Azure Monitor 分析情報のために必要です。 Microsoft Sentinel や Microsoft Defender for Cloud などの他のサービスでも Log Analytics ワークスペースが使用され、Azure Monitor に使用するものと同じものを共有できます。 この監視をサポートするには 1 つのワークスペースから始めることができますが、複数のワークスペースを使用する場合のガイダンスについては、「[Azure Monitor ログのデプロイの設計](logs/design-logs-deployment.md)」を参照してください。

Log Analytics ワークスペースの作成にはコストがかかりませんが、そこに収集されるようにデータを構成すると、料金が発生する可能性があります。 「[Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)」をご覧ください。  

最初の Log Analytics ワークスペースの作成については、「[Azure ポータルで Log Analytics ワークスペースを作成する](logs/quick-create-workspace.md)」を参照してください アクセスの構成については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](logs/manage-access.md)」を参照してください。 ワークスペースの構成には、Resource Manager テンプレートなどのスケーラブルな方法を使用することもできますが、ほとんどの環境では、必要なワークスペースの数がごく限られているため、多くの場合、その必要はありません。

## <a name="collect-data-from-azure-resources"></a>Azure リソースからデータを収集する
Azure リソースの一部の監視は、構成を必要とせずに自動的に使用できますが、追加の監視データを収集するには構成手順を実行する必要があります。 次の表では、Azure リソースから使用可能なすべてのデータを収集するために必要な構成手順を示します。これには、Azure Monitor Metrics および Azure Monitor Logs にデータが送信されるステップが含まれています。 以下のセクションでは、各ステップについてさらに詳しく説明します。

[![Azure リソース監視をデプロイする](media/best-practices-data-collection/best-practices-azure-resources.png)](media/best-practices-data-collection/best-practices-azure-resources.png#lightbox)

### <a name="collect-tenant-and-subscription-logs"></a>テナントとサブスクリプションのログを収集する
テナントに対する [Azure Active Directory ログ](../active-directory/reports-monitoring/overview-reports.md)とサブスクリプションに対する[アクティビティ ログ](essentials/platform-logs-overview.md)は自動的に収集されますが、それらを Log Analytics ワークスペースに送信することで、Log Analytics のログ クエリを使用して、他のログ データと共にこれらのイベントを分析することができます。 これにより、Azure Active Directory ログに対してアラートを生成するための唯一の方法であるログ クエリ アラートを作成し、アクティビティ ログ アラートより複雑なロジックを提供することもできます。

アクティビティ ログをワークスペースに送信するためのコストは発生しませんが、Azure Active Directory ログに対するデータ インジェストと保持の料金が発生します。 

Log Analytics ワークスペースにログ エントリを送信するための、テナントとサブスクリプションに対する診断設定の作成については、「[Azure AD ログを Azure Monitor ログと統合する](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)」および「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](essentials/diagnostic-settings.md)」を参照してください。 




### <a name="collect-resource-logs-and-platform-metrics"></a>リソース ログとプラットフォーム メトリックを収集する
Azure のリソースでは、リソース内で実行された操作の詳細を提供する[リソース ログ](essentials/platform-logs-overview.md)が自動的に生成されます。 ただし、プラットフォーム メトリックとは異なり、リソース ログが収集されるように構成する必要があります。 それらを Log Analytics ワークスペースに送信し、Azure Monitor Logs で使用される他のデータと結合するには、診断設定を作成します。 同じ診断設定を使用して、ほとんどのリソースのプラットフォーム メトリックを、同じワークスペースに送信することもできます。これにより、ログ クエリを使用して他の収集データと共にメトリック データを分析することができます。

Log Analytics ワークスペースでリソース ログを収集するにはコストがかかるので、貴重なデータを含むログ カテゴリのみを選択してください。 すべてのカテゴリを収集すると、ほとんど価値のないデータの収集にコストが発生してしまいます。 収集対象となるカテゴリの説明と推奨事項については、各 Azure サービスの監視に関するドキュメントを参照してください。 また、ログ収集のコストの最適化の詳細については、「[Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)」を参照してください。

Azure リソース用の診断設定の作成については、「[Azure でリソース ログとメトリックを収集するための診断設定を作成する](essentials/diagnostic-settings.md#create-in-azure-portal)」を参照してください。 

診断設定は Azure リソースごとに作成する必要があるため、Azure Policy を使用して、各リソースの作成時に診断設定を自動的に作成してください。 Azure リソースの種類にはそれぞれ、診断設定に一覧表示する必要がある一意のカテゴリのセットがあります。 このため、リソースの種類ごとに別個のポリシー定義が必要です。 一部のリソースの種類には、変更せずに割り当てることができる組み込みのポリシー定義があります。 その他のリソースの種類については、カスタム定義を作成する必要があります。

特定の Azure サービスに関するポリシーの定義を作成するプロセスと診断設定を大規模に作成する方法の詳細については、「[Azure Policy を使用して大規模に作成する](essentials/diagnostic-settings.md#create-at-scale-using-azure-policy)」を参照してください。

### <a name="enable-insights"></a>分析情報を有効にする
分析情報は、特定のサービスに特化した監視エクスペリエンスを提供するものです。 使用されるデータは、既に収集されているデータ (プラットフォームのメトリックやリソース ログなど) と同じですが、きわめて重要なデータの特定と分析に役立つカスタム ブックが提供されます。 対象となるサービスのリソース ログを収集する以外、特に必要な構成はなく、ほとんどの分析情報は Azure portal から得られます。 分析情報が利用できるかどうか、また構成が必要かどうかを調べるには、各 Azure サービスの監視に関するドキュメントを参照してください。

分析情報にコストはかかりませんが、それらによって収集されるデータに対して課金される場合があります。

Azure Monitor で利用可能な分析情報とソリューションの一覧については、「[Azure Monitor によって監視される内容](monitor-reference.md)」を参照してください。 固有の構成または価格の情報については、それぞれのドキュメントを参照してください。

> [!IMPORTANT]
> 次の分析情報は他に比べてとりわけ複雑であり、その構成には、特別なガイダンスが伴います。
> 
> - [VM insights](#monitor-virtual-machines)
> - [Container insights](#monitor-containers)
> - [アプリケーションの監視](#monitor-applications)


## <a name="monitor-virtual-machines"></a>仮想マシンの監視
仮想マシンでは、他の Azure リソースと同様のデータが生成されますが、ゲスト オペレーティング システムからデータを収集するにはエージェントが必要です。 また、仮想マシン上で実行されるワークロードは多様であるため、仮想マシンには固有の監視要件があります。 Azure Monitor を使用した仮想マシンの監視に特化したシナリオについては、「[Azure Monitor を使用して Azure 仮想マシンを監視する](vm/monitor-vm-azure.md)」を参照してください。

## <a name="monitor-containers"></a>コンテナーの監視
仮想マシンから生成されるデータは他の Azure リソースと同様ですが、必要なデータを収集するには、コンテナー化されたバージョンの Log Analytics エージェントが必要です。 コンテナー化された監視環境の準備には Container insights が役立ちます。Container insights は、サード パーティのツールと連携して、AKS とそれがサポートするワークフローを包括的に監視する機能を果たします。 Azure Monitor を使用した AKS の監視に特化したシナリオについては、「[Azure Monitor のコンテナー正常性機能を使用して Azure Kubernetes Service (AKS) を監視する](../aks/monitor-aks.md?toc=/azure/azure-monitor/toc.json)」を参照してください。

## <a name="monitor-applications"></a>アプリケーションの監視
カスタムア プリケーションを監視するために Azure Monitor によって使用される [Application Insights](app/app-insights-overview.md) を、監視するアプリケーションごとに構成する必要があります。 構成プロセスは、監視対象のアプリケーションの種類と、実行する監視の種類によって異なります。 Application Insights によって収集されたデータは、機能に応じて、Azure Monitor Metrics、Azure Monitor Logs、Azure Blob Storage に格納されます。 パフォーマンス データは Azure Monitor Metrics と Azure Monitor Logs の両方に格納され、追加の構成は必要ありません。

### <a name="create-an-application-resource"></a>アプリケーション リソースを作成する
Application Insights は、クラウド ネイティブ アプリケーションとハイブリッド アプリケーションの監視を目的とした Azure Monitor の機能です。

監視するアプリケーションごとに、Application Insights でリソースを作成する必要があります。 Application Insights によって収集されたログ データは、ワークスペースベースのアプリケーションの Azure Monitor ログに格納されます。 クラシック アプリケーションのログ データは、[データの構造](logs/data-platform-logs.md#data-structure)に関するページで説明されているように、Log Analytics ワークスペースとは別に保存されます。

 アプリケーションを作成するときに、クラシックまたはワークスペース ベースのどちらを使用するかを選択する必要があります。 クラシック アプリケーションを作成するには、「[Application Insights リソースの作成](app/create-new-resource.md)」を参照してください。 ワークスペース ベースのアプリケーションを作成するには、「[ワークスペース ベースの Application Insights リソース (プレビュー)](app/create-workspace-resource.md)」を参照してください。


 複数のアプリケーションに対してそれぞれ別々のアプリケーション リソースを使用するか、それとも単一のアプリケーション リソースするかは、設計上避けて通ることのできない重要な判断です。 リソースを分ければ、コストが下がり、異なるアプリケーションからのデータの混在も防止できますが、リソースを 1 つにすれば、関連するすべてのテレメトリをまとめ、監視を単純化することができます。 この設計判断の詳しい基準については、「[デプロイする必要がある Application Insights リソースの数](app/separate-resources.md)」を参照してください。



### <a name="configure-codeless-or-code-based-monitoring"></a>コード不要の監視またはコード ベースの監視を構成する
アプリケーションの監視を有効にするには、コード不要の監視またはコード ベースの監視を使用するかどうかを決定する必要があります。 構成プロセスは、この決定と監視対象のアプリケーションの種類によって異なります。

**コード不要の監視** は、実装するのが最も簡単で、コードを開発した後で構成できます。 コードを更新する必要はありません。 アプリケーションに応じて監視を有効にする方法の詳細については、次のリソースを参照してください。

- [Azure Web Apps でホストされているアプリケーション](app/azure-web-apps.md)
- [Java アプリケーション](app/java-in-process-agent.md)
- [Azure VM または Azure 仮想マシン スケール セット上の IIS でホストされる ASP.NET アプリケーション](app/azure-vm-vmss-apps.md)
- [IIS のオンプレミスでホストされる ASP.NET アプリケーション](app/status-monitor-v2-overview.md)


**コード ベースの監視** では、さらにカスタマイズが可能で、追加のテレメトリが収集されますが、Application Insights SDK NuGet パッケージでコードに依存関係を追加する必要があります。 アプリケーションに応じて監視を有効にする方法の詳細については、次のリソースを参照してください。

- [ASP.NET アプリケーション](app/asp-net.md)
- [ASP.NET Core アプリケーション](app/asp-net-core.md)
- [.NET コンソール アプリケーション](app/console.md)
- [Java](app/java-in-process-agent.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [その他のプラットフォーム](app/platforms.md)

### <a name="configure-availability-testing"></a>可用性テストを構成する
Application Insights の可用性テストは反復的なテストであり、世界中のポイントから定期的にアプリケーションの可用性と応答性が監視されます。 単純な ping テストなら無料で作成できます。一連の Web 要求を作成してユーザー トランザクションをシミュレートすることもできますが、関連コストが発生します。 

さまざまな種類のテストの概要と作成方法の詳細については、「[任意の Web サイトの可用性を監視する](app/monitor-web-app-availability.md)」を参照してください。

### <a name="configure-profiler"></a>Profiler を構成する
Application Insights の Profiler を使用すると、.NET アプリケーションのパフォーマンス トレースが提供されます。 特定の Web 要求の処理に最も長い時間がかかる "ホットな" コード パスを特定するのに役立ちます。 Profiler を構成するプロセスは、アプリケーションの種類によって異なります。 

Profiler の構成の詳細については、「[Application Insights を使用した Azure のプロファイル運用アプリケーション](app/profiler-overview.md)」を参照してください。

### <a name="configure-snapshot-debugger"></a>スナップショット デバッガーを構成する
Application Insights のスナップショット デバッガーを使用すると、.NET アプリケーションからの例外テレメトリを監視し、スローされる上位の例外のスナップショットを収集して、運用環境の問題を診断するために必要な情報を得ることができます。 スナップショット デバッガーを構成するためのプロセスは、アプリケーションの種類によって異なります。 

スナップショット デバッガーの構成の詳細については、「[.NET アプリでの例外でのデバッグ スナップショット](app/snapshot-debugger.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- すべての Azure リソースのデータ収集を構成したら、[データの分析と視覚化](best-practices-analysis.md)に関する記事で、このデータを分析する方法を参照してください。 
