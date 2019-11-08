---
title: Grafana を使用して Azure Data Explorer のデータを視覚化する
description: この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581951"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Grafana で Azure Data Explorer のデータを視覚化する

Grafana は、データのクエリを実行して視覚化し、視覚化に基づいてダッシュボードを作成して共有できる分析プラットフォームです。 Grafana には、Azure Data Explorer に接続してそのデータを視覚化できる、Azure Data Explorer "*プラグイン*" が用意されています。 この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。

次のビデオを使用することで、Grafana の Azure Data Explorer プラグインを使って、Azure Data Explorer を Grafana 用のデータ ソースとして設定してから、データを視覚化する方法を学習できます。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

または、以下の記事で詳しく説明されているように、[データ ソースを構成](#configure-the-data-source)し、[データを視覚化](#visualize-data)することができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下が必要です。

* お使いのオペレーティング システムに対応する [Grafana バージョン 5.3.0 以降](https://docs.grafana.org/installation/)。

* Grafana の [Azure Data Explorer プラグイン](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)。

* StormEvents サンプル データを含むクラスター。 詳細については、「[クイック スタート: Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>プロパティを指定し、接続をテストする

"*閲覧者*" ロールに割り当てられたサービス プリンシパルを使用して、Grafana のインスタンスでプロパティを指定し、Azure Data Explorer への接続をテストします。

1. Grafana で、左側のメニューの歯車アイコンを選択し、 **[Data Sources]\(データ ソース\)** を選択します。

    ![データ ソース](media/grafana/data-sources.png)

1. **[Add data source (データ ソースの追加)]** を選択します。

1. **[Data Sources / New]\(データソース/新規\)** ページで、データ ソースの名前を入力し、種類として **[Azure Data Explorer Datasource]\(Azure Data Explorer データ ソース\)** を選択します。

    ![接続名と種類](media/grafana/connection-name-type.png)

1. https://{クラスター名}.{リージョン}.kusto.windows.net の形式でクラスターの名前を入力します。 Azure portal または CLI で取得した他の値を入力します。 マッピングについては、次の画像の下の表をご覧ください。

    ![接続のプロパティ](media/grafana/connection-properties.png)

    | Grafana UI | Azure ポータル | Azure CLI |
    | --- | --- | --- |
    | サブスクリプション ID | サブスクリプション ID | SubscriptionId |
    | テナント ID | ディレクトリ ID | tenant |
    | クライアント ID | アプリケーション ID | appId |
    | クライアント シークレット | パスワード | password |
    | | | |

1. **[Save & Test]\(保存してテスト\)** を選択します。

    テストが成功した場合は、次のセクションに進みます。 問題が発生した場合は、Grafana で指定した値を確認し、これまでの手順を見直します。

## <a name="visualize-data"></a>データの視覚化

Azure Data Explorer を Grafana のデータ ソースとして構成したら、データを視覚化します。 ここでは基本的な例を紹介しますが、できることはほかにも多数あります。 サンプル データ セットに対して実行する他のクエリの例については、「[Azure データ エクスプローラーのクエリを記述する](write-queries.md)」を参照することをお勧めします。

1. Grafana で、左側のメニューのプラス アイコンを選択し、 **[Dashboard]\(ダッシュボード\)** を選択します。

    ![ダッシュボードの作成](media/grafana/create-dashboard.png)

1. **[Add]\(追加\)** タブの **[Graph]\(グラフ\)** を選択します。

    ![グラフの追加](media/grafana/add-graph.png)

1. グラフ パネルで、 **[Panel Title]\(パネル タイトル\)** 、 **[Edit]\(編集\)** の順に選択します。

    ![パネルの編集](media/grafana/edit-panel.png)

1. パネルの下部にある **[Data Source]\(データ ソース\)** を選択し、構成済みのデータ ソースを選択します。

    ![データ ソースの選択](media/grafana/select-data-source.png)

1. クエリ ウィンドウで、次のクエリをコピーし、 **[Run]\(実行\)** を選択します。 このクエリは、サンプル データ セットのイベント数を日単位でバケット処理します。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Run query](media/grafana/run-query.png)

1. グラフは既定で過去 6 時間のデータを対象としているため、グラフには結果が表示されません。 上部のメニューで、 **[Last 6 hours]\(過去 6 時間\)** を選択します。

    ![過去 6 時間](media/grafana/last-six-hours.png)

1. StormEvents サンプル データ セットに含まれている 2007 年を対象とするカスタム範囲を指定します。 **[適用]** を選択します。

    ![カスタム日付範囲](media/grafana/custom-date-range.png)

    これで、日単位でバケット処理された 2007 年のデータがグラフに表示されます。

    ![完成したグラフ](media/grafana/finished-graph.png)

1. 上部のメニューで保存アイコンを選択します。 ![[Save]\(保存\) アイコン](media/grafana/save-icon.png)。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer のクエリを記述する](write-queries.md)

* [チュートリアル: Power BI で Azure Data Explorer のデータを視覚化する](visualize-power-bi.md)
