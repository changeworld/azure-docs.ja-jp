---
title: Azure IoT Hub から取得したデータのリアルタイム データの視覚化 – Power BI
description: Power BI を使用して、センサーから収集されて Azure IoT Hub に送信された気温と湿度のデータを視覚化します。
author: eross-msft
keywords: リアルタイム データの視覚化, ライブ データの視覚化, センサー データの視覚化
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: lizross
ms.openlocfilehash: 15e297f65aad93cd3999ae44953a3410c7281788
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555052"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>チュートリアル: Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

Microsoft Power BI を使用して、Azure IoT ハブが受信したリアルタイム センサー データを視覚化できます。 これを行うには、IoT Hub からのデータを使用し、Power BI 内のデータセットにルーティングするように Azure Stream Analytics ジョブを構成します。 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="エンド ツー エンド ダイアグラム" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/) は、大規模なデータ セットに対してセルフサービスおよびエンタープライズ ビジネス インテリジェンス (BI) を実行するために使用できるデータ視覚化ツールです。 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) は、分析情報を取得、レポートを作成、またはアラートとアクションをトリガーするために使用できるデータの高速移動ストリームを分析および処理するための、フル マネージド リアルタイム分析サービスです。 

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * IoT ハブのコンシューマー グループを作成します。
> * コンシューマー グループから温度テレメトリを読み取って Power BI に送信する Azure Stream Analytics ジョブを作成して構成します。
> * Power BI 内で温度データのレポートを作成し、Web で共有します。

## <a name="prerequisites"></a>前提条件

* 選択した開発言語で[テレメトリの送信](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)に関するクイックスタートのいずれかを完了します。 または、温度テレメトリを送信する任意のデバイス アプリを使用することもできます。たとえば、[Raspberry Pi オンライン シミュレーターや](iot-hub-raspberry-pi-web-simulator-get-started.md)、[組み込みデバイス](../iot-develop/quickstart-devkit-mxchip-az3166.md)のクイックスタートの 1 つなどです。 これらの記事では、次の要件について取り上げています。
  
  * 有効な Azure サブスクリプション
  * サブスクリプション内の Azure IoT Hub。
  * Azure IoT ハブにメッセージを送信するクライアント アプリ。

* Power BI アカウント ([Power BI を無料で試す](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成、構成、実行

まずは、Stream Analytics ジョブを作成しましょう。 ジョブを作成したら、入力、出力、およびデータを取得するためのクエリを定義します。

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]** を選択します。 検索ボックスに「*Stream Analytics ジョブ*」と入力し、ドロップダウン リストから選択します。 **[Stream Analytics ジョブ]** 概要ページで、 **[作成]** を選択します

2. 次の情報をジョブに入力します。

   **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]** :IoT ハブと同じリソース グループを使用します。

   **[場所]** :リソース グループと同じ場所を使用します。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="Azure での Stream Analytics ジョブの作成":::

3. **［作成］** を選択します

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。

2. **[ジョブ トポロジ]** で、 **[入力]** を選択します。

3. **[入力]** ウィンドウで、 **[Add stream input]\(ストリーム入力の追加\)** を選択し、ドロップダウン リストから **[IoT Hub]** を選択します。 新しい入力ウィンドウで、次の情報を入力します。

   **入力のエイリアス**:入力の一意のエイリアスを入力します。

   **サブスクリプションから IoT Hub を選択する**: このラジオ ボタンを選択します。

   **サブスクリプション**:このチュートリアルに使用している Azure サブスクリプションを選択します。

   **IoT Hub**:このチュートリアルで使用している IoT Hub を選択します。

   **エンドポイント**: **[メッセージング]** を選びます。

   **共有アクセス ポリシー名**:Stream Analytics ジョブで IoT ハブに使用する共有アクセス ポリシーの名前を選択します。 このチュートリアルでは、*service* を選択できます。 *service* ポリシーは、新しい IoT ハブ上で既定で作成され、IoT ハブによって公開されるクライアント側エンドポイント上で送受信するためのアクセス許可を付与します。 詳細については、「[アクセス制御とアクセス許可](iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。

   **共有アクセス ポリシー キー**: このフィールドは、共有アクセス ポリシー名の選択内容に基づいて自動的に入力されます。

   **コンシューマー グループ**:以前に作成したコンシューマー グループを選びます。

   他のすべてのフィールドは既定値のままにします。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="Azure で Stream Analytics ジョブに入力を追加する":::

4. **[保存]** を選択します。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で、 **[出力]** を選択します。

2. **[出力]** ペインで **[追加]** を選択し、ドロップダウン リストから **[Power BI]** を選びます。

3. **[Power BI - New output]\(Power BI - 新規出力\)** ウィンドウで、 **[Authorize]\(承認\)** を選択し、指示に従って Power BI アカウントにサインインします。

4. Power BI にサインインした後、次の情報を入力します。

   **出力のエイリアス**:出力の一意のエイリアス。

   **グループ ワークスペース**:ターゲットのグループ ワークスペースを選択します。

   **データセット名**:データセットの名前を入力します。

   **テーブル名**:テーブルの名前を入力します。

   **認証モード**:既定値のままにします。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="Azure で Stream Analytics ジョブに出力を追加する":::

5. **[保存]** を選択します。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で、 **[クエリ]** を選択します。

2. `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。

3. `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。

1. クエリの最後の行として、次の `WHERE` 句を追加します。 この行により、**temperature** プロパティを持つメッセージだけが Power BI に転送されます。

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. クエリは次のスクリーンショットのようになります。 **[クエリの保存]** を選択します。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="Stream Analytics ジョブにクエリを追加する":::

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、 **[概要]** を選択してから、 **[開始]**  >  **[Now]\(今すぐ\)**  >  **[開始]** を選択します。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="Azure での Stream Analytics ジョブの実行":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>データを視覚化する Power BI レポートの作成と公開

次の手順では、Power BI サービスを使用してレポートの作成と公開を行う方法を示しています。 Power BI で "新しい外観" を使用する場合は、いくつかの変更を加えて次の手順を実行できます。 "新しい外観" の相違点と移動方法を理解するには、「[Power BI サービスの "新しい外観"](/power-bi/fundamentals/desktop-latest-update)」を参照してください。

1. デバイス上でクライアント アプリが実行されていることを確認します。

2. [Power BI](https://powerbi.microsoft.com/) アカウントにサインインし、上部のメニューから **[Power BI サービス]** を選択します。

3. 使用したワークスペースである **[マイ ワークスペース]** を右側のメニューから選択します。

4. **[すべて]** タブまたは **[Datasets + dataflows]\(データセット + データフロー\)** タブに、Stream Analytics ジョブの出力を作成したときに指定したデータセットが表示されます。

5. 作成したデータセットをポイントし、 **[その他のオプション]** メニュー (データセット名の右側にある 3 つのドット) を選択して、 **[レポートの作成]** を選択します。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="Microsoft Power BI レポートの作成":::

6. 時間の経過に伴う温度の変化を示す折れ線グラフを作成します。

   1. レポート作成ページの **[視覚化]** ウィンドウで、折れ線グラフのアイコンを選択して折れ線グラフを追加します。 グラフの辺と角にあるガイドを使用して、サイズと位置を調整します。

   2. **[フィールド]** ウィンドウで、Stream Analytics ジョブの出力を作成したときに指定したテーブルを展開します。

   3. **EventEnqueuedUtcTime** を、 **[視覚化]** ウィンドウの **[軸]** にドラッグします。

   4. **temperature** を **[値]** にドラッグします。

      折れ線グラフが作成されます。 x 軸は日付と時刻 (UTC タイム ゾーン) を示し、 y 軸はセンサーから取得した温度を示します。

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="Microsoft Power BI レポートに温度の折れ線グラフを追加する":::

     > [!NOTE]
     > テレメトリ データの送信に使用するデバイスまたはシミュレートされたデバイスによっては、フィールドの一覧が若干異なる場合があります。
     >

8. **[保存]** を選択してレポートを保存します。 メッセージが表示されたら、レポートの名前を入力します。 秘密度ラベルの入力を求めるメッセージが表示されたら、 **[パブリック]** を選択し、 **[保存]** を選択できます。

10. 引き続きレポート ペインで、 **[ファイル]**  >  **[Embed report]\(レポートを埋め込む\)**  >  **[Web サイトまたはポータル]** を選択します。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="Microsoft Power BI レポートの埋め込みレポート Web サイト選択する":::

    > [!NOTE]
    > 埋め込みコードを作成するには管理者に問い合わせることを求める通知が表示された場合は、管理者への連絡が必要である可能性があります。 この手順を完了する前に、埋め込みコードの作成を有効にする必要があります。
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="管理者への問い合わせを求める通知":::


11. 他のユーザーと共有できるレポート アクセス用のレポート リンクと、ブログまたは Web サイトにレポートを組み込むために使用できるコード スニペットが表示されます。 **[安全な埋め込みコード]** ウィンドウのリンクをコピーし、ウィンドウを閉じます。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="埋め込みレポート リンクのコピー":::

12. Web ブラウザーを開いて、リンクをアドレス バーに貼り付けます。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="Microsoft Power BI レポートの公開":::

Microsoft は [Power BI のモバイル アプリ](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/)も提供しています。これを使用すると、モバイル デバイスで Power BI のダッシュボードとレポートを表示して操作できます。

## <a name="cleanup-resources"></a>リソースをクリーンアップする

このチュートリアルでは、Power BI 内でリソース グループ、IoT ハブ、Stream Analytics ジョブ、およびデータセットを作成しました。 

他のチュートリアルを実行する予定がある場合は、リソース グループと IoT ハブをそのままにしておき、後で再利用します。 

IoT ハブまたは作成した他のリソースが不要になった場合は、ポータル内でリソース グループを削除できます。 そのためには、リソース グループを選択してから、 **[リソース グループの削除]** を選択します。 IoT ハブを保持する場合は、リソース グループの **[概要]** ペインから他のリソースを削除できます。 これを行うには、リソースを右クリックし、コンテキスト メニューから **[削除]** を選択して、プロンプトに従います。 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>Azure CLI を使用して Azure リソースをクリーンアップする

リソース グループとそのすべてのリソースを削除するには、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用します。

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>Power BI リソースをクリーンアップする

Power BI 内でデータセット **PowerBiVisualizationDataSet** を作成しました。 それを削除するには、[Power BI](https://powerbi.microsoft.com/) アカウントにサインインします。 左側のメニューの **[ワークスペース]** で、 **[マイ ワークスペース]** を選択します。 **[Datasets + dataflows]\(データセット + データフロー\)** タブの下にあるデータセットの一覧で、**PowerBiVisualizationDataSet** データセットの上にマウス ポインターを移動します。 データセット名の右側に表示される 3 つの垂直ドットを選択して **[その他のオプション]** メニューを開き、 **[削除]** を選択してプロンプトに従います。 データセットを削除すると、レポートも削除されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のタスクを実行することで、Power BI を使用して Azure IoT ハブからのリアルタイム センサー データを視覚化する方法について説明しました。

> [!div class="checklist"]
> * IoT ハブのコンシューマー グループを作成します。
> * コンシューマー グループから温度テレメトリを読み取って Power BI に送信する Azure Stream Analytics ジョブを作成して構成します。
> * Power BI 内で温度データのレポートを構成し、Web で共有します。

Azure IoT Hub からのデータを視覚化する別の方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Web アプリを使用した Azure IoT Hub からのリアルタイム センサー データの視覚化](iot-hub-live-data-visualization-in-web-apps.md)。