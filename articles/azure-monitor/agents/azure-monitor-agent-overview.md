---
title: Azure Monitor エージェントの概要
description: 仮想マシンのゲスト オペレーティング システムから監視データを収集する Azure Monitor エージェント (AMA) の概要。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 0487e5bd8109c02605e785e74d45589dd5fde5b9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039599"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor エージェントの概要 (プレビュー)
Azure Monitor エージェント (AMA) では、仮想マシンのゲスト オペレーティング システムから監視データが収集され、それが Azure Monitor に配信されます。 この記事では、Azure Monitor エージェントのインストール方法やデータ収集の構成方法など、Azure Monitor エージェントの概要について説明します。

## <a name="relationship-to-other-agents"></a>他のエージェントとの関係
Azure Monitor エージェントでは、Azure Monitor によって現在使用されている次のエージェントが置き換えられ、仮想マシンからゲスト データが収集されます。

- [Log Analytics エージェント](./log-analytics-agent.md) - データが Log Analytics ワークスペースに送信され、VM insights ソリューションと監視ソリューションがサポートされます。
- [Diagnostics 拡張機能](./diagnostics-extension-overview.md) - Azure Monitor メトリック (Windows のみ)、Azure Event Hubs、および Azure Storage にデータが送信されます。
- [Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md) - Azure Monitor メトリックにデータが送信されます (Linux のみ)。

Azure Monitor エージェントでは、この機能が 1 つのエージェントに統合されるだけでなく、既存のエージェントに勝る次の利点があります。

- 監視の範囲。 さまざまな VM セットからのさまざまなデータ セットのコレクションが一元的に構成されます。  
- Linux マルチホーム:Linux VM から複数のワークスペースにデータが送信されます。
- Windows イベント フィルター:XPATH クエリを使用して、収集される Windows イベントがフィルター処理されます。
- 拡張機能管理の強化:Azure Monitor エージェントでは、現在の Log Analytics エージェントの管理パックや Linux プラグインよりも透過的で制御可能な、拡張性を処理する新しい方法を使用しています。

### <a name="changes-in-data-collection"></a>データ収集の変更
既存のエージェントのデータ収集を定義する方法は、それぞれ明確に異なっており、それぞれ Azure Monitor エージェントによって対処される課題を抱えています。

- Log Analytics エージェントでは、Log Analytics ワークスペースからその構成が取得されます。 これを一元的に構成することは簡単ですが、異なる仮想マシンに個別の定義を定義するのは困難です。 データは、Log Analytics ワークスペースにのみ送信できます。

- 診断拡張機能では、仮想マシンごとに構成を指定します。 これにより、異なる仮想マシンに個別の定義を簡単に定義できますが、一元的に管理することは困難です。 Azure Monitor メトリック、Azure Event Hubs、または Azure Storage にのみデータを送信できます。 Linux エージェントの場合、Azure Monitor メトリックにデータを送信するために、オープン ソースの Telegraf エージェントが必要です。

Azure Monitor エージェントでは、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用して、各エージェントから収集するデータが構成されます。 データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースから独立し、仮想マシンからも独立しているため、一度定義すると、コンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](data-collection-rule-azure-monitor-agent.md)」をご覧ください。

## <a name="should-i-switch-to-azure-monitor-agent"></a>Azure Monitor エージェントに切り替える必要はありますか?
Azure Monitor エージェントは、[Azure Monitor 用の一般提供されているエージェントと共存できます](agents-overview.md)が、Azure Monitor エージェントのパブリック プレビュー段階では、VM を現在のエージェントから移行することを検討することができます。 この決定を行う際には、以下の点を考慮してください。

- **環境要件。** Azure Monitor エージェントは、サポートされているオペレーティング システム、環境、およびネットワーク要件のセットが現在のエージェントよりも制限されています。 新しいオペレーティング システムのバージョンやネットワーク要件の種類など、今後の環境サポートは、Azure Monitor エージェントでのみ提供される可能性が高くなります。 お使いの環境が Azure Monitor エージェントでサポートされているかどうかを評価することをお勧めします。 そうでない場合は、現在のエージェントのままにする必要があります。 Azure Monitor エージェントが現在の環境をサポートしている場合は、その環境への移行を検討することをお勧めします。
- **パブリック プレビューのリスク許容範囲。** Azure Monitor エージェントは、現在サポートされているシナリオで十分にテストされていますが、エージェントはまだパブリック プレビュー段階です。 バージョンの更新と機能の改善は頻繁に行われ、バグが発生する可能性があります。 VM 上でデータ収集が停止する可能性があるエージェントのバグのリスクを評価することをお勧めします。 データ収集の欠落がサービスに大きな影響を与えない場合は、Azure Monitor エージェントに移行してください。 不安定さに対する許容度が低い場合は、一般提供されているエージェントを引き続き使用し、Azure Monitor エージェントがそのレベルに達するまで待つことをお勧めします。
- **現在と新規の機能要件。** Azure Monitor エージェントでは、フィルター処理、スコープ、マルチホームなど、いくつかの新機能を導入していますが、カスタム ログ収集やソリューションとの統合など、他の機能については、まだ現在のエージェントと同等ではありません。 Azure Monitor のほとんどの新機能は、Azure Monitor エージェントでのみ使用できるようになるため、時間の経過と共に、新しいエージェントでのみ使用できる機能が増えます。 Azure Monitor エージェントに必要な機能があるかどうか、また新しいエージェントで他の重要な機能を利用せずに一時的に実行できる機能があるかどうかを検討することをお勧めします。 必要なすべてのコア機能が Azure Monitor エージェントにある場合は、移行することを検討してください。 必須の重要な機能がある場合は、Azure Monitor エージェントが同等になるまで、現在のエージェントを引き続き使用します。
- **やり直しの許容範囲。** デプロイ スクリプトやオンボード テンプレートなどのリソースを使用して新しい環境を設定する場合、Azure Monitor エージェントが一般提供されたときに、それらをやり直すことができるかどうかを検討することをお勧めします。 このやり直しの労力が最小限になる場合は、今のところは現在のエージェントのままにしておきます。 大量の作業が必要な場合は、新しいエージェントを使用して新しい環境を設定することを検討してください。 Azure Monitor エージェントは、2021 年に一般提供され、Log Analytics エージェントが非推奨となる日が公開される予定です。 非推奨化が始まってから数年間は、現在のエージェントはサポートされます。



## <a name="current-limitations"></a>現在の制限
Azure Monitor エージェントのパブリック プレビュー中は、次の制限事項が適用されます。

- Azure Monitor エージェントでは、VM insights や Azure Security Center などのソリューションと分析情報はサポートされません。 現在サポートされているシナリオは、構成したデータ収集ルールを使用してデータを収集することだけです。 
- データ収集ルールは、収集先として使用する Log Analytics ワークスペースと同じリージョンに作成する必要があります。
- 現在、Azure 仮想マシン、仮想マシン スケール セット、Azure Arc 対応サーバーがサポートされています。 Azure Kubernetes Service と他のコンピューティング リソースの種類は、現在サポートされていません。
- 仮想マシンは、次の HTTPS エンドポイントにアクセスできる必要があります。
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>他のエージェントとの共存
Azure Monitor エージェントは、既存のエージェントと共存させることができるため、評価または移行中にそれらの既存の機能を使用し続けることができます。 これは、既存のソリューションのサポートにおけるパブリック プレビューの制限のため、特に重要です。 ただし、重複データの収集では注意する必要があります。これにより、クエリ結果にずれが生じ、データ インジェストと保持に追加料金が発生する可能性があるためです。

たとえば、VM insights では、Log Analytics エージェントを使用して、パフォーマンス データが Log Analytics ワークスペースに送信されます。 また、エージェントから Windows イベントと Syslog イベントを収集するようにワークスペースを構成している場合もあります。 Azure Monitor エージェントをインストールし、これらの同じイベントとパフォーマンス データに対してデータ収集ルールを作成した場合、データが重複することになります。


## <a name="costs"></a>コスト
Azure Monitor エージェントには料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 Log Analytics のデータ収集と保持の詳細についてと顧客メトリックについては、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="data-sources-and-destinations"></a>データ ソースと収集先
次の表に、現在 Azure Monitor エージェントでデータ収集ルールを使用して収集できるデータの種類と、そのデータを送信できる場所を示します。 分析情報、ソリューション、および Azure Monitor エージェントを使用して他の種類のデータを収集するその他のソリューションのリストについては、「[Azure Monitor で何が監視されていますか?](../monitor-reference.md)」 を参照してください。


Azure Monitor エージェントでは、Azure Monitor メトリック、または Azure Monitor ログをサポートする Log Analytics ワークスペースにデータが送信されます。

| Data Source | 変換先 | Description |
|:---|:---|:---|
| パフォーマンス        | Azure Monitor メトリック<br>Log Analytics ワークスペース | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| Windows イベント ログ | Log Analytics ワークスペース | Windows イベント ログ システムに送信された情報。 |
| syslog             | Log Analytics ワークスペース | Linux イベント ログ システムに送信される情報。 |


## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure Monitor エージェントで現在サポートされている Windows および Linux オペレーティング システムのバージョンの一覧については、「[サポートされるオペレーティング システム](agents-overview.md#supported-operating-systems)」を参照してください。



## <a name="security"></a>セキュリティ
Azure Monitor エージェントにはキーは必要ありませんが、代わりに[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) が必要です。 エージェントをデプロイする前に、各仮想マシンで、システム割り当てマネージド ID を有効にしている必要があります。

## <a name="networking"></a>ネットワーキング
Azure Monitor エージェントでは Azure サービス タグ (AzureMonitor タグと AzureResourceManager タグの両方が必要) がサポートされますが、Azure Monitor プライベート リンク スコープや直接プロキシとはまだ連動していません。


## <a name="next-steps"></a>次のステップ

- Windows および Linux の仮想マシンに [Azure Monitor エージェント をインストール](azure-monitor-agent-install.md)します。
- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。