---
title: 'Azure Monitor を使用して仮想マシンを監視する: 監視の構成'
description: Azure Monitor で仮想マシンの監視を構成する方法について説明します。 Azure Monitor のシナリオで仮想マシンとそのワークロードを監視します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 6eb624fd132823afe25a91414c2a316f9dc7bab3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310340"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>Azure Monitor を使用して仮想マシンを監視する: 監視の構成
この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 Azure Monitor で Azure 仮想マシンとハイブリッド仮想マシンの監視を構成する方法について説明します。

この記事では、仮想マシンのホストとそのゲスト オペレーティング システムを監視するための最も一般的な Azure Monitor の機能について説明します。 特定の環境とビジネス要件によっては、この構成で有効にされるすべての機能を実装したくない場合があります。 各セクションでは、その構成で有効にされる機能と、追加コストが発生する可能性があるかどうかについて説明します。 この情報は、構成の各手順を実行するかどうかを評価するために役立ちます。 詳細な価格情報については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

この構成で有効にされる各機能の一般的な説明については、[シナリオの概要](monitor-virtual-machine.md)に関するページを参照してください。 この記事には、要件の評価に役立つ各機能の詳細な説明を提供するコンテンツへのリンクも含まれています。

> [!NOTE]
> この構成で有効にされる機能では、仮想マシンで実行されているワークロードの監視がサポートされます。 ただし、特定のワークロードによっては、多くの場合、追加の構成が必要となります。 この追加の構成の詳細については、[ワークロードの監視](monitor-virtual-machine-workloads.md)に関する記事を参照してください。

## <a name="configuration-overview"></a>構成の概要
次の表に、この構成のために実行する必要がある手順の一覧を示します。 それぞれがその構成手順を詳細に説明するセクションにリンクされています。

| 手順 | 説明 |
|:---|:---|
| [構成なし](#no-configuration) | Azure 仮想マシンのホストのアクティビティ ログとプラットフォーム メトリックは、構成なしで自動的に収集されます。 |
| [Log Analytics ワークスペースを作成して準備する](#create-and-prepare-a-log-analytics-workspace) | Log Analytics ワークスペースを作成して、VM の分析情報用に構成します。 特定の要件に応じて、複数のワークスペースを構成できます。 |
| [アクティビティ ログを Log Analytics ワークスペースに送信する](#send-an-activity-log-to-a-log-analytics-workspace) | アクティビティ ログをワークスペースに送信して、他のログ データと一緒に分析します。 |
| [ハイブリッド マシンを準備する](#prepare-hybrid-machines) | ハイブリッド マシンでは、Azure 仮想マシンのように管理できるように、Azure Arc によって有効にされたサーバー エージェントがインストールされている必要があります。インストールされていない場合は、エージェントを手動でインストールする必要があります。 |
| [マシンで VM 分析情報を有効にする](#enable-vm-insights-on-machines) | 必要なエージェントをデプロイし、ゲスト オペレーティング システムからのデータの収集を開始する VM 分析情報にマシンをオンボードします。 |
| [ゲストのパフォーマンス データをメトリックに送信する](#send-guest-performance-data-to-metrics) |Azure Monitor エージェントをインストールして、パフォーマンス データを Azure Monitor のメトリックに送信します。 |

## <a name="no-configuration"></a>構成なし
Azure Monitor では、Azure 仮想マシンに対する基本的なレベルの監視が無料で提供され、構成する必要もありません。 Azure 仮想マシンのプラットフォーム メトリックには、CPU、ネットワーク、ディスク使用率などの重要なメトリックが含まれます。 これらは、Azure portal のマシンの[概要ページ](monitor-virtual-machine-analyze.md#single-machine-experience)で表示できます。 アクティビティ ログも自動的に収集され、マシンの最近のアクティビティ (構成の変更や停止および開始された時間など) が含まれています。

## <a name="create-and-prepare-a-log-analytics-workspace"></a>Log Analytics ワークスペースを作成して準備する
VM 分析情報をサポートし、Log Analytics エージェントからテレメトリを収集するには、少なくとも 1 つの Log Analytics ワークスペースが必要です。 ワークスペースのコストは発生しませんが、データを収集するときに、取り込みと保有のコストが発生します。 詳細については、「[Azure Monitor ログで使用量とコストを管理する](../logs/manage-cost-storage.md)」を参照してください。

多くの環境では、すべての仮想マシンと監視するその他の Azure リソースのために 1 つのワークスペースが使用されます。 [Microsoft Defender for Cloud と Microsoft Sentinel](monitor-virtual-machine-security.md) で使用されるワークスペースを共有することもできますが、多くのお客様は、可用性とパフォーマンスのテレメトリをセキュリティ データから分離することを選択します。 Azure Monitor を使い始めたばかりの場合は、1 つのワークスペースから始めて、要件の発展に合わせてさらにワークスペースを作成することを検討してください。

ワークスペースの構成を設計するために検討する必要があるロジックの詳細については、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください。

### <a name="multihoming-agents"></a>エージェントをマルチホームにする
マルチホームとは、複数のワークスペースに接続する仮想マシンを指します。 通常、Azure Monitor のみでマルチホーム エージェントを使用する理由はほとんどはありません。 エージェントで複数のワークスペースにデータを送信すると、ほとんどの場合、各ワークスペースに重複するデータが作成され、全体的なコストが増加します。 複数のワークスペースのデータを結合するには、[ワークスペース間のクエリ](../logs/cross-workspace-query.md)と[ブック](../visualizations/../visualize/workbooks-overview.md)を使用します。

ただし、マルチホームを検討する理由の 1 つは、Microsoft Defender for Cloud または Microsoft Sentinel が Azure Monitor とは別のワークスペースに格納されている環境がある場合です。 各サービスによって監視されているマシンでは、各ワークスペースにデータを送信する必要があります。 Windows エージェントでは、最大 4 つのワークスペースに送信することができるため、このシナリオがサポートされます。 現在、Linux エージェントでは、1 つのワークスペースにのみ送信できます。 Azure Monitor と Microsoft Defender for Cloud または Microsoft Sentinel で Linux マシンの共通セットを監視する場合は、各サービスで同じワークスペースを共有する必要があります。

エージェントをマルチホームにするもう 1 つの理由は、[ハイブリッド監視モデル](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring)を使用している場合です。 このモデルでは、Azure Monitor と Operations Manager を使用して同じマシンを監視します。 Log Analytics エージェントと Operations Manager 用の Microsoft Management Agent は同じエージェントです。 これらは異なる名前で呼ばれることがあります。

### <a name="workspace-permissions"></a>ワークスペースのアクセス許可
ワークスペースのアクセス モードでは、さまざまなデータ セットにアクセスできるユーザーを定義します。 アクセス モードを定義し、アクセス許可を構成する方法の詳細については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](../logs/manage-access.md)」を参照してください。 Azure Monitor を使い始めたばかりの場合は、ワークスペースの作成時に既定値を受け入れ、後でアクセス許可を構成することを検討してください。

### <a name="prepare-the-workspace-for-vm-insights"></a>VM 分析情報用のワークスペースを準備する
仮想マシンの監視を有効にする前に、VM 分析情報用の各ワークスペースを準備します。 この手順では、Log Analytics エージェントからのデータ収集をサポートするために必要なソリューションをインストールします。 この構成は、ワークスペースごとに 1 回だけ行います。 他の方法に加えて、Azure portal を使用したこの構成の詳細については、「[VM insights の有効化の概要](vminsights-enable-overview.md)」を参照してください。

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>アクティビティ ログを Log Analytics ワークスペースに送信する
Azure portal で、各仮想マシンのホストについて収集されたプラットフォームのメトリックとアクティビティ ログを表示できます。 このデータを VM 分析情報と同じ Log Analytics ワークスペースに送信し、仮想マシン用に収集された他の監視データと共に分析します。 Azure サブスクリプション内のすべてのリソースに対して 1 つのアクティビティ ログが存在するため、他の Azure リソースの監視を構成したときに、このタスクを既に実行している可能性があります。

アクティビティ ログ データの取り込みまたは保有にはコストはかかりません。 アクティビティ ログを Log Analytics ワークスペースに送信する診断設定を作成する方法の詳細については、「[診断設定の作成](../essentials/diagnostic-settings.md)」を参照してください。

### <a name="network-requirements"></a>ネットワークの要件
Linux および Windows 用の Log Analytics エージェントでは、TCP ポート 443 を介して Azure Monitor サービスへのアウトバウンド通信を行います。 Dependency Agent では、すべての通信に Log Analytics エージェントが使用されるため、別のポートを必要としません。 ファイアウォールとプロキシを構成する方法の詳細については、「[ネットワーク要件](../agents/log-analytics-agent.md#network-requirements)」 を参照してください。

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="ネットワークを示す図。" lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>Gateway
Log Analytics ゲートウェイを使用すると、1 つのゲートウェイを介してオンプレミスのマシンからの通信をチャネル化できます。 ただし、Azure Arc 対応サーバーのエージェントを Log Analytics ゲートウェイと使用することはできません。 セキュリティ ポリシーでゲートウェイが必要な場合は、オンプレミスのマシンのエージェントを手動でインストールする必要があります。 Log Analytics ゲートウェイを構成して使用する方法の詳細については、「[Log Analytics ゲートウェイ](../agents/gateway.md)」を参照してください。

### <a name="azure-private-link"></a>Azure Private Link
Azure Private Link を使用すると、Log Analytics ワークスペースのプライベート エンドポイントを作成できます。 これが構成されたら、ワークスペースへの接続は、このプライベート エンドポイントを介して行う必要があります。 Private Link は DNS のオーバーライドを使用して機能するため、個々のエージェントに構成要件はありません。 Private Link の詳細については、「[Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](../logs/private-link-security.md)」を参照してください。

## <a name="prepare-hybrid-machines"></a>ハイブリッド マシンを準備する
ハイブリッド マシンとは、Azure で実行されていないマシンです。 これは、別のクラウドまたはホストされているプロバイダーで実行されている仮想マシン、あるいは会社のデータセンター内のオンプレミスで実行されている仮想マシンまたは物理マシンです。 ハイブリッド マシンで [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)を使用すると、Azure 仮想マシンと同様に管理できます。 Azure Monitor で VM 分析情報を使用して、Azure 仮想マシンの場合と同じプロセスを使用し、Azure Arc 対応サーバーの監視を有効にすることができます。 Azure 用のハイブリッド マシンの準備に関する完全なガイドについては、「[Azure Arc 対応サーバーの計画およびデプロイ](../../azure-arc/servers/plan-at-scale-deployment.md)」を参照してください。 このタスクには、個々のマシンを有効にすることと、[Azure Policy](../../governance/policy/overview.md) を使用して大規模なハイブリッド環境全体を有効にすることが含まれます。

Azure Arc 対応サーバーに対してさらにコストが発生することはありませんが、有効にするさまざまなオプションに対してコストが発生することがあります。 詳細については、「[Azure Arc の価格](https://azure.microsoft.com/pricing/details/azure-arc/)」を参照してください。 ハイブリッド マシンが VM 分析情報に対して有効にされた後にワークスペースで収集されるデータには、コストが発生します。

### <a name="machines-that-cant-use-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーを使用できないマシン
次の条件に一致するハイブリッド マシンがある場合、それらのマシンでは Azure Arc 対応サーバーを使用できません。

- マシンのオペレーティング システムは、Azure Arc によって有効にされているサーバー エージェントでサポートされていない。詳細については、「[サポートされるオペレーティング システム](../../azure-arc/servers/agent-overview.md#prerequisites)」を参照してください。
- ご使用のセキュリティ ポリシーでは、マシンを Azure に直接接続できない。 Log Analytics エージェントでは、Azure Arc 対応サーバーがインストールされているかどうかにかかわらず、[Log Analytics ゲートウェイ](../agents/gateway.md)を使用できます。 Azure Arc によって有効にされているサーバー エージェントは、Azure に直接接続される必要があります。

これらのマシンは Azure Monitor を使用して引き続き監視できますが、エージェントを手動でインストールする必要があります。 これらのハイブリッド マシンに Log Analytics エージェントと Dependency Agent を手動でインストールするには、「[ハイブリッド仮想マシンで VM 分析情報を有効にする](vminsights-enable-hybrid.md)」を参照してください。

> [!NOTE]
> 現在、Azure Arc 対応サーバーのプライベート エンドポイントは、パブリック プレビューの段階にあります。 このエンドポイントを使用すると、仮想ネットワークのプライベート IP アドレスを使用して、ハイブリッド マシンを Azure に安全に接続できます。

## <a name="enable-vm-insights-on-machines"></a>マシンで VM 分析情報を有効にする
マシンで VM 分析情報を有効にすると、Log Analytics エージェントと Dependency Agent がインストールされ、ワークスペースに接続されて、パフォーマンス データの収集が開始されます。 パフォーマンス ビューとブックの使用を開始して、さまざまなゲスト オペレーティング システムのメトリックの傾向を分析し、VM 分析情報のマップ機能を有効にして実行中のプロセスとマシン間の依存関係を分析して、さまざまなアラート ルールを作成するために必要なデータを収集することができます。

Azure 仮想マシンと Azure Arc 対応サーバーで同じ方法を使用して、個々のマシンで VM 分析情報を有効にすることができます。 これらの方法には、Azure portal または Azure Resource Manager テンプレートを使用した個々のマシンのオンボードや、Azure Policy を使用した大規模なマシンの有効化が含まれます。 VM 分析情報には直接コストはかかりませんが、Log Analytics ワークスペースで収集されたデータの取り込みと保有にはコストがかかります。

マシンで VM 分析情報を有効にするためのさまざまなオプションについては、「[VM insights の有効化の概要](vminsights-enable-overview.md)」を参照してください。 新しいマシンの作成時に VM 分析情報を自動的に有効にするポリシーを作成するには、[「Azure Policy を使用して VM insights を有効にする](vminsights-enable-policy.md)」を参照してください。


## <a name="send-guest-performance-data-to-metrics"></a>ゲストのパフォーマンス データをメトリックに送信する
[Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)で Azure Monitor、Microsoft Defender for Cloud、および Microsoft Sentinel が完全にサポートされている場合、このエージェントによって Log Analytics エージェントが置き換えられます。 それまでは、Log Analytics エージェントと一緒にインストールして、マシンのゲスト オペレーティング システムからのパフォーマンス データを Azure Monitor のメトリックに送信できます。 この構成により、メトリックス エクスプローラーでこのデータを評価し、メトリック アラートを使用できます。

Azure Monitor エージェントには、収集するデータと、そのデータの送信先を定義する少なくとも 1 つのデータ収集ルール (DCR) が必要です。 同じリソース グループ内のすべてのマシンで 1 つの DCR を使用できます。

次のデータ ソースを使用して、監視するマシンを含むリソース グループごとに 1 つの DCR を作成します。 

- **データ ソースの種類**: パフォーマンス カウンター
- **送信先**: Azure Monitor のメトリック

Log Analytics エージェントによって既に収集されているデータと重複するので、ログにデータを送信しないように注意してください。

Azure 仮想マシンと Azure Arc 対応サーバーで同じ方法を使用して、個々のマシンに Azure Monitor エージェントをインストールできます。 これらの方法には、Azure portal または Resource Manager テンプレートを使用した個々のマシンのオンボードや、Azure Policy を使用した大規模なマシンの有効化が含まれます。 Azure Arc 対応サーバーを使用できないハイブリッド マシンの場合は、エージェントを手動でインストールします。

Azure portal を使用して、DCR を作成し、Azure Monitor エージェントを 1 つ以上のエージェントにデプロイするには、[Azure portal でのルールと関連付けの作成](../agents/data-collection-rule-azure-monitor-agent.md)に関する記事を参照してください。 その他のインストール方法については、「[Azure Monitor エージェントをインストールする](../agents/azure-monitor-agent-install.md)」で説明されています。 エージェントと DCR を作成時に新しいマシンに自動的にデプロイするポリシーを作成するには、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](../best-practices.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [仮想マシン用に収集された監視データを分析する](monitor-virtual-machine-analyze.md)
* [収集されたデータからアラートを作成する](monitor-virtual-machine-alerts.md)
* [仮想マシンで実行されているワークロードを監視する](monitor-virtual-machine-workloads.md)
