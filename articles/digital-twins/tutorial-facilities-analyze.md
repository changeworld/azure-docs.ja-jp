---
title: Azure Digital Twins 設定からのイベントを分析する | Microsoft Docs
description: このチュートリアルの手順を使用して、Azure Time Series Insights を使って Azure Digital Twins 空間からのイベントを視覚化および分析する方法を学習します。
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323463"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>チュートリアル: Time Series Insights を使用して Azure Digital Twins 空間からのイベントを視覚化および分析する

Azure Digital Twins インスタンスをデプロイし、空間をプロビジョニングして、特定の条件を監視するカスタム関数を実装した後は、傾向や異常を見つけるために空間から受け取ったイベントやデータを視覚化することができます。 

[最初のチュートリアル](tutorial-facilities-setup.md)では、モーション、二酸化炭素、温度のセンサーを備えた部屋がある架空の建物の空間グラフを構成しました。 [2 番目のチュートリアル](tutorial-facilities-udf.md)では、グラフとユーザー定義関数をプロビジョニングしました。 関数によってこれらのセンサーの値が監視され、適切な条件になると通知がトリガーされます。その条件とは、部屋が空室で、温度と二酸化炭素のレベルが正常であるという条件です。 このチュートリアルでは、Digital Twins 設定からの通知とデータを Azure Time Series Insights と統合する方法について説明します。 その後、センサーの値を時間の経過と共に視覚化して、最も利用されている部屋や 1 日のうちで最も忙しい時間帯などの傾向を調べることができます。 さらに、どの部屋で風通しの悪さや暑さをより強く感じるかや、建物内のあるエリアが、空調設備の異常を示す高い温度値を常に送信しているかどうか、などの異常を検出することもできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Event Hubs を使用したデータのストリーミング
> * Time Series Insights を使用した分析

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、お客様が Azure Digital Twins の設定の[構成](tutorial-facilities-setup.md)と[プロビジョニング](tutorial-facilities-udf.md)を完了していることを前提としています。 次に進む前に、以下が準備されていることを確認します。
- [Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Digital Twins のインスタンスが実行中であること。
- [Digital Twins C# のサンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp)がダウンロードされ、作業マシン上に展開されていること。
- サンプルを実行する開発マシンに [.NET Core SDK バージョン 2.1.403 以上](https://www.microsoft.com/net/download)がインストールされていること。 適切なバージョンがインストールされているかどうかを確認するには、`dotnet --version` を実行します。 


## <a name="stream-data-using-event-hubs"></a>Event Hubs を使用したデータのストリーミング
[Event Hubs](../event-hubs/event-hubs-about.md) サービスを使用すると、データをストリーム配信するパイプラインを作成できます。 このセクションでは、Digital Twins インスタンスと TSI インスタンスの間のコネクタとしてイベント ハブを作成する方法について説明します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. 左側のナビゲーション パネルで、**[リソースの作成]** をクリックします。 

1. **[Event Hubs]** を見つけて選択します。 **Create** をクリックしてください。

1. Event Hubs 名前空間の**名前**を入力し、*Standard* **価格レベル**、**サブスクリプション**、Digital Twins インスタンスに使用した**リソース グループ**、**場所**を選択します。 **Create** をクリックしてください。 

1. デプロイされたら、Event Hubs 名前空間の "*デプロイ*" に移動し、**[リソース]** の下の名前空間をクリックします。

    ![Event Hub 名前空間](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. Event Hubs 名前空間の **[概要]** ウィンドウで、最上部にある **[イベント ハブ]** をクリックします。 
    ![イベント ハブ](./media/tutorial-facilities-analyze/create-event-hub.png)

1. イベント ハブの**名前**を入力し、**[作成]** をクリックします。 デプロイされると、Event Hubs 名前空間の **[Event Hubs]** ウィンドウに *[アクティブ]* **状態**として表示されます。 このイベント ハブをクリックして、**[概要]** ウィンドウを開きます。

1. 最上部にある **[コンシューマー グループ]** をクリックし、コンシューマー グループの名前 (たとえば、*tsievents*) を入力します。 **Create** をクリックしてください。
    ![イベント ハブ コンシューマー グループ](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   作成されたコンシューマー グループが、イベント ハブの **[概要]** ウィンドウの下部にある一覧に表示されます。 

1. イベント ハブの **[共有アクセス ポリシー]** ウィンドウを開き、**[追加]** ボタンをクリックします。 *ManageSend* という名前のポリシーを**作成**し、すべてのチェック ボックスがオンになっていることを確認します。 

    ![イベント ハブの接続文字列](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. 作成した *ManageSend* ポリシーを開き、**[接続文字列 - 主キー]** と **[接続文字列 - セカンダリ キー]** の値を一時ファイルにコピーします。 これらの値は、次のセクションでイベント ハブのエンドポイントを作成するために必要です。

### <a name="create-endpoint-for-the-event-hub"></a>イベント ハブのエンドポイントの作成

1. コマンド ウィンドウで、Digital Twins サンプルの **_occupancy-quickstart\src** フォルダーに移動していることを確認します。

1. エディターで **_actions\createEndpoints.yaml_** ファイルを開きます。 内容を次のものに置き換えます。

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. プレースホルダー `Primary_connection_string_for_your_event_hub` を、イベント ハブの **[接続文字列 - 主キー]** の値に置き換えます。 この接続文字列が次の形式になっていることを確認します。
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. プレースホルダー `Secondary_connection_string_for_your_event_hub` を、イベント ハブの **[接続文字列 - セカンダリ キー]** の値に置き換えます。 この接続文字列が次の形式になっていることを確認します。 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. プレースホルダー `Name_of_your_Event_Hubs_namespace` を Event Hubs 名前空間の名前に置き換えます。

    > [!IMPORTANT]
    > 引用符なしですべての値を入力します。 *YAML* ファイル内のコロンの後に少なくとも 1 つの空白文字があることを確認してください。 *YAML* ファイルの内容は、[このツール](https://onlineyamltools.com/validate-yaml)のようなオンライン YAML 検証ツールを使用して検証することもできます。


1. ファイルを保存して閉じます。 コマンド ウィンドウで次のコマンドを実行し、メッセージが表示されたら Azure アカウントを使用してサインインします。

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   イベント ハブに対して 2 つのエンドポイントが作成されます。

   ![Event Hubs のエンドポイント](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Time Series Insights を使用した分析

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[リソースの作成]** をクリックします。 

1. 新しい **Time Series Insights** リソースを見つけて選択します。 **Create** をクリックしてください。

1. Time Series Insights インスタンスの**名前**を入力し、**サブスクリプション**を選択します。 Digital Twins インスタンスに対して使用した**リソース グループ**と**場所**を選択します。 **Create** をクリックしてください。

    ![TSI の作成](./media/tutorial-facilities-analyze/create-tsi.png)

1. デプロイされたら、Time Series Insights 環境を開き、その **[イベント ソース]** ウィンドウを開きます。 コンシューマー グループを追加するために、最上部にある **[追加]** をクリックします。

1. **[新しいイベント ソース]** ウィンドウで、**名前**を入力し、他の値が正しく選択されていることを確認します。 **イベント ハブ ポリシー名**として、*ManageSend* を選択します。さらに、**イベント ハブ コンシューマー グループ**として、前のセクションで作成した "*コンシューマー グループ*" を選択します。 **Create** をクリックしてください。

    ![TSI のイベント ソース](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Time Series Insights 環境の **[概要]** ウィンドウを開き、最上部にある **[環境を開く]** をクリックします。 "*データ アクセスの警告*" が表示された場合は、TSI インスタンスの **[データ アクセス ポリシー]** ウィンドウを開きます。**[追加]** をクリックし、ロールとして **[共同作成者]** を選択して、適切なユーザーを選択します。

1. **[環境を開く]** をクリックすると、[Time Series Insights エクスプローラー](../time-series-insights/time-series-insights-explorer.md)が開きます。 イベントが表示されない場合は、Digital Twins サンプルの **_device-connectivity_** プロジェクトに移動し、`dotnet run` を実行して、デバイス イベントをシミュレートします。

1. いくつかのシミュレートされたイベントが生成されたら、Time Series Insights エクスプローラーに戻り、最上部にある更新ボタンをクリックします。 シミュレートされたセンサー データの分析グラフが作成されるのがわかります。 

    ![TSI エクスプローラー](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. 次に、Time Series エクスプローラーを使用して、部屋、センサー、その他のリソースからのさまざまなイベントやデータについてグラフやヒートマップを生成できます。 独自の視覚化を作成するには、左側の **[メジャー]** と **[SPLIT BY]\(分割基準\)** という名前のドロップダウン ボックスをクリックします。 たとえば、**[メジャー]** で *[イベント]* を選択し、**[SPLIT BY]\(分割基準\)** で *[DigitalTwins-SensorHardwareId]* を選択して、次の画像のような各センサーのヒートマップを生成します。

    ![TSI エクスプローラー](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これ以降 Azure Digital Twins の探索を中止する場合は、このチュートリアルで作成されたリソースを削除してかまいません。

1. [Azure portal](http://portal.azure.com) の左側のメニューの **[すべてのリソース]** をクリックし、目的の Digital Twins リソース グループを選択して**削除**します。
2. 必要に応じて、作業マシン上のサンプル アプリケーションも続けて削除することができます。 


## <a name="next-steps"></a>次の手順

Azure Digital Twins の空間インテリジェンス グラフとオブジェクト モデルについて学習するには、次の記事に進んでください。 
> [!div class="nextstepaction"]
> [Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する](concepts-objectmodel-spatialgraph.md)

