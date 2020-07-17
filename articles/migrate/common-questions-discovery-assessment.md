---
title: Azure Migrate での検出、評価、および依存関係分析に関する質問
description: Azure Migrate での検出、評価、および依存関係分析に関してよく寄せられる質問への回答を取得します。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9374330044bcd0c0c5f2be44688c2b35760d4418
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996752"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>検出、評価、および依存関係分析 - よく寄せられる質問

この記事では、Azure Migrate の検出、評価、および依存関係分析に関してよく寄せられる質問に回答します。 その他の質問については、次のリソースを確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- [Azure Migrate アプライアンス](common-questions-appliance.md)に関する質問
- [サーバー移行](common-questions-server-migration.md) に関する質問
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問の回答を示します。


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate を使用した検出と評価は、どの地域でサポートされていますか?

[パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>1 つのアプライアンスで検出できる VM の数を教えてください？

1 つのアプライアンスを使って最大 10,000 までの VMware VM、最大 5,000 までの Hyper-V VM、および最大 250 までの物理サーバーを検出できます。 多くのマシンがある場合は、「[Hyper-v の評価のスケーリング](scale-hyper-v-assessment.md)」、「[VMware の評価のスケーリング](scale-vmware-assessment.md)」、または「[物理サーバーの評価のスケーリング](scale-physical-assessment.md)」を参照してください。

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Azure Government に一部の VM の種類が表示されない

評価と移行がサポートされている VM の種類は、Azure Government の場所で使用できるかどうかによって異なります。 Azure Government の VM の種類を[確認および比較](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)できます。


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>VM のサイズが変更しました。 評価を再実行できますか?

Azure Migrate アプライアンスは、オンプレミス環境の情報を継続的に収集します。  評価はオンプレミス VM のポイントインタイム スナップショットです。 評価する VM の設定を変更する場合は、[再計算] オプションを使って最新の変更で評価を更新してください。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>マルチテナント環境で VM を検出する方法はありますか？

- **VMware**:環境がテナント間で共有されている場合、あるテナントの VM が別のテナントのサブスクリプションで検出されないようにするには、検出したい VM だけにアクセスできる VMware vCenter Server 資格情報を作成します。 次に、Azure Migrate アプライアンスで検出を開始するときに、これらの資格情報を使用します。
- **Hyper-V**:検出には Hyper-V ホストの資格情報が使用されます。 VM が同じ Hyper-V ホストを共有している場合、現在、検出を分離する方法はありません。  

## <a name="do-i-need-vcenter-server"></a>vCenter Server は必要ですか?

はい。VMware 環境で検出を実行するには、Azure Migrate に vCenter Server が必要です。 vCenter Server で管理されていない ESXi ホストの検出は、Azure Migrate でサポートされていません。

## <a name="what-are-the-sizing-options"></a>どのようなサイズ変更オプションがありますか?

オンプレミスに合わせたサイズ設定では、Azure Migrate が VM のパフォーマンス データを考慮せずに評価を行います。 Azure Migrate は、オンプレミスの構成に基づいて VM のサイズを評価します。 パフォーマンスベースのサイズ設定では、使用率データに基づいてサイズが設定されます。

たとえば、50% の CPU 使用率と 50% のメモリ使用率で 4 コアと 8 GB のメモリを備えたオンプレミスの VM の場合は、次のようになります。
- オンプレミスのサイズ設定では、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
- パフォーマンスベースのサイズ設定では、使用率が考慮されるため、2 コアと 4 GB のメモリを備えた VM SKU を使用することをお勧めします。

同様に、ディスクのサイズ設定は、サイズ設定基準とストレージの種類に依存します。
- サイズ設定基準がパフォーマンスベースで、ストレージの種類が自動の場合、Azure Migrate では、ターゲット ディスクの種類 (Standard または Premium) を識別するときに、ディスクの IOPS とスループットの値が考慮されます。
- サイズ設定基準がパフォーマンスベースでストレージの種類が Premium の場合、Azure Migrate ではオンプレミスのディスクのサイズに基づき、Premium ディスク SKU が推奨されます。 オンプレミスに合わせたサイズ設定で、ストレージの種類が Standard または Premium である場合は、ディスクのサイズ設定に同じロジックが適用されます。

## <a name="does-performance-history-and-utilization-affect-sizing"></a>パフォーマンス履歴および使用率はサイズ変更に影響しますか?

はい。パフォーマンス履歴および使用率は Azure Migrate でのサイズ変更に影響します。

### <a name="performance-history"></a>パフォーマンス履歴

Azure Migrate では、パフォーマンスベースのサイズ変更の場合にのみ、オンプレミス マシンのパフォーマンス履歴が収集され、それを使用して Azure での VM のサイズとディスクの種類が推奨されます。

1. アプライアンスによりオンプレミス環境が継続的にプロファイルされて、20 秒ごとにリアルタイムの使用率データが収集されます
1. このアプライアンスでは、収集された 20 秒のサンプルがロールアップされ、それを使用して 15 分ごとに 1 つのデータ ポイントが作成されます。
1. データ ポイントを作成するために、アプライアンスではすべての 20 秒のサンプルからピーク値が選択されます。
1. データ ポイントは、アプライアンスから Azure に送信されます。

### <a name="utilization"></a>使用率

Azure で評価を作成する場合は、設定されているパフォーマンスの期間とパフォーマンス履歴の百分位値に応じて、Azure Migrate で有効な使用率値が計算され、その値がサイズ設定に使用されます。

たとえば、パフォーマンスの期間を 1 日に設定し、百分位値を第 95 百分位値に設定すると、Azure Migrate は、過去 1 日間にコレクターから送信された 15 分のサンプル ポイントを昇順で並べ替えます。 第 95 百分位値を有効な使用率として選択します。

第 95 百分位値を使用すると、外れ値が無視されます。 Azure Migrate で第 99 百分位を使用している場合は、外れ値が含まれる可能性があります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにするには、第 99 百分位を使用するように Azure Migrate を設定する必要があります。

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>インポートベースの評価は、探索ソースをアプライアンスとした評価とどのように異なりますか?

インポートベースの評価は、CSV ファイルを使用して Azure Migrate にインポートされたマシンで作成された評価です。 インポートには、次の 4 つのフィールドのみが必須です。サーバー名、コア、メモリ、およびオペレーティングシステム。 以下のことに注目してみてください。 
 - ブートの種類のパラメーターについてのインポートベースの評価では、準備基準はあまり厳格ではありません。 ブートの種類が指定されていない場合は、マシンのブートの種類は BIOS であると見なされ、マシンは**条件付き対応**としてマークされません。 検出ソースをアプライアンスとした評価では、ブートの種類が見つからない場合、準備は**条件付き対応**としてマークされます。 準備の計算に差異が生じる理由は、インポートベースの評価が行われるときに、移行計画の初期段階でユーザーがマシンに関する情報を完備していないことがあるためです。 
 - パフォーマンスベースのインポート評価では、ユーザーから与えられた使用率の値を使用して、適切なサイズ計算を行います。 使用率の値はユーザーが指定するので、**パフォーマンス履歴**と**百分位の使用率**のオプションは、評価プロパティで無効になっています。 検出ソースをアプライアンスとした評価では、選択した百分位値はアプライアンスによって収集されたパフォーマンス データから取得されます。

## <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化は、移行する VM のグループ評価の信頼性を高める上で役立ちます。 依存関係の視覚化により、評価を実行する前に、マシンの依存関係がクロスチェックされます。 これにより、見落としがなくなり、Azure への移行時に予期しない障害を回避しやすくなります。 Azure Migrate では、依存関係の視覚化を実現するために Azure Monitor の Service Map ソリューションを使用します。 [詳細については、こちらを参照してください](concepts-dependency-visualization.md)。

> [!NOTE]
> エージェントベースの依存関係の分析は、Azure Government では使用できません。 エージェントレスの依存関係の分析を使用できます

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>エージェントベースとエージェントレスの違いは何ですか?

エージェントレスの視覚化とエージェントベースの視覚化の違いを次の表にまとめます。

**要件** | **エージェントレス** | **エージェント ベース**
--- | --- | ---
サポート | このオプションは現在プレビュー段階であり、VMware VM でのみ使用できます。 サポートされているオペレーティング システムについては[こちらを確認してください](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)。 | 一般提供 (GA) 中。
エージェント | クロスチェックを行うマシンにエージェントをインストールする必要はありません。 | 分析するオンプレミスの各マシンにエージェントをインストールします。[Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) と[依存関係エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
前提条件 | 前提条件とデプロイの要件については[こちらを確認してください](concepts-dependency-visualization.md#agentless-analysis)。 | 前提条件とデプロイの要件については[こちらを確認してください](concepts-dependency-visualization.md#agent-based-analysis)。
Log Analytics | 不要。 | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)の [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) ソリューションを使用します。 [詳細については、こちらを参照してください](concepts-dependency-visualization.md#agent-based-analysis)。
しくみ | 依存関係の視覚化が有効になっているコンピューター上の TCP 接続データをキャプチャします。 検出後は、5 分間隔でデータが収集されます。 | マシンにインストールされている Service Map エージェントにより、TCP プロセスと、各プロセスの受信/送信接続に関するデータが収集されます。
Data | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。 | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。<br/><br/> 接続数、待機時間、データ転送に関する情報が収集され、Log Analytics クエリで使用できます。 
グラフ | 1 つのサーバーの依存関係マップを、1 時間から 30 日までの範囲で表示できます。 | 1 つのサーバーの依存関係マップ。<br/><br/> マップは 1 時間についてのみ表示できます。<br/><br/> サーバーのグループの依存関係マップ。<br/><br/> マップ ビューからグループのサーバーを追加および削除します。
データのエクスポート | 現在、表形式でダウンロードすることはできません。 | データは Log Analytics で照会できます。


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>エージェントレスの依存関係分析用にアプライアンスをデプロイする必要はありますか。

はい。[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイする必要があります。

## <a name="do-i-pay-for-dependency-visualization"></a>依存関係の視覚化への支払いはありますか？

いいえ。 [Azure Migrate の価格](https://azure.microsoft.com/pricing/details/azure-migrate/)について、詳しくはこちらを参照してください。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>エージェントベースの依存関係の視覚化のためには何をインストールしますか?

エージェントベースの依存関係の視覚化を使用するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールします。

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)
- インターネットに接続されていないマシンがある場合、それらのマシンに Log Analytics ゲートウェイをダウンロードしてインストールします。

エージェントベースの依存関係の視覚化を使用している場合にのみ、これらのエージェントが必要です。

## <a name="can-i-use-an-existing-workspace"></a>既存のワークスペースを使用できますか?

はい。エージェントベースの依存関係の視覚化の場合、既存のワークスペースを移行プロジェクトにアタッチして依存関係の視覚化に利用できます。 

## <a name="can-i-export-the-dependency-visualization-report"></a>依存関係の視覚化のレポートはエクスポートできますか。

いいえ。エージェントベースの視覚化では依存関係視覚化レポートはエクスポートできません。 ただし、Azure Migrate では Service Map が使用されています。[Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) を使用すると、JSON 形式で依存関係を取得できます。

## <a name="can-i-automate-agent-installation"></a>エージェントのインストールを自動化できますか?

エージェントベースの依存関係の視覚化の場合:

- [依存関係エージェントをインストールするスクリプト](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)を使用してください。
- MMA の場合は、[コマンドラインまたはオートメーションを使用](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)するか、[スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)を使用します。
- スクリプトのほか、デプロイ ツール (Microsoft Endpoint Configuration Manager と [Intigua](https://www.intigua.com/intigua-for-azure-migration)) を利用して、エージェントをデプロイすることもできます。

## <a name="what-operating-systems-does-mma-support"></a>MMA ではどのようなオペレーティング システムがサポートされていますか?

- [MMA でサポートする Windows オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)を参照してください。
- [MMA でサポートする Linux オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)を参照してください。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>1 時間以上にわたる依存関係を視覚化することはできますか?

エージェントベースの視覚化では、依存関係を視覚化できるのは最大で 1 時間です。 履歴内で最大 1 か月前の特定の日付に戻ることができますが、視覚化の最大期間は 1 時間です。 たとえば、依存関係マップで期間を指定して昨日の依存関係を見ることはできますが、依存関係を表示できる時間枠は 1 時間だけです。 ただし、Azure Monitor ログを使用すれば、それより長い期間について[依存関係データのクエリを実行](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)できます。

エージェントレスの視覚化では、1 時間から 30 日の範囲で、1 つのサーバーの依存関係マップを表示できます。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10 以上の VM が含まれるグループの依存関係を視覚化できますか?

最大 10 個の VM を含むグループの[依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 グループ内の VM の数が 10 を超える場合は、グループを小さなグループに分割してから依存関係を視覚化することをお勧めします。

## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。
