---
title: Azure Time Series Insights を使用してリモート監視データを視覚化する | Microsoft Docs
description: Time Series Insights 環境を構成して、リモート監視ソリューションの時系列データを調査および分析する方法について説明します。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185892"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Time Series Insights を使用してリモート監視データを視覚化する

オペレーターは、リモート監視の構成済みソリューションによって提供される追加設定不要の視覚化をもっと拡張したいと考えることがあります。 マイクロソフトのソリューション アクセラレーターは、追加設定なしで TSI と統合できます。 このハウツー記事では、デバイスのテレメトリを分析し、異常を検出するように Time Series Insights を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー記事を完了するには、以下が必要です。

* [リモート監視の構成済みソリューションをデプロイする](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>コンシューマー グループの作成

Time Series Insights にデータをストリーミングするために使用する専用のコンシューマー グループを IoT Hub 内に作成する必要があります。

> [!NOTE]
> コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 コンシューマー グループごとに、最大 5 つの出力コンシューマーが可能です。 5 つの出力シンクすべてに対して、新しいコンシューマー グループを作成する必要があり、最大 32 のコンシューマー グループを作成できます。

1. Azure Portal で [クラウド シェル] ボタンをクリックします。

1. 次のコマンドを実行して、新しいコンシューマー グループを作成します。

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>新しい Time Series Insights 環境を作成する

Azure Time Series Insights は、IoT 規模の時系列データをクラウドで管理するための、フル マネージドの分析、ストレージ、視覚化サービスです。 極めてスケーラブルな時系列データ ストレージを提供し、世界中からの何十億ものイベント ストリーミングを数秒で調査、分析します。 Time Series Insights を使用すると、テラバイトの時系列データを保存、管理し、何十億ものイベントを同時に調査、視覚化できるほか、根本原因分析を行い、複数のサイトやアセットを比較できます。

1. [Azure Portal](http://portal.azure.com/) にサインインします。

1. **[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Time Series Insights]** を選択します。

    ![新しい Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Time Series Insights 環境を作成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | 環境名 | 次のスクリーン ショットでは、**contorosrmtsi** という名前を使用しています。 この手順を完了したら、独自の一意の名前を選択してください。 |
    | サブスクリプション | ドロップダウンから Azure サブスクリプションを選択します。 |
    | リソース グループ | **新規作成**。 **ContosoRM** という名前を使用します。 |
    | Location | **[米国東部]** を使用します。 環境は、リモート監視ソリューションと同じリージョンに作成します。 |
    | SKU |**S1** |
    | 容量 | **1** |
    | [ダッシュボードにピン留めする] | **はい** |

    ![Time Series Insights を作成する](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. **Create** をクリックしてください。 環境が作成されるまで、少し時間がかかる可能性があります。

## <a name="create-event-source"></a>イベント ソースを作成する

IoT Hub に接続する新しいイベント ソースを作成します。 必ず前の手順で作成したコンシューマー グループを使用してください。 Time Series Insights では、各サービスが別のサービスによって使用されていない専用のコンシューマー グループを用意する必要があります。

1. 新しい Time Series 環境に移動します。

1. 左側で、**[イベント ソース]** を選択します。

    ![イベント ソースを表示する](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. **[追加]** をクリックします。

    ![イベント ソースを追加する](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub を新しいイベント ソースとして構成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | イベント ソース名 | 次のスクリーン ショットでは、**contosorm-iot-hub** という名前を使用しています。 この手順を完了したら、独自の一意の名前を使用してください。 |
    | ソース | **IoT Hub** |
    | インポート オプション | **使用可能なサブスクリプションの IoT Hub を使用する** |
    | サブスクリプション ID | ドロップダウンから Azure サブスクリプションを選択します。 |
    | IoT Hub 名 | **contosorma57a6**。 リモート監視ソリューションの IoT Hub の名前を使用します。 |
    | IoT Hub ポリシー名 | **iothubowner**。使用するポリシーは、所有者ポリシーであることを確認してください。 |
    | IoT Hub ポリシー キー | このフィールドは自動的に設定されます。 |
    | IoT Hub コンシューマー グループ | **timeseriesinsights**。 |
    | イベントのシリアル化の形式 | **JSON**     | タイムスタンプ プロパティ名 | 空白 |

    ![イベント ソースを作成する](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. **Create** をクリックしてください。

> [!NOTE]
> Time Series Insights エクスプローラーにその他のユーザー アクセスを許可する必要がある場合は、[データ アクセスを許可する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access)ための手順を使用します。

## <a name="time-series-insights-explorer"></a>Time Series Insights エクスプローラー

Time Series Insights エクスプローラーは、データの視覚化を作成するために役立つ Web アプリです。

1. **[概要]** タブを選択します。

1. **[環境を開く]** をクリックします。Time Series Insights Web アプリが開きます。

    ![Time Series Insights エクスプローラー](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. 時間選択パネルで、クイック タイム メニューから **[過去 12 時間]** を選択し、**[検索]** をクリックします。

    ![Time Series Insights エクスプローラーでの検索](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. 左側の条件パネルで、メジャー値として **[温度]** を選択し、分割値として **[iothub-connection-device-id]** を選択します。

    ![Time Series Insights エクスプローラーでのクエリ](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. グラフを右クリックし、**[イベントの探索]** を選択します。

    ![Time Series Insights エクスプローラーでのイベント](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. イベントは、表形式のグリッドに表示されます。

    ![Time Series Insights エクスプローラーでの表](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. パースペクティブ ビュー ボタンをクリックします。

    ![Time Series Insights エクスプローラーのパースペクティブ](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. **[追加]** をクリックして、パースペクティブで新しいクエリを作成します。

    ![Time Series Insights エクスプローラーでのクエリの追加](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. クイック タイムとして **[過去 12 時間]** を、メジャーとして **[湿度]** を、分割として **[iiothub-connection-device-id]** を選択します。

    ![Time Series Insights エクスプローラーでのクエリ](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. パースペクティブ ビュー ボタンをクリックして、デバイス メトリック ダッシュボードを表示します。

    ![Time Series Insights エクスプローラーでのダッシュボード](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>次の手順

Time Series Insights エクスプローラーでのデータの探索とクエリ方法の詳細については、「[Azure タイム シリーズ Insights エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)」を参照してください。
