---
title: Network Performance Monitor から接続モニターに移行する
titleSuffix: Azure Network Watcher
description: Network Performance Monitor から接続モニターに移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366353"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Network Performance Monitor から接続モニターに移行する

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースへの新しいテストの追加や、新しいワークスペースでの Network Performance Monitor の有効化は実行できなくなります。 2021 年 7 月 1 日より前に作成されたテストは使い続けることができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、Network Performance Monitor から Azure Network Watcher の新しい接続モニターにテストを移行します。

Network Performance Monitor (NPM) から機能が向上した新しい接続モニターに、1 回のクリックで、ダウンタイムなしにテストを移行できます。 利点の詳細については、[接続モニター](./connection-monitor-overview.md)に関する記事を参照してください。


## <a name="key-points-to-note"></a>注意する点

この移行は、次の結果を生み出すために役立ちます。

* オンプレミスのエージェントとファイアウォールの設定はそのままで動作します。 変更の必要はありません。 Azure 仮想マシンにインストールされている Log Analytics エージェントを、[Network Watcher 拡張機能](../virtual-machines/extensions/network-watcher-windows.md)に置き換える必要があります。
* 既存のテストは、[接続モニター] > [テストグループ] > [テスト形式] にマップされます。 **[編集]** を選択すると、新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして変更したり、Azure Resource Manager 経由でテンプレートを送信したりすることができます。
* エージェントによって、Log Analytics ワークスペースとメトリックの両方にデータが送信されます。
* データの監視:
   * **Log Analytics 内のデータ**: 移行まで、データは NetworkMonitoring テーブルで NPM が構成されているワークスペースに残っています。 移行後、データは、同じワークスペース内の NetworkMonitoring テーブル、NWConnectionMonitorTestResult テーブル、および NWConnectionMonitorPathResult テーブルに移ります。 NPM でテストが無効にされた後、データは NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルにのみ格納されるようになります。
   * **ログ ベースのアラート、ダッシュボード、統合**: 新しい NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルに基づいて、クエリを手作業で編集する必要があります。 メトリックでアラートを作成し直すには、「[接続モニターによるネットワーク接続の監視](./connection-monitor-overview.md#metrics-in-azure-monitor)」を参照してください。
* ExpressRoute の監視の場合:
    * **エンド ツー エンドの損失と待機時間**: 接続モニターから電力が供給されます。監視する回線とピアリングをユーザーが構成する必要がないため、NPM よりも簡単になります。 パス内の回線が自動的に検出され、(NPM で結果が格納される LA よりも高速に) データをメトリックで使用できるようになります。 トポロジも同様に機能します。
    * **帯域幅の測定**: 帯域幅に関連するメトリックの登場により、NPM の ログ分析ベースのアプローチは、ExpressRoute のお客様向けの帯域幅の監視には効果がなくなりました。 この機能は、接続モニターでは現在使用できません。
    
## <a name="prerequisites"></a>前提条件

* お使いのサブスクリプションと Log Analytics ワークスペースのリージョンで Network Watcher を確実に有効にします。 行われていない場合、"Before you attempt migrate, please enable Network watcher extension in selection subscription and location of LA workspace selected." (移行を実行する前に、選択したサブスクリプションと、選択した LA ワークスペースの場所で Network watcher extension を有効にしてください。) というエラーが表示されます。
* Log Analytics ワークスペースとは異なるリージョン/サブスクリプションに属している Azure VM がエンドポイントとして使用されている場合、そのサブスクリプションとリージョンで Network Watcher が有効になっていることを確認します。   
* Log Analytics エージェントがインストールされている Azure 仮想マシンで、Network Watcher 拡張機能を有効にする必要があります。

## <a name="migrate-the-tests"></a>テストを移行する

Network Performance Monitor から接続モニターにテストを移行するには、次のようにします。

1. Network Watcher で、 **[Connection Monitor]\(接続モニター\)** を選択し、 **[Migrate tests from NPM]\(NPM からのテストの移行\)** タブを選択します。 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Network Performance Monitor から接続モニターにテストを移行する" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. ドロップダウン リストで、お使いのサブスクリプションとワークスペースを選択してから、移行する NPM 機能を選択します。 
1. **[インポート]** を選択してテストを移行します。
* ワークスペースで NPM が有効になっていない場合は、"No valid NPM config found" (有効な NPM 構成が見つかりませんでした) というエラーが表示されます。 
* 手順 2 で選択した機能にテストが存在しない場合、"選択したワークスペースには、<feature> 構成がありません" というエラーが表示されます。
* 有効なテストがない場合は、"Workspace selected does not have valid tests" (選択したワークスペースには有効なテストがありません) というエラーが表示されます。
* テストには、現在はアクティブでないが、過去にアクティブだった可能性のあるエージェントが含まれている場合があります。 "いくつかのテストに、アクティブではなくなったエージェントが含まれています。 非アクティブなエージェントの一覧 - {0}。 これらのエージェントは、過去に実行されていた可能性がありますが、シャットダウンされたか、今はもう実行されていません。 エージェントを有効にして、接続モニターに移行してください。 非アクティブなエージェントが含まれていないテストを移行するには、[続行] をクリックします。" というエラーが表示されます。

移行が開始されると、次の変更が行われます。 
* 新しい接続モニター リソースが作成されます。
   * リージョンとサブスクリプションごとに 1 つの接続モニターが作成されます。 オンプレミスのエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_"workspace_region_name"` という形式になります。 Azure のエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_<Azure_region_name>` という形式になります。
   * 監視データは、NPM が有効になっている Log Analytics ワークスペースと同じ場所の新しいテーブル (NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブル) に格納されるようになりました。 
   * テスト名はテスト グループ名として引き継がれます。 テストの説明は移行されません。
   * 移行元と移行先のエンドポイントが作成され、新しいテスト グループで使用されます。 オンプレミスのエージェントの場合、エンドポイントは `<workspaceName>_<FQDN of on-premises machine>` という形式になります。エージェントの説明は移行されません。
   * 宛先ポートとプローブ間隔は、`TC_<protocol>_<port>` と `TC_<protocol>_<port>_AppThresholds` というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 ICMP の場合、テスト構成の名前は `TC_<protocol>` と `TC_<protocol>_AppThresholds` になります。 成功のしきい値とその他のオプションのプロパティが設定されている場合は、それらが移行されます。設定されていない場合は空白のままになります。
   * 移行するテストに、実行されていないエージェントが含まれている場合は、エージェントを有効にして再度移行する必要があります。
* NPM は無効化されないため、移行後のテストで、NetworkMonitoring テーブル、NWConnectionMonitorTestResult テーブル、および NWConnectionMonitorPathResult テーブルにデータを送り続けることができます。 このアプローチにより、既存のログ ベースのアラートと統合が影響を受けないことが保証されます。
* 新しく作成された接続モニターが接続モニターに表示されます。

移行後は、必ず次のようにしてください。
* NPM のテストを手動で無効にします。 それまでの間、それらに対して引き続き同じ料金が発生します。 
* NPM を無効にしている間に、NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルのアラートを再作成するか、メトリックを使用します。 
* 外部統合を NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルに移行します。 外部統合の例は、Power BI や Grafana のダッシュボードや、セキュリティ情報イベント管理 (SIEM) システムとの統合などです。


## <a name="next-steps"></a>次の手順

接続モニターの詳細については、以下を参照してください。
* [接続モニター (クラシック) から接続モニターに移行する](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Azure portal を使用して接続モニターを作成する](./connection-monitor-create-using-portal.md)
