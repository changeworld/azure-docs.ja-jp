---
title: Azure Functions のベスト プラクティス
description: Azure で実行される効率的な関数コードを設計、デプロイ、維持するためのベスト プラクティスについて説明します。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 620d3c27ae9f1df0b927119143a42e7f83730962
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715418"
---
# <a name="best-practices-for-reliable-azure-functions"></a>信頼性の高い Azure Functions のためのベスト プラクティス

Azure Functions は、Azure、サードパーティのサービス、オンプレミスのシステムで発生するイベントによってトリガーされるコードを実装する機能により、既存の Azure App Service アプリケーション プラットフォームを拡張する、イベント ドリブンのオンデマンド コンピューティング エクスペリエンスです。 Functions を使用すると、データ ソースまたはメッセージング ソリューションに接続することによってソリューションを構築でき、イベントの処理と対応が容易になります。 Functions が実行される Azure データ センターは、多くの統合コンポーネントを備えた複雑なものです。 ホストされたクラウド環境では、VM が再起動または移動されたりする場合があり、システムのアップグレードが行われます。 関数アプリは、外部 API、Azure サービス、他のデータベースにも依存している可能性が高く、これらも定期的に信頼できなくなることがあります。 

この記事では、クラウドベースの環境で正常な状態を維持し、パフォーマンスが高い効率的な関数アプリを、設計してデプロイするためのベスト プラクティスについて説明します。

## <a name="choose-the-correct-hosting-plan"></a>適切なホスティング プランを選択する 

Azure で Function App を作成するときは、アプリのホスティング プランを選択する必要があります。 選択するプランは、パフォーマンス、信頼性、コストに影響します。 Functions では 3 つの基本的なホスティング プランを利用できます。 

+ [従量課金プラン](consumption-plan.md)
+ [Premium プラン](functions-premium-plan.md)
+ [専用 (App Service) プラン](dedicated-plan.md)

Linux または Windows を実行する場合、すべてのホスティング プランが一般提供 (GA) されます。

App Service プラットフォームのコンテキストでは、関数を動的にホストするために使用される Premium プランは、エラスティック Premium プラン (EP) です。 他にも、Premium と呼ばれる専用 (App Service) プランがあります。 詳細については、[Premium プラン](functions-premium-plan.md)に関する記事を参照してください。

選択するホスティング プランによって、次の動作が決まります。

+   需要に基づいて関数アプリがスケーリングされる方法と、インスタンスの割り当ての管理方法。
+   各 Function App  インスタンスに利用できるリソース。
+   Azure Virtual Network 接続などの高度な機能のサポート。

正しいホスティング プランの選択に関する詳細と、プラン間の詳細な比較については、「[Azure Functions のホスティング オプション](functions-scale.md)」参照してください。

関数アプリを作成するときに、正しいプランを選択することが重要です。 Functions では、ホスティング プランを切り替える機能 (主に、従量課金とエラスティック Premium プランの間) が制限されています。 詳細については、「[プランの移行](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration)」を参照してください。 

## <a name="configure-storage-correctly"></a>ストレージを正しく構成する

Functions では、ストレージ アカウントが関数アプリと関連付けられている必要があります。 ストレージ アカウントの接続は、トリガーの管理や関数の実行のログ記録などの操作のために、Functions ホストによって使用されます。 関数アプリを動的にスケーリングするときにも使用されます。 詳細については、「[Azure Functions のストレージに関する考慮事項](storage-considerations.md)」を参照してください。

関数アプリでファイル システムまたはストレージ アカウントが正しく構成されていないと、関数のパフォーマンスと可用性に影響する可能性があります。 正しく構成されていないストレージ アカウントのトラブルシューティングについては、[ストレージのトラブルシューティング](functions-recover-storage-account.md)に関する記事を参照してください。 

### <a name="storage-connection-settings"></a>ストレージの接続の設定

動的にスケーリングする関数アプリは、ストレージ アカウントの Azure Files エンドポイントから、またはスケールアウトされるインスタンスに関連付けられているファイル サーバーから実行できます。 この動作は、次のアプリケーション設定によって制御されます。

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

これらの設定は、Premium プランまたは Windows の従量課金プランで実行している場合にのみサポートされます。

Azure portal で、または Azure CLI や Azure PowerShell を使用して、関数アプリを作成すると、必要に応じて関数アプリ用にこれらの設定が作成されます。 Azure Resource Manager テンプレート (ARM テンプレート) からリソースを作成するときは、テンプレートに `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` も含める必要があります。 

ARM テンプレートを使用して初めてデプロイするときは、`WEBSITE_CONTENTSHARE` を含めないでください。自動的に生成されます。   

次の ARM テンプレートの例を使用すると、これらの設定を正しく構成できます。

+ [従量課金プラン](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [専用プラン](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [VNET 統合を使用する Premium プラン](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [デプロイ スロットを使用する従量課金プラン](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>Storage account configuration

Function App を作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 Functions は、トリガーの管理や関数実行のログ記録などの操作を Azure Storage に依存しています。 関数アプリ用のストレージ アカウントの接続文字列は、`AzureWebJobsStorage` と `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` のアプリケーション設定にあります。

このストレージ アカウントを作成するときは、次の点に注意してください。 

+ 待機時間を短縮するには、ストレージ アカウントは関数アプリと同じリージョンに作成します。

+ 運用環境でのパフォーマンスを向上させるには、関数アプリごとに個別のストレージ アカウントを使用します。 これは、Durable Functions とイベント ハブによってトリガーされる関数に特に当てはまります。 

+ イベント ハブによってトリガーされる関数の場合は、[Data Lake Storage が有効になっている](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81)アカウントを使用しないでください。

### <a name="handling-large-data-sets"></a>大きなデータ セットの処理

Linux で実行する場合は、ファイル共有をマウントすることで、ストレージをさらに追加できます。 共有のマウントは、関数で大規模な既存のデータ セットを処理するのに便利な方法です。 詳細については、「[ファイル共有をマウントする](storage-considerations.md#mount-file-shares)」を参照してください。

## <a name="organize-your-functions"></a>関数を整理する 

ソリューションの一部として、複数の関数を開発して公開することがあります。 このような関数は、多くの場合、1 つの関数アプリに結合されますが、別々の関数アプリ内で実行することもできます。 Premium および専用 (App Service) ホスティング プランでは、同じプランで実行することにより、複数の関数アプリで同じリソースを共有することもできます。 関数と関数アプリをグループ化する方法は、ソリューション全体のパフォーマンス、スケーリング、構成、デプロイ、セキュリティに影響する場合があります。 

従量課金と Premium プランでは、関数アプリ内のすべての関数がまとめて動的にスケーリングされます。

関数を整理する方法の詳細については、「[関数編成のベスト プラクティス](performance-reliability.md#function-organization-best-practices)」を参照してください。

## <a name="optimize-deployments"></a>デプロイを最適化する

関数アプリをデプロイするときは、Azure での関数のデプロイ単位が関数アプリである点に気を付けることが重要です。 関数アプリ内のすべての関数が、通常は同じデプロイ パッケージから、同時にデプロイされます。  

デプロイを成功させるには、次のオプションを検討してください。

+  デプロイ パッケージから関数を実行します。 このような[パッケージから実行する方法](run-functions-from-deployment-package.md)には、次の利点があります。

    + ファイル コピー ロック問題のリスクを軽減します。 
    + 再起動をトリガーする運用アプリに直接デプロイできます。 
    + パッケージ内のすべてのファイルがアプリで使用できることがわかっています。 
    + ARM テンプレートのデプロイのパフォーマンスが向上します。
    + 特に大規模な npm パッケージのツリーの JavaScript 関数の場合、コールド スタート時間を減らすことができます。

+ [継続的配置](functions-continuous-deployment.md)を使用して、デプロイをソース管理ソリューションに接続することを検討します。 継続的配置を使用すると、デプロイ パッケージから実行することもできます。

+ [Premium プランのホスティング](functions-premium-plan.md)の場合は、ウォームアップ トリガーを追加して、新しいインスタンスが追加されるときの待機時間を短縮することを検討します。 詳細については、「[Azure Functions のウォームアップ トリガー](functions-bindings-warmup.md)」を参照してください。 

+ デプロイのダウンタイムを最小限に抑え、デプロイをロールバックするには、デプロイ スロットの使用を検討してください。 詳細については、「[Azure Functions デプロイ スロット](functions-deployment-slots.md)」を参照してください。

## <a name="write-robust-functions"></a>堅牢な関数を記述する

関数のコードを記述するときは、いくつかの設計原則に従うと、関数の一般的なパフォーマンスと可用性に役立ちます。 これらの原則は次のとおりです。
 
+ [実行時間の長い関数を使用しない。](performance-reliability.md#avoid-long-running-functions) 
+ [関数間の通信を計画する。](performance-reliability.md#cross-function-communication) 
+ [ステートレスな関数を記述する。](performance-reliability.md#write-functions-to-be-stateless)
+ [防御的な関数を記述する。](performance-reliability.md#write-defensive-functions)

クラウド コンピューティングでは一時的な障害は一般的なことなので、クラウドベースのリソースにアクセスするときは、[再試行パターン](/azure/architecture/patterns/retry)を使用する必要があります。 多くのトリガーとバインドには、再試行が既に実装されています。 

## <a name="design-for-security"></a>セキュリティ向けの設計

セキュリティについての検討は、関数の準備ができた後ではなく、計画フェーズの間に行うのが最善です。 関数を安全に開発してデプロイする方法については、「[Azure Functions のセキュリティ保護](security-concepts.md)」を参照してください。  

## <a name="consider-concurrency"></a>コンカレンシーを検討する

着信イベントの結果として関数アプリの需要が増えると、従量課金と Premium プランで実行されている関数アプリはスケールアウトされます。関数アプリが負荷に応答する方法と、着信イベントを処理するようにトリガーを構成する方法を理解しておくことが重要です。 一般的な概要については、「[Azure Functions でのイベント ドリブン スケーリング](event-driven-scaling.md)」を参照してください。

専用 (App Service) プランでは、ユーザーが関数アプリのスケールアウトに対応する必要があります。 

### <a name="worker-process-count"></a>ワーカー プロセスの数

場合によっては、スケールアウトの前に、言語ワーカー プロセスと呼ばれる複数のプロセスをインスタンスに作成することで、負荷を処理する方が効率的です。許可される言語ワーカー プロセスの最大数は、[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) の設定によって制御されます。 この設定の既定値は `1` で、複数のプロセスが使用されないことを意味します。 プロセスの最大数に達すると、関数アプリはスケールアウトされ、負荷を処理するためにより多くのインスタンスが作成されます。 この設定は、ホスト プロセスで実行される [C# クラス ライブラリ関数](functions-dotnet-class-library.md)には適用されません。

Premium プランまたは専用 (App Service) プランで `FUNCTIONS_WORKER_PROCESS_COUNT` を使用する場合は、プランによって提供されるコアの数に注意してください。 たとえば、Premium プラン `EP2` では 2 つのコアが提供されるので、値 `2` から開始し、必要に応じて最大値になるまで 2 つずつ増やす必要があります。

### <a name="trigger-configuration"></a>トリガーの構成

スループットとスケーリングを計画するときは、さまざまな種類のトリガーによってイベントが処理される方法を理解しておくことが重要です。 一部のトリガーでは、バッチ処理の動作を制御したり、コンカレンシーを管理したりできます。 多くの場合、これらのオプションの値を調整することで、呼び出された関数の要求に合わせて各インスタンスを適切にスケールできます。 これらの構成オプションは、関数アプリ内のすべてのトリガーに適用され、アプリの host.json ファイルに保持されます。 設定の詳細については、特定のトリガーのリファレンスの構成セクションを参照してください。

Functions によるメッセージ ストリームの処理方法の詳細については、「[Azure Functions の信頼性の高いイベント処理](functions-reliable-event-processing.md)」を参照してください。

### <a name="plan-for-connections"></a>接続を計画する

[従量課金プラン](consumption-plan.md)で実行される関数アプリには、接続の制限が適用されます。 これらの制限は、インスタンスごとに適用されます。 これらの制限のため、および一般的なベスト プラクティスとして、関数コードからのアウトバウンド接続を最適化する必要があります。 詳細については、「[Azure Functions での接続の管理](manage-connections.md)」を参照してください。 

### <a name="language-specific-considerations"></a>言語固有の考慮事項

選択した言語については、次の点に注意してください。

# <a name="c"></a>[C#](#tab/csharp)

+ [非同期コードを使用し、呼び出しがブロックされないようにします](performance-reliability.md#use-async-code-but-avoid-blocking-calls)。

+ [キャンセル トークンを使用します](functions-dotnet-class-library.md?#cancellation-tokens) (インプロセスのみ)。

# <a name="java"></a>[Java](#tab/java)

+ CPU バインドと IO バインドの操作が混在するアプリケーションでは、[より多くのワーカー プロセス](functions-app-settings.md#functions_worker_process_count)を使用することを検討します。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [`async` と `await` を使用します](functions-reference-node.md#use-async-and-await)。

+ [CPU バインドのアプリケーションには、複数のワーカー プロセスを使用します](functions-reference-node.md?tabs=v2#scaling-and-concurrency)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [コンカレンシーに関する考慮事項を確認します](functions-reference-powershell.md#concurrency)。

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions で Python アプリのスループット パフォーマンスを向上させる](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>可用性を最大にする

サーバーレス アーキテクチャでは、コールド スタートが重要な考慮事項です。 詳細については、「[コールド スタート](event-driven-scaling.md#cold-start)」を参照してください。 シナリオでコールド スタートが問題になる場合、詳細については「[サーバーレスのコールド スタートについて](https://azure.microsoft.com/blog/understanding-serverless-cold-start/)」を参照してください。 

動的なスケーリングを維持しながら、コールド スタートを減らすには、Premium プランが推奨されるプランです。 次のガイダンスに従うと、3 つのホスティング プランすべてで、コールド スタートを減らし、可用性を向上させることができます。 

| 計画 | ガイダンス |
| --- | --- | 
| **Premium プラン** | • [関数アプリでウォームアップ トリガーを実装します](functions-bindings-warmup.md)<br/>• [常時使用可能なインスタンス数と最大バースト制限の値を設定します](functions-premium-plan.md#plan-and-sku-settings)<br/>• [仮想ネットワークで HTTP 以外のトリガーを使用する場合は、仮想ネットワーク トリガーのサポートを使用します](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **専用プラン** | • [Azure App Service の正常性チェックが有効になっている 2 つ以上のインスタンスで実行します](../app-service/monitor-instances-health-check.md)<br/>• [自動スケーリングを実装します](/azure/architecture/best-practices/auto-scaling)|
| **従量課金プラン** | • バインドとトリガーについてシングルトン パターンとコンカレンシー設定の使用を確認し、関数アプリのスケーリング方法に人為的な制限を設けないようにします。<br/>• [スケールアウトを制限する可能性のある `functionAppScaleLimit` の設定を確認します](event-driven-scaling.md#limit-scale-out)<br/>• 開発とテストの間に設定された日ごとの使用量クォータ (GB 秒) の上限を確認します。 運用環境では、この制限を削除することを検討します。 |

## <a name="monitor-effectively"></a>効率的に監視する

Azure Functions には、関数の実行やコードから書き込まれたトレースを監視するための、Azure Application Insights との統合が組み込まれています。 詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。 Azure Monitor には、関数アプリ自体の正常性を監視する機能も用意されています。 詳細については、「[Azure Functions と共に Azure Monitor メトリックを使用する](monitor-metrics.md)」を参照してください。

Application Insights 統合を使用して関数を監視する場合は、次の点に注意する必要があります。

+ [AzureWebJobsDashboard](functions-app-settings.md#azurewebjobsdashboard) のアプリケーション設定が削除されていることを確認します。 これは、古いバージョンの Functions でサポートされていた設定です。 `AzureWebJobsDashboard` が存在する場合、それを削除すると関数のパフォーマンスが向上します。 

+  [Application Insights のログ](analyze-telemetry-data.md)を確認します。 検出しようとしているデータが見つからない場合は、サンプリング設定を調整し、監視シナリオをより適切にキャプチャすることを検討します。 設定 `excludedTypes` を使用すると、`Request` や `Exception` などの特定の種類をサンプリングから除外できます。 詳細については、「[サンプリングを構成する](configure-monitoring.md?tabs=v2#configure-sampling)」を参照してください。

Azure Functions では、[システム生成とユーザー生成のログを Azure Monitor ログに送信する](functions-monitor-log-analytics.md)こともできます。 Azure Monitor ログとの統合は、現在、プレビュー段階です。 

## <a name="build-in-redundancy"></a>冗長性があるように構築する

ビジネス ニーズによっては、データ センターの停止中でも、関数を常に使用できるようにすることが必要になる場合があります。 重要な関数を常に実行させておくための、複数リージョンのアプローチの使用方法については、[Azure Functions の geo ディザスター リカバリーと高可用性](functions-geo-disaster-recovery.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[お使いの Function App の管理](functions-how-to-use-azure-function-app-settings.md)
