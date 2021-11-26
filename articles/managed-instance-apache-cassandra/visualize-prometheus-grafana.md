---
title: Azure Managed Instance for Apache Cassandra から生成されたメトリックを視覚化するように Grafana を構成する
description: Azure Managed Instance for Apache Cassandra クラスターから生成されたメトリックを視覚化するために、VM に Grafana をインストールして構成する方法について説明します。
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/16/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3b74d168f37391bf89bc7591b7263ba40f3d1074
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723252"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>マネージド インスタンス クラスターから生成されたメトリックを視覚化するように Grafana を構成する

Azure Managed Instance for Apache Cassandra クラスターをデプロイすると、サービスによって各データ ノードに [Metric Collector for Apache Cassandra](https://github.com/datastax/metric-collector-for-apache-cassandra) エージェント ソフトウェアがプロビジョニングされます。 メトリックは [Prometheus](https://prometheus.io/) によって使用され、Grafana で視覚化されます。 この記事では、マネージド インスタンス クラスターから生成されたメトリックを視覚化するように Prometheus と Grafana を構成する方法について説明します。 

メトリックを視覚化するには、次のタスクが必要となります。

* マネージド インスタンスが存在する Azure 仮想ネットワーク内に Ubuntu 仮想マシンをデプロイします。
* [Prometheus ダッシュボード](https://github.com/datastax/metric-collector-for-apache-cassandra#installing-the-prometheus-dashboards)を VM にインストールします。

## <a name="deploy-an-ubuntu-server"></a>Ubuntu サーバーをデプロイする

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. マネージド インスタンス クラスターがあるリソース グループに移動します。 **[追加]** を選択し、**Ubuntu Server 18.04 LTS** イメージを検索します。

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Azure portal から Ubuntu サーバー イメージを検索する。" border="true":::

1. そのイメージを選択し、 **[作成]** を選択します。

1. **[仮想マシンの作成]** ブレードで、次の各フィールドの値を入力します。その他のフィールドは既定値のままでかまいません。

   * **[仮想マシン名]** - VM の名前を入力します。
   * **[リージョン]** - 仮想ネットワークのデプロイ先と同じリージョンを選択します。

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Ubuntu サーバー イメージを使用して VM を作成するためのフォームに記入する。" border="true":::

1. **[ネットワーク]** タブで、マネージド インスタンスがデプロイされている仮想ネットワークを選択します。

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Ubuntu サーバーのネットワーク設定を構成する。" border="true":::

1. 最後に **[確認と作成]** を選択して、メトリック サーバーを作成します。

## <a name="install-prometheus-dashboards"></a>Prometheus ダッシュボードをインストールする

1. 最初に、新しくデプロイされた Ubuntu サーバーのネットワーク設定に、ポート `9090` と `3000` を許可する受信ポート規則があることを確認します。 これらは後で Prometheus と Grafana のそれぞれで必要になります。 

   :::image type="content" source="./media/visualize-prometheus-grafana/networking.png" alt-text="ポートを許可する" border="true":::

1. [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) や任意のクライアント ツールを使用して、Ubuntu サーバーに SSH で接続します。

1. VM に接続した後、メトリック コレクター ソフトウェアをインストールする必要があります。 まず、ファイルをダウンロードして解凍します。

   ```bash
    #install unzip utility (if not already installed)
    sudo apt install unzip
    
    #get dashboards
    wget https://github.com/datastax/metric-collector-for-apache-cassandra/releases/download/v0.3.0/datastax-mcac-dashboards-0.3.0.zip -O temp.zip
    unzip temp.zip
   ```

1. 次に、prometheus ディレクトリに移動し、vi を使用して `tg_mcac.json` ファイルを編集します。

   ```bash
    cd */prometheus
    vi tg_mcac.json    
   ```


1. クラスター内の各ノードの IP アドレスを、それぞれポート 9443 で `targets` に追加します。 `tg_mcac.json` ファイルは以下のようになります。

   ```bash
    [
      {
        "targets": [
          "10.9.0.6:9443","10.9.0.7:9443","10.9.0.8:9443"
        ],
        "labels": {
    
        }
      }
    ]  
   ```

1. ファイルを保存します。 次に、同じディレクトリで `prometheus.yaml` ファイルを編集します。 以下のセクションを見つけてください。

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
   ```

1. このセクションの直下に、以下を追加します。 メトリックは https 経由で公開されるため、これが必要です。

   ```bash
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. ファイルは次のようになるはずです。 各行のタブが以下の通りであることを確認してください。 

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. ファイルを保存します。 Prometheus と Grafana を起動する準備ができました。 まず、Docker をインストールします。

    ```bash
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu `lsb_release -cs` test"
    sudo apt update
    sudo apt install docker-ce
    ```

1. 次に、docker compose をインストールします。

    ```bash
    sudo apt install docker-compose
    ```

1. そして、`docker-compose.yaml` が存在する最上位のディレクトリに移動し、アプリケーションを起動します。

    ```bash
    cd ..
    sudo docker-compose up
    ```

1. Prometheus はメトリック サーバーのポート `9090` で、Grafana ダッシュボードはポート `3000` で利用できるはずです。

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.png" alt-text="Cassandra マネージド インスタンスのメトリックをダッシュボードで表示する。" border="true":::


## <a name="next-steps"></a>次のステップ

この記事では、Grafana を使用してダッシュボードを構成し、Prometheus のメトリックを視覚化する方法について説明しました。 Azure Managed Instance for Apache Cassandra の詳細については、次の記事を参照してください。

* [Azure Managed Instance for Apache Cassandra の概要](introduction.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
