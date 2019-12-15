---
title: Azure Migrate についてよく寄せられる質問
description: Azure Migrate サービスに関する一般的な質問の回答を示します。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284465"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。 この記事の内容についてさらに質問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿してください。

## <a name="general"></a>全般

### <a name="which-azure-geographies-are-supported"></a>サポートされている Azure の地域を教えてください。

[VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) および [Hyper-v Vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)で Azure Migrate がサポートされている地域を確認します。

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate と Site Recovery の違いは何ですか。

Azure Migrate は、オンプレミス マシンとワークロードの検出、評価、および Azure への移行を、管理および追跡するための一元的なハブとして機能します。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) はディザスター リカバリー ソリューションです。 Azure Migrate: Server Migration ツールは、一部のオンプレミス マシンのリフトアンドシフト移行に、いくつかのバックエンド Site Recovery 機能を使用します。

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Azure Migrate プロジェクトを削除するにはどうすればよいですか。

[こちらの手順](how-to-delete-project.md)に従って、プロジェクトを削除することができます。 Azure Migrate プロジェクトでマシンとワークロードを検出、評価、移行したときに作成された[リソースを確認](how-to-delete-project.md#created-resources)します。


## <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

### <a name="how-does-the-appliance-connect-to-azure"></a>アプライアンスはどのように Azure に接続しますか。

インターネット経由の接続、または Azure ExpressRoute とパブリック/Microsoft ピアリングを使用した接続が可能です。

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate の Server Assessment と Server Migration に必要なネットワーク接続は何ですか。

Azure Migrate が Azure と通信するために必要な URL とポートについては、[VMware](migrate-support-matrix-vmware.md) と [Hyper-V](migrate-support-matrix-hyper-v.md) のサポート マトリックスを確認してください。

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>テンプレートを使用して作成したアプライアンス VM を強化することはできますか。

Azure Migrate アプライアンスに必要な通信とファイアウォールの規則をそのままにしておく限り、コンポーネント (ウイルス対策など) をテンプレートに追加できます。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスによって収集されるデータは何ですか。

アプライアンスによって収集された次のデータを確認します。
- VMware VM の[パフォーマンス データ](migrate-appliance.md#collected-performance-data-vmware)と[メタデータ](migrate-appliance.md#collected-metadata-vmware)
- Hyper-V VM の[パフォーマンス データ](migrate-appliance.md#collected-performance-data-hyper-v)と[メタデータ](migrate-appliance.md#collected-metadata-hyper-v)


### <a name="does-appliance-analysis-impact-performance"></a>アプライアンス分析はパフォーマンスに影響しますか。

Azure Migrate アプライアンスでは、VM のパフォーマンス データを測定するために、オンプレミスのマシンが継続的にプロファイルされます。 このプロファイルは、Hyper-V/ESXi ホストや vCenter Server のパフォーマンスにほとんど影響しません。

### <a name="where-and-how-long-is-collected-data-stored"></a>収集されたデータはどこにどのくらい保存されますか。

Azure Migrate アプライアンスによって収集されたデータは、移行プロジェクトを作成するときに選択した Azure の場所に格納されます。 データは Microsoft サブスクリプションに安全に格納され、Azure Migrate プロジェクトを削除すると削除されます。

収集されたデータは、依存関係の視覚化用として、Azure サブスクリプションで作成された米国の Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。 依存関係の視覚化の[詳細を確認](concepts-dependency-visualization.md)します。

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>継続的プロファイリング時にアップロードされるデータの量はどのくらいですか。

Azure Migrate に送信されるデータの量は、いくつかのパラメーターによって変わります。 量の概要を説明すると、10 台のマシン (それぞれ 1 台のディスクと 1 つの NIC を搭載) の Azure Migrate プロジェクトからは、1 日あたり約 50 MB が送信されます。 この値は概算で、NIC とディスクのデータ ポイント数に基づいて変化します。 マシン、NIC、またはディスクの数が増える場合、送信されるデータの増加は非線形です。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>保存時と転送中のデータは暗号化されますか。

はい、両方です。
- メタデータは HTTPS を介してインターネット上で安全に Azure Migrate サービスに送信されます。
- メタデータは、Microsoft サブスクリプションの [Azure Cosmos データベース](../cosmos-db/database-encryption-at-rest.md)と [Azure Blob ストレージ](../storage/common/storage-service-encryption.md)に格納されます。 メタデータは保存時に暗号化されます。
- また、依存関係の分析用データも転送中に暗号化されます (セキュア HTTPS)。 これは、サブスクリプションの Log Analytics ワークスペースに格納されます。 また、保存時にも暗号化されます。

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>アプライアンスは vCenter Server や Azure Migrate とどのように通信しますか。

1. アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。
2. アプライアンスは VMware PowerCLI を使用して vCenter Server に照会し、vCenter Server が管理する VM に関するメタデータを収集します。 VM (コア、メモリ、ディスク、NIC) に関する構成データと、過去 1 か月間の各 VM のパフォーマンス履歴を収集されます。
3. 収集されたメタデータは、Azure Migrate:Server Assessment に送信され (インターネット経由、HTTPS)、評価されます。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>同じアプライアンスを複数の vCenter Server インスタンスに接続できますか。

No. アプライアンスと vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server インスタンス上の VM を検出する場合は、複数のアプライアンスをデプロイする必要があります。


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>マシンのサイズが変わりました。 評価を再実行することはできますか。

Azure Migrate アプライアンスは、オンプレミス環境の情報を継続的に収集します。 ただし、評価はオンプレミス VM のポイントインタイム スナップショットです。 評価する VM の設定を変更する場合は、[再計算] オプションを使って最新の変更で評価を更新してください。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>マルチテナント環境で検出を実行するにはどうすればよいですか。

- VMware の場合、テナント間で環境が共有されているときに、あるテナントの VM が別のテナントのサブスクリプションで検出されないようにするには、検出したい VM だけにアクセスできる vCenter Server 資格情報を作成します。 次に、Azure Migrate アプライアンスで検出を開始するときに、これらの資格情報を使用します。
- Hyper-V の場合、検出には Hyper-V ホストの資格情報が使用されます。 VM が同じ Hyper-V ホストを共有している場合、現在、検出を分離する方法はありません。  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>1 つのアプライアンスで検出できる VM の数を教えてください。

1 つの移行アプライアンスで最大 10,000 の VMware VM と最大 5,000 の Hyper-V VM を検出できます。 オンプレミス環境にさらに多くのマシンがある場合は、[Hyper-V](scale-hyper-v-assessment.md) と [VMware](scale-vmware-assessment.md) の評価のスケーリング方法を参照してください。

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Azure Migrate アプライアンスをプロジェクトから削除できますか。

現在、プロジェクトからアプライアンスを削除することはサポートされていません。

- アプライアンスを削除する唯一の方法は、アプライアンスに関連付けられた Azure Migrate プロジェクトを含むリソース グループを削除することです。
- ただし、リソース グループを削除すると、リソース グループ内のプロジェクトに関連付けられている他の登録済みアプライアンス、検出されたインベントリ、評価、およびその他すべての Azure コンポーネントも削除されます。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate Server Assessment


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Azure Migrate が VMware VM を検出するために vCenter Server は必要ですか。

はい。VMware 環境で検出を実行するには Azure Migrate に vCenter Server が必要です。 vCenter Server で管理されていない ESXi ホストの検出はサポートされていません。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server Assessment と MAP Toolkit の違いは何ですか。

Server Assessment は、移行準備を支援する評価と、Azure への移行のためのワークロードの評価を提供します。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) は、新しいバージョンの Windows クライアントやサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡など、他のタスクに役立ちます。 そのような目的には、引き続き MAP Toolkit を使用します。

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Server Assessment と Site Recovery Deployment Planner の違いは何ですか。

Server Assessment は移行計画ツールです。 Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

- **オンプレミスの Azure への移行計画**:オンプレミスのサーバーを Azure に移行する予定がある場合は、移行計画に Server Assessment を使用します。 オンプレミスのワークロードが評価され、移行する際に役立つガイダンス、ツールが用意されています。 移行計画の準備ができたら、Azure Migrate Server Migration などのツールを使用して、マシンを Azure に移行できます。
- **Azure へのディザスター リカバリーの計画**:Site Recovery を使用してオンプレミスから Azure へのディザスター リカバリーを設定する場合は、Site Recovery Deployment Planner を使用します。 Deployment Planner は、ディザスター リカバリーを目的として、オンプレミス環境に対する Site Recovery 独自の詳細な評価を提供します。 また、レプリケーションやフェールオーバーなどのディザスター リカバリーに関する推奨事項を提供します。

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>オンプレミスに合わせたサイズ設定とパフォーマンス ベースのサイズ設定の違いは何ですか。

オンプレミスに合わせたサイズ設定では、Azure Migrate が VM のパフォーマンス データを考慮せずに評価を行います。 オンプレミスの構成に基づいて VM のサイズを評価します。 パフォーマンスベースのサイズ設定では、使用率データに基づいてサイズが設定されます。

- たとえば、50% の CPU 使用率と 50% のメモリ使用率で 4 コアと 8 GB のメモリを備えたオンプレミスの VM の場合、次のようになります。
    - オンプレミスのサイズ設定では、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
    - パフォーマンスベースのサイズ設定では、使用率が考慮されるため、2 コアと 4 GB のメモリを備えた VM SKU を使用することをお勧めします。

- 同様に、ディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
    - サイズ設定基準がパフォーマンスベースで、ストレージの種類が自動の場合、Azure Migrate では、ターゲット ディスクの種類 (Standard または Premium) を識別するときに、ディスクの IOPS とスループットの値が考慮されます。
    - サイズ設定基準がパフォーマンスベースでストレージの種類が Premium の場合、Azure Migrate ではオンプレミスのディスクのサイズに基づき、Premium ディスク SKU が推奨されます。 オンプレミスに合わせたサイズ設定で、ストレージの種類が Standard または Premium である場合は、ディスクのサイズ設定に同じロジックが適用されます。

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>パフォーマンス履歴と使用率パーセンタイルはサイズに関する推奨事項に影響しますか。

これらのプロパティが適用されるのは、パフォーマンス ベースのサイズ設定の場合のみです。

- Azure Migrate では、オンプレミス マシンのパフォーマンス履歴が収集され、それを使用して Azure での VM のサイズとディスクの種類が推奨されます。
- アプライアンスによりオンプレミス環境が継続的にプロファイルされて、20 秒ごとにリアルタイムの使用率データが収集されます。
- アプライアンスは、20 秒のサンプルをロールアップし、15 分ごとに 1 つのデータ ポイントを作成します。
- データ ポイントを作成するために、アプライアンスではすべての 20 秒のサンプルからピーク値が選択されます。
- このデータ ポイントは、アプライアンスから Azure に送信されます。

パフォーマンスの期間とパフォーマンス履歴のパーセンタイル値に基づいて Azure で評価を作成する場合は、Azure Migrate で有効な使用率値が計算され、その値がサイズ設定に使用されます。

- たとえば、パフォーマンスの期間を 1 日に設定し、百分位値を 95 に設定すると、Azure Migrate は、過去 1 日間にコレクターから送信された 15 分のサンプル ポイントを昇順で並べ替えて、95 番目の百分位値を有効な使用率として選択します。
- 95 パーセンタイル値を使用すると、外れ値が無視されます。 99 パーセンタイルを使用する場合は、外れ値が含まれる可能性があります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、99 パーセンタイルを選択する必要があります。

## <a name="server-assessment---dependency-visualization"></a>サーバー評価 - 依存関係の視覚化


### <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化を使用すると、信頼性を高めて移行対象の VM のグループを評価できます。 依存関係の視覚化により、評価を実行する前に、マシンの依存関係がクロスチェックされます。 これにより、見落としがなくなり、Azure への移行時に予期しない障害を回避しやすくなります。 Azure Migrate では、依存関係の視覚化を実現するために Azure Monitor の Service Map ソリューションを使用します。 [詳細情報](concepts-dependency-visualization.md)。

> [!NOTE]
> 依存関係の視覚化は、Azure Government では使用できません。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>依存関係の視覚化を使用するために料金はかかりますか。
No. Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>依存関係の視覚化のために何をインストールする必要がありますか。

依存関係の視覚化を使用するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。

次のエージェントを各マシンにインストールする必要があります。
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

依存関係の視覚化を使用している場合を除き、これらのエージェントは不要です。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>依存関係の視覚化に既存のワークスペースを使用できますか。

はい。既存のワークスペースを移行プロジェクトにアタッチして依存関係の視覚化に活用できます。 [詳細情報](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>依存関係の視覚化のレポートはエクスポートできますか。

いいえ。依存関係の視覚化はエクスポートできません。 ただし、Azure Migrate では Service Map が使用されています。[Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) を使用すると、JSON 形式で依存関係を取得できます。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Microsoft Monitoring Agent (MMA) と依存関係エージェントのインストールを自動化するには、どうすればよいですか。

こちらの[依存関係エージェントをインストールするスクリプト](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)を使用してください。 コマンド ラインまたはオートメーションを使用して [MMA をインストールするには、こちらの手順](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)に従ってください。 MMA の場合は、[このスクリプト](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)を使用します。

スクリプトのほか、デプロイ ツール (System Center Configuration Manager と [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)) を利用して、エージェントをデプロイすることもできます。


### <a name="what-operating-systems-are-supported-by-mma"></a>MMA でサポートされるオペレーティング システムはどれですか。

- [MMA でサポートされる Windows オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)を参照してください。
- [MMA でサポートされる Linux オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)を参照してください。

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>1 時間以上の期間の依存関係を視覚化することはできますか。
No. 依存関係を視覚化できるのは最大で 1 時間です。 履歴内で最大 1 か月前の特定の日付に戻ることができますが、視覚化の最大期間は 1 時間です。 たとえば、依存関係マップで期間を指定して昨日の依存関係を見ることはできますが、依存関係を表示できる時間枠は 1 時間だけです。 ただし、Azure Monitor ログを使用すれば、それより長い期間について[依存関係データのクエリを実行](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)できます。

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>VM 数が 10 個を超えるグループに依存関係の視覚化を使用できますか。
最大 10 個の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 グループ内の VM の数が 10 を超える場合は、グループを小さなグループに分割してから依存関係を視覚化することをお勧めします。

## <a name="azure-migrate-server-migration"></a>Azure Migrate Server Migration

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Azure Migrate Server Migration と Site Recovery の違いは何ですか。

- オンプレミス VMware VM のエージェントレス型移行は Azure Migrate Server Migration 内でネイティブです。
- Hyper-V VM の移行、物理サーバーの移行、およびエージェント ベースの VMware VM の移行では、Azure Migrate Server Migration は Azure Site Recovery のレプリケーション エンジンを利用します。


## <a name="next-steps"></a>次の手順
[Azure Migrate の概要](migrate-services-overview.md)を確認します。
