---
title: Grafana を使用して Azure のサービスとアプリケーションを監視する
description: Grafana で表示できるように Azure Monitor および Application Insights データをルーティングします。
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.component: ''
ms.openlocfilehash: 533fbfc42939299f99699a13513cd3b55c89cabc
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957286"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana での Azure サービスの監視
[Azure Monitor データ ソース プラグイン](https://grafana.com/plugins/grafana-azure-monitor-datasource)を使用して [Grafana](https://grafana.com/) から Azure サービスおよびアプリケーションを監視することもできるようになりました。 このプラグインは、Application Insights SDK によって収集されたアプリケーション パフォーマンス データや、Azure Monitor によって提供されるインフラストラクチャ データを収集します。 その後、このデータを Grafana ダッシュボードで表示できます。

このプラグインは現在、プレビューの段階です。

Azure Marketplace から Grafana サーバーを設定し、Azure Monitor および Application Insights からのメトリック用のダッシュボードを構築するには、次の手順を使用します。

## <a name="set-up-a-grafana-instance"></a>Grafana インスタンスを設定する
1. Azure Marketplace に移動し、Grafana Labs のそばの Grafana を選択します。

2. 名前と詳細を入力します。 新しいリソース グループを作成します。 VM ユーザー名、VM パスワード、および Grafana サーバー管理者のパスワードとして選択した値を記録してください。  

3. VM サイズとストレージ アカウントを選択します。

4. ネットワーク構成設定を構成します。

5. 概要を表示し、使用条件を受け入れた後、**[作成]** を選択します。

## <a name="log-in-to-grafana"></a>Grafana にログインする
1. デプロイが完了したら、**[Go to Resource Group] (リソース グループに移動する)** を選択します。 新しく作成されたリソースの一覧が表示されます。

    ![Grafana リソース グループ オブジェクト](media/monitor-send-to-grafana/grafana1.png)

    ネットワーク セキュリティ グループ (この場合は *grafana-nsg*) を選択すると、Grafana サーバーへのアクセスのためにポート 3000 が使用されることを確認できます。

2. リソースの一覧に戻り、**[パブリック IP アドレス]** を選択します。 この画面で見つかった値を使用して、ブラウザーに「*http://<IP address>:3000*」または「*<DNSName>:3000*」と入力します。 今構築した Grafana サーバーのログイン ページが表示されます。

    ![Grafana ログイン画面](media/monitor-send-to-grafana/grafana2.png)

3. *admin* のユーザー名と、前に作成した Grafana サーバー管理者のパスワードを使用してログインします。

## <a name="configure-data-source-plugin"></a>データ ソース プラグインを構成する

正常にログインすると、Azure Monitor データ ソース プラグインが既に含まれていることが確認されます。

![Grafana での Azure Monitor プラグインの表示](media/monitor-send-to-grafana/grafana3.png)

1. Azure Monitor および Application Insights を構成するには、**[データ ソースを追加する]** を選択します。

2. データ ソースの名前を選択し、ドロップダウンからデータ ソースとして **[Azure Monitor]** を選択します。


## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

Grafana は Azure Active Directory サービス プリンシパルを使用して Azure Monitor API に接続し、メトリック データを収集します。 Azure リソースへのアクセスを管理するには、サービス プリンシパルを作成する必要があります。

1. サービス プリンシパルを作成するには、[この手順](../active-directory/develop/howto-create-service-principal-portal.md)を参照してください。 テナント ID、クライアント ID、およびクライアント シークレットをコピーおよび保存します。

2. Azure Active Directory アプリケーションにリーダー ロールを割り当てるには、「[アプリケーションをロールに割り当てる](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role)」を参照してください。     

3. Application Insights を使用する場合は、Application Insights ベースのメトリックを収集するための Application Insights API とアプリケーション ID も含めることができます。 詳細については、「[Getting your API key and Application ID (API キーとアプリケーション ID の取得)](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)」を参照してください。

4. これらのすべての情報を入力した後、**[保存]** を選択すると、Grafana が API をテストします。 次のようなメッセージが表示されます。  

    ![Grafana での Azure Monitor プラグインの表示](media/monitor-send-to-grafana/grafana4-1.png)

> [!NOTE]
> プラグインを構成するときに、どの Azure クラウド (パブリック、Azure 米国政府機関、Azure Germany、または Azure China) に対してプラグインを構成するかを指定できます。
>
>

## <a name="build-a-grafana-dashboard"></a>Grafana ダッシュボードを構築する

1. ホームに移動し、**[新しいダッシュボード]** を選択します。

2. 新しいダッシュボードで、**[Graph] (グラフ)** を選択します。 他のチャート作成オプションを試すこともできますが、この記事では *[Graph] (グラフ)* を例として使用します。

    ![Grafana の新しいダッシュボード](media/monitor-send-to-grafana/grafana5.png)

3. ダッシュボードに空白のグラフが表示されます。

4. パネルのタイトルをクリックし、**[編集]** を選択して、このグラフ チャートにプロットするデータの詳細を入力します。

5. 右側のすべての VM を選択したら、ダッシュボードでのメトリックの表示を開始できます。

2 つのグラフを含む単純なダッシュボードを次に示します。 左側のグラフは、2 つの VM の CPU の割合を示します。 右側のグラフは、トランザクション API の種類で分類された Azure Storage アカウント内のトランザクションを示します。

![Grafana の 2 つのグラフの例](media/monitor-send-to-grafana/grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>省略可能: ダッシュボード再生リストを作成する

Grafana の多くの有効な機能の 1 つに、ダッシュボード再生リストがあります。 複数のダッシュボードを作成し、それらを再生リストに追加して、表示するダッシュボードごとに間隔を構成できます。 ダッシュボードのサイクル全体を表示するには、**[再生]** を選択します。 それらを大きな壁のモニタに表示して、グループの "状態ボード" を提供することもできます。

![Grafana の再生リストの例](media/monitor-send-to-grafana/grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>省略可能: 同じ Grafana サーバーでカスタム メトリックを監視する

Telegraf および InfluxDB をインストールして、同じ Grafana インスタンスでカスタム メトリックとエージェント ベースのメトリックの両方を収集してプロットすることもできます。 これらのメトリックをダッシュボードにまとめて表示するために使用できる多くのデータ ソース プラグインがあります。

この設定を再利用して、Prometheus サーバーからのメトリックを含めることもできます。 Grafana のプラグイン ギャラリーにある Prometheus データ ソース プラグインを使用します。

Telegraf、InfluxDB、Prometheus、および Docker の使用方法に関する優れた参照記事を次に示します。
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04 (Ubuntu 16.04 で TICK スタックを使用してシステム メトリックを監視する方法)](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitor Docker resource metrics with Grafana, InfluxDB, and Telegraf (Grafana、InfluxDB、および Telegraf を使用した Docker リソース メトリックの監視)](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [A monitoring solution for Docker hosts, containers, and containerized services (Docker ホスト、コンテナー、およびコンテナー化されたサービスの監視ソリューション)](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Azure Monitor および Application Insights からのメトリックを含む完全な Grafana ダッシュボードのイメージを次に示します。
![Grafana Example Metrics (Grafana のメトリックの例)](media/monitor-send-to-grafana/grafana8.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

VM を実行している場合は、それを使用しているかどうかにかかわらず課金されます。 追加料金が発生しないようにするには、この記事で作成されたリソース グループをクリーンアップします。

1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[Grafana]** の順にクリックします。
2. リソース グループのページで、**[削除]** をクリックし、テキスト ボックスに「**Grafana**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順
* [Azure Monitor メトリックの概要](../monitoring/monitoring-data-collection.md)
