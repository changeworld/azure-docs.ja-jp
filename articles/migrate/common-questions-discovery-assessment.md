---
title: よく寄せられる質問 - Azure Migrate での検出、評価、および依存関係分析
description: Azure Migrate での検出、評価、および依存関係分析に関してよく寄せられる質問への回答を取得します。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2ae534e50cbee3ebd17d703f957aa7200460dbfe
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426294"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>検出、評価、および依存関係分析に関してよく寄せられる質問

この記事では、Azure Migrate の検出、評価、および依存関係分析に関してよく寄せられる質問に回答します。 その他の質問については、次の記事を確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)。
- Azure Migrate アプライアンスに関する[一般的な質問](common-questions-appliance.md)。
- サーバー移行に関する[質問](common-questions-server-migration.md)
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に質問を投稿してください。



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>1 つのアプライアンスで検出できる VM の数を教えてください？

1 つのアプライアンスで最大 10,000 までの VMware VM、最大 5,000 までの Hyper-V VM、および最大 250 までのサーバーを検出できます。 オンプレミス環境にさらに多くのマシンがある場合は、[Hyper-V](scale-hyper-v-assessment.md) 評価、[VMware](scale-vmware-assessment.md) 評価、および[物理](scale-physical-assessment.md)評価のスケーリングに関する記事を参照してください。



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>ＶＭ サイズが変更されました。 評価を再実行できますか?

Azure Migrate アプライアンスは、オンプレミス環境の情報を継続的に収集します。 ただし、評価はオンプレミス VM のポイントインタイム スナップショットです。 評価する VM の設定を変更する場合は、[再計算] オプションを使って最新の変更で評価を更新してください。

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>マルチテナント環境で VM を検出する方法はありますか？

- VMware の場合、テナント間で環境が共有されているときに、あるテナントの VM が別のテナントのサブスクリプションで検出されないようにするには、検出したい VM だけにアクセスできる vCenter Server 資格情報を作成します。 次に、Azure Migrate アプライアンスで検出を開始するときに、これらの資格情報を使用します。
- Hyper-V の場合、検出には Hyper-V ホストの資格情報が使用されます。 VM が同じ Hyper-V ホストを共有している場合、現在、検出を分離する方法はありません。  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>VMware VM を検出するために vCenter Server は必要ですか？

はい。VMware 環境で検出を実行するには Azure Migrate に vCenter Server が必要です。 vCenter Server で管理されていない ESXi ホストの検出はサポートされていません。


## <a name="whats-the-difference-sizing-options"></a>残領域サイズ変更オプションは？

オンプレミスに合わせたサイズ設定では、Azure Migrate が VM のパフォーマンス データを考慮せずに評価を行います。 オンプレミスの構成に基づいて VM のサイズを評価します。 パフォーマンスベースのサイズ設定では、使用率データに基づいてサイズが設定されます。

- たとえば、50% の CPU 使用率と 50% のメモリ使用率で 4 コアと 8 GB のメモリを備えたオンプレミスの VM の場合、次のようになります。
    - オンプレミスのサイズ設定では、4 コアで 8 GB のメモリを備えた Azure VM SKU が推奨されます。
    - パフォーマンスベースのサイズ設定では、使用率が考慮されるため、2 コアで 4 GB のメモリを備えた VM SKU が推奨されます。

- 同様に、ディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
    - サイズ設定基準がパフォーマンスベースで、ストレージの種類が自動の場合、Azure Migrate では、ターゲット ディスクの種類 (Standard または Premium) を識別するときに、ディスクの IOPS とスループットの値が考慮されます。
    - サイズ設定基準がパフォーマンスベースでストレージの種類が Premium の場合、Azure Migrate ではオンプレミスのディスクのサイズに基づき、Premium ディスク SKU が推奨されます。 オンプレミスに合わせたサイズ設定で、ストレージの種類が Standard または Premium である場合は、ディスクのサイズ設定に同じロジックが適用されます。

## <a name="does-performance-historyutilization-impact-sizing"></a>パフォーマンス履歴/使用率のサイズ変更の影響はありますか？

これらのプロパティが適用されるのは、パフォーマンス ベースのサイズ設定の場合のみです。

- Azure Migrate では、オンプレミス マシンのパフォーマンス履歴が収集され、それを使用して Azure での VM のサイズとディスクの種類が推奨されます。
- アプライアンスによりオンプレミス環境が継続的にプロファイルされて、20 秒ごとにリアルタイムの使用率データが収集されます。
- アプライアンスは、20 秒のサンプルをロールアップし、15 分ごとに 1 つのデータ ポイントを作成します。
- データ ポイントを作成するために、アプライアンスではすべての 20 秒のサンプルからピーク値が選択されます。
- このデータ ポイントは、アプライアンスから Azure に送信されます。

パフォーマンスの期間とパフォーマンス履歴のパーセンタイル値に基づいて Azure で評価を作成する場合は、Azure Migrate で有効な使用率値が計算され、その値がサイズ設定に使用されます。

- たとえば、パフォーマンスの期間を 1 日に設定し、百分位値を 95 に設定すると、Azure Migrate は、過去 1 日間にコレクターから送信された 15 分のサンプル ポイントを昇順で並べ替えて、95 番目の百分位値を有効な使用率として選択します。
- 95 パーセンタイル値を使用すると、外れ値が無視されます。 99 パーセンタイルを使用する場合は、外れ値が含まれる可能性があります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、99 パーセンタイルを選択する必要があります。

## <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化を使用すると、信頼性を高めて移行対象の VM のグループを評価できます。 依存関係の視覚化により、評価を実行する前に、マシンの依存関係がクロスチェックされます。 これにより、見落としがなくなり、Azure への移行時に予期しない障害を回避しやすくなります。 Azure Migrate では、依存関係の視覚化を実現するために Azure Monitor の Service Map ソリューションを使用します。 [詳細情報](concepts-dependency-visualization.md)。

> [!NOTE]
> 依存関係の視覚化は、Azure Government では使用できません。

## <a name="do-i-pay-for-dependency-visualization"></a>依存関係の視覚化への支払いはありますか？
いいえ。 Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

## <a name="what-do-i-install-for-dependency-visualization"></a>依存関係視覚化のためには何をインストールしますか？

依存関係の視覚化を使用するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。

次のエージェントを各マシンにインストールする必要があります。
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

依存関係の視覚化を使用している場合を除き、これらのエージェントは不要です。

## <a name="can-i-use-an-existing-workspace"></a>既存のワークスペースを使用できますか?

はい。既存のワークスペースを移行プロジェクトにアタッチして依存関係の視覚化に活用できます。 [詳細については、こちらを参照してください](concepts-dependency-visualization.md#how-does-it-work)。

## <a name="can-i-export-the-dependency-visualization-report"></a>依存関係の視覚化のレポートはエクスポートできますか。

いいえ。依存関係の視覚化はエクスポートできません。 ただし、Azure Migrate では Service Map が使用されています。[Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) を使用すると、JSON 形式で依存関係を取得できます。

## <a name="can-i-automate--mmadependency-agent-installation"></a>MMA/Dependency Ａｇｅｎｔ のインストールを自動化できますか？

こちらの[依存関係エージェントをインストールするスクリプト](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)を使用してください。 コマンド ラインまたはオートメーションを使用して [MMA をインストールするには、こちらの手順](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)に従ってください。 MMA の場合は、[このスクリプト](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)を使用します。

スクリプトのほか、デプロイ ツール (Microsoft Endpoint Configuration Manager と [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)) を利用して、エージェントをデプロイすることもできます。


## <a name="what-operating-systems-does-mma-support"></a>MMA ではどのようなオペレーティング システムがサポートされていますか?

- [MMA でサポートされる Windows オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)を参照してください。
- [MMA でサポートされる Linux オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)を参照してください。

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>1 時間以上の期間の依存関係を視覚化することはできますか。
いいえ。 依存関係を視覚化できるのは最大で 1 時間です。 履歴内で最大 1 か月前の特定の日付に戻ることができますが、視覚化の最大期間は 1 時間です。 たとえば、依存関係マップで期間を指定して昨日の依存関係を見ることはできますが、依存関係を表示できる時間枠は 1 時間だけです。 ただし、Azure Monitor ログを使用すれば、それより長い期間について[依存関係データのクエリを実行](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)できます。

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10 以上の VM が含まれるグループの依存関係を視覚化できますか？
最大 10 個の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 グループ内の VM の数が 10 を超える場合は、グループを小さなグループに分割してから依存関係を視覚化することをお勧めします。




## <a name="next-steps"></a>次のステップ
[Azure Migrate の概要](migrate-services-overview.md)を確認します。
