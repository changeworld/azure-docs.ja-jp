---
title: VM 用 Azure Monitor を使用してアプリの依存関係を表示する方法 | Microsoft Docs
description: マップは、Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする、VM 用 Azure Monitor の機能です。 この記事では、さまざまなシナリオでこの機能を使用する方法について詳しく説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: magoedte
ms.openlocfilehash: e75409e791c00dc0a5bec591aecfbaa019df8f81
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225164"
---
# <a name="using-azure-monitor-for-vms-map-to-understand-application-components"></a>VM 用 Azure Monitor のマップを使用したアプリケーション コンポーネントの理解
Azure 環境で実行されている Windows および Linux 仮想マシン上の検出されたアプリケーション コンポーネントの表示は、VM 用 Azure Monitor を使用して仮想マシンから直接、または Azure Monitor から VM のグループを経由しての 2 の方法で観察できます。 

この記事は、2 つの視点でのエクスペリエンスとマップ機能の使用方法を理解するうえで役立ちます。 VM の Azure Monitor の構成については、[VM の Azure Monitor の有効化](monitoring-vminsights-onboard.md)に関する記事をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="introduction-to-map-experience"></a>マップ エクスペリエンスの概要
単一の仮想マシンまたは VM のグループのマップの表示について説明する前に、情報を表示する方法と視覚化によって表される内容を理解できるように、マップ機能の概要を簡単に説明します。  

マップ機能を VM から直接選択しても、Azure Monitor から選択しても、一貫したエクスペリエンスが得られます。  唯一の違いは、Azure Monitor から選択すると、多層アプリケーションまたはクラスターのすべてのメンバーが 1 つのマップに表示されることです。

マップでは、サーバー間のアクティブなネットワーク接続で実行されているプロセス、受信および送信接続の待機時間、TCP 接続されたアーキテクチャのポートを検出することによって、指定した時間範囲での VM の依存関係が視覚化されます。  VM を展開してプロセスの詳細を表示すると、その VM と通信しているプロセスだけが表示されます。 仮想マシンに接続するフロントエンド クライアントの数は、クライアント グループに示されます。 VM が接続するバックエンド サーバーの数は、サーバー ポート グループに示されます。 サーバー ポート グループを展開すると、そのポート経由で接続されているサーバーの詳細な一覧が表示されます。  

仮想マシンをクリックすると、右側に **[プロパティ]** ウィンドウが展開され、オペレーティング システムによって報告されたシステム情報、Azure VM のプロパティ、検出された接続を集計したドーナツなど、選択した項目のプロパティが表示されます。 

![コンピューターのシステム プロパティ](./media/monitoring-vminsights-maps/properties-pane-01.png)

ウィンドウの右側にある **[ログ イベント]** アイコンをクリックしてウィンドウのフォーカスを切り替えると、VM から収集されたデータが Log Analytics に送信され、クエリに使用できるテーブルの一覧が表示されます。  表示されているレコードの種類のいずれかをクリックすると、**[ログ]** ページが開き、その仮想マシンに対する構成済みのクエリによってフィルター処理されたその種類の結果が表示されます。  

![[プロパティ] ウィンドウのログ検索の一覧](./media/monitoring-vminsights-maps/properties-pane-logs-01.png)

"*ログ*" を閉じると、**[プロパティ]** ウィンドウに戻ります。**[アラート]** を選択すると、正常性基準に基づいて VM で発生したアラートが表示されます。 マップが Azure アラートと統合され、選択された時間範囲内に選択されたサーバーで発生したアラートが表示されます。 最新のアラートがある場合はサーバーにアイコンが表示され、[Machine Alerts]\(マシンのアラート\) ウィンドウにアラートが一覧表示されます。 

![[プロパティ] ウィンドウのマシンのアラート](./media/monitoring-vminsights-maps/properties-pane-alerts-01.png)

マップ機能で関連するアラートを表示できるようにするには、特定のコンピューターに対して実行されるアラート ルールを作成します。 適切なアラートを作成するには:
- コンピューター別にグループ化する句を含めます (例: **by Computer interval 1 minute**)。
- メトリックの測定に基づいてアラートを生成することを選択します。

Azure アラートとアラート ルールの作成の詳細については、「[Azure Monitor での統合アラート](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)」をご覧ください。

右上隅の **[凡例]** オプションは、マップ上の記号と役割を示します。  マップの詳細を確認するためにズームインし、その周辺を移動するには、ページの右下にあるズーム コントロールでズーム レベルを設定し、ページを現在のページのサイズに合わせます。  

## <a name="connection-metrics"></a>接続のメトリック
**[接続]** ウィンドウには、VM からの選択した接続 (TCP ポート経由) の標準的な接続メトリックが表示されます。 メトリックとして、応答時間、1 分あたりの要求数、トラフィックのスループット、リンクがあります。  

![ネットワーク接続のグラフ ウィンドウの例](./media/monitoring-vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失敗した接続
マップには、プロセスとコンピューターの失敗した接続が表示されます。赤色の破線は、クライアント システムがプロセスまたはポートに到達できないことを示しています。 失敗した接続を試みたシステムが、依存関係エージェントを使用するシステムである場合は、そのシステムから失敗した接続が報告されます。 マップでは、接続の確立に失敗した TCP ソケットを監視することで、このプロセスを評価します。 この失敗の原因としては、ファイアウォール、クライアントまたはサーバーの構成の問題、またはリモート サービスが利用できない状態であることが考えられます。

![マップ上の失敗した接続の例](./media/monitoring-vminsights-maps/map-group-failed-connection-01.png)

失敗した接続を把握すると、トラブルシューティング、移行の検証、セキュリティ分析、サービスの全体的なアーキテクチャの理解に役立ちます。 失敗した接続は無害の場合もありますが、突然到達不能になったフェールオーバー環境や、クラウド移行後に相互に通信できなくなった 2 つのアプリケーション層など、問題を端的に示していることも少なくありません。

### <a name="client-groups"></a>クライアント グループ
マップ上のクライアント グループは、マップされたマシンに接続されているクライアント マシンを表します。 1 つのクライアント グループは、個々のプロセスまたはマシンのクライアントを表します。

![マップ上のクライアント グループの例](./media/monitoring-vminsights-maps/map-group-client-groups-01.png)

クライアント グループ内のシステムの、監視対象のクライアントと IP アドレスを確認するには、グループを選択します。 そのグループの下に、グループの内容が表示されます。  

![マップ上のクライアント グループの IP アドレスの一覧の例](./media/monitoring-vminsights-maps/map-group-client-group-iplist-01.png)

グループに監視対象のクライアントと監視対象外のクライアントが含まれている場合は、グループのドーナツ グラフの適切なセクションを選択することで、クライアントをフィルター処理できます。

### <a name="server-port-groups"></a>サーバー ポート グループ
サーバー ポート グループは、マップされたマシンからの受信接続があるサーバーのサーバー ポートを表します。 このグループには、サーバー ポートと、そのポートに接続しているサーバーの数が含まれています。 グループを選択すると、個々のサーバーと接続が一覧表示されます。 

![マップ上のサーバー ポート グループの例](./media/monitoring-vminsights-maps/map-group-server-port-groups-01.png)  

グループに監視対象のサーバーと監視対象外のサーバーが含まれている場合は、グループのドーナツ グラフの適切なセクションを選択することで、サーバーをフィルター処理できます。

## <a name="view-map-directly-from-a-virtual-machine"></a>仮想マシンから直接マップを表示する 

仮想マシンから VM 用 Azure Monitor に直接アクセスするには、次の手順に従います。

1. Azure Portal で、**[仮想マシン]** を選択します。 
2. リストから VM を選択し、**[監視]** セクションの **[Insights (プレビュー)]** を選択します。  
3. **[マップ]** タブを選択します。

マップでは、指定した時間範囲での VM の依存関係 (アクティブなネットワーク接続で実行されているプロセス グループとプロセス) が視覚化されます。  既定では、マップには過去 30 分間の情報が表示されます。  左上隅にある**時間範囲**セレクターを使用して、過去の時間範囲 (最大 1 時間) でクエリを実行すると、過去 (インシデントの発生中や変更が行われる前など) の依存関係を表示できます。  

![直接 VM マップの概要](./media/monitoring-vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Azure Monitor からマップを表示する
Azure Monitor からマップ機能にアクセスすると、仮想マシンとその依存関係の全体像を確認できます。  Azure Monitor からマップ機能にアクセスするには、次の手順に従います。 

1. Azure portal で、**[モニター]** を選択します。 
2. **[分析情報]** セクションの **[Virtual Machines (プレビュー)]** を選択します。
3. **[マップ]** タブを選択します。

![Azure Monitor のマルチ VM マップの概要](./media/monitoring-vminsights-maps/map-multivm-azure-monitor-01.png)

複数の Log Analytics ワークスペースがある場合は、ページの上部にある**ワークスペース** セレクターから、ソリューションと統合され、ソリューションに報告する仮想マシンがあるワークスペースを選択します。  次に、**グループ** セレクターからサブスクリプションまたはリソース グループを選択して、指定した期間で、そのグループに一致する一連の VM とその依存関係を表示します。  既定では、マップには過去 30 分間の情報が表示されます。  **時間範囲**セレクターを使用して、過去の時間範囲 (最大 1 時間) でクエリを実行すると、過去 (インシデントの発生中や変更が行われる前など) の依存関係を表示できます。   

## <a name="next-steps"></a>次の手順
正常性機能の使用方法については、[Azure VM の正常性の表示](monitoring-vminsights-health.md)に関する記事をご覧ください。また、VM のパフォーマンスによってボトルネックや全体的な使用状況を確認する方法については、[VM 用 Azure Monitor でのパフォーマンスの表示](monitoring-vminsights-performance.md)に関する記事をご覧ください。 
