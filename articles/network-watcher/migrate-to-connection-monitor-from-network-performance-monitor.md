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
ms.openlocfilehash: 59e266274d6b65182ad63513ecf9bf3b5ab95490
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98164840"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Network Performance Monitor から接続モニターに移行する

Network Performance Monitor (NPM) から機能が向上した新しい接続モニターに、1 回のクリックで、ダウンタイムなしにテストを移行できます。 利点の詳細については、[接続モニター](./connection-monitor-overview.md)に関する記事を参照してください。


## <a name="key-points-to-note"></a>注意する点

この移行は、次の結果を生み出すために役立ちます。

* オンプレミスのエージェントとファイアウォールの設定はそのままで動作します。 変更の必要はありません。 Azure 仮想マシンにインストールされている Log Analytics エージェントは、Network Watcher 拡張機能に置き換える必要があります。
* 既存のテストは、[接続モニター] > [テストグループ] > [テスト形式] にマップされます。 **[編集]** を選択すると、新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして変更したり、Azure Resource Manager 経由でテンプレートを送信したりすることができます。
* エージェントによって、Log Analytics ワークスペースとメトリックの両方にデータが送信されます。
* データの監視:
   * **Log Analytics 内のデータ**: 移行まで、データは NetworkMonitoring テーブルで NPM が構成されているワークスペースに残っています。 移行後のデータは、同じワークスペース内の NetworkMonitoring テーブルと ConnectionMonitor_CL テーブルに移動されます。 NPM でテストが無効にされると、データは ConnectionMonitor_CL テーブルにのみ格納されるようになります。
   * **ログ ベースのアラート、ダッシュボード、統合**: 新しい ConnectionMonitor_CL テーブルに基づいて、クエリを手作業で編集する必要があります。 メトリックでアラートを作成し直すには、「[接続モニターによるネットワーク接続の監視](./connection-monitor-overview.md#metrics-in-azure-monitor)」を参照してください。
    
## <a name="prerequisites"></a>前提条件

* お使いのサブスクリプションと Log Analytics ワークスペースのリージョンで Network Watcher を確実に有効にします。
* Log Analytics エージェントがインストールされている Azure 仮想マシンで、Network Watcher 拡張機能を有効にする必要があります。

## <a name="migrate-the-tests"></a>テストを移行する

Network Performance Monitor から接続モニターにテストを移行するには、次のようにします。

1. Network Watcher で、 **[Connection Monitor]\(接続モニター\)** を選択し、 **[Migrate tests from NPM]\(NPM からのテストの移行\)** タブを選択します。 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Network Performance Monitor から接続モニターにテストを移行する" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. ドロップダウン リストで、お使いのサブスクリプションとワークスペースを選択してから、移行する NPM 機能を選択します。 
1. **[インポート]** を選択してテストを移行します。

移行が開始されると、次の変更が行われます。 
* 新しい接続モニター リソースが作成されます。
   * リージョンとサブスクリプションごとに 1 つの接続モニターが作成されます。 オンプレミスのエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_"on-premises"` という形式になります。 Azure のエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_<Azure_region_name>` という形式になります。
   * 監視データは、NPM が有効にされているのと同じ Log Analytics ワークスペースの、Connectionmonitor_CL という新しいテーブルに格納されるようになります。 
   * テスト名はテスト グループ名として引き継がれます。 テストの説明は移行されません。
   * 移行元と移行先のエンドポイントが作成され、新しいテスト グループで使用されます。 オンプレミスのエージェントの場合、エンドポイントは `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` という形式になります。 Azure では、実行されていないエージェントが移行するテストに含まれている場合は、エージェントを有効にして、再度移行する必要があります。
   * 宛先ポートとプローブ間隔は、*TC_\<testname>* および *TC_\<testname>_AppThresholds* というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 成功のしきい値などのオプションのプロパティは、空白のままです。
* NPM は無効にされないため、移行されたテストでは引き続き NetworkMonitoring テーブルと ConnectionMonitor_CL テーブルにデータを送信できます。 このアプローチにより、既存のログ ベースのアラートと統合が影響を受けないことが保証されます。
* 新しく作成された接続モニターが接続モニターに表示されます。

移行後は、必ず次のようにしてください。
* NPM のテストを手動で無効にします。 それまでの間、それらに対して引き続き同じ料金が発生します。 
* NPM を無効にしている間は、ConnectionMonitor_CL テーブルにアラートを再作成するか、メトリックを使用します。 
* すべての外部統合を ConnectionMonitor_CL テーブルに移行します。 外部統合の例は、Power BI や Grafana のダッシュボードや、セキュリティ情報イベント管理 (SIEM) システムとの統合などです。


## <a name="next-steps"></a>次の手順

接続モニターの詳細については、以下を参照してください。
* [接続モニター (クラシック) から接続モニターに移行する](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Azure portal を使用して接続モニターを作成する](./connection-monitor-create-using-portal.md)
