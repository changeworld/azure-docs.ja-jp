---
title: Power BI での AI 推論イベントのリアルタイム視覚化
description: Azure Video Analyzer を使用して、継続的なビデオ記録やイベントベースの記録を行うことができます。 このチュートリアルでは、Microsoft Power BI の IoT Hub から AI 推論イベントをリアルタイムで視覚化する手順について説明します。
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 571e89e1616f8e7ab2ee0eca021ea1020dea9140
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564074"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>チュートリアル: Power BI での AI 推論イベントのリアルタイム視覚化

Azure Video Analyzer には、ライブ ビデオをキャプチャ、記録、分析する機能と共に、ビデオ分析の結果を AI 推論イベントの形式で [IoT Edge ハブ](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)に発行する機能が備わっています。 その後、これらの AI 推論イベントは、Visual Studio Code などの他の宛先や、Time Series Insights や Event Hubs などの Azure サービスにルーティングできます。

ダッシュボードは、ビジネスを監視し、重要なすべてのメトリックを一目で視覚化する洞察力の高い方法です。 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) 経由で [Microsoft Power BI](https://powerbi.microsoft.com/) を使用して、Video Analyzer によって生成された AI 推論イベントを視覚化し、分析情報をすばやく取得して、組織内の同僚とダッシュボードを共有することができます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.svg" alt-text="Azure Video Analyzer を Azure Stream Analytics 経由で Microsoft Power BI に接続するブロック図。":::

このチュートリアルでは、次の作業を行います。

- Stream Analytics ジョブを作成して実行し、IoT Hub から必要なデータを取得して Power BI に送信する
- 推論イベントを生成するライブ パイプラインを実行する
- AI 推論を視覚化する Power BI ダッシュボードを作成する

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

- Video Analyzer での[監視およびログ記録](monitor-log-edge.md)
- [IoT Hub の組み込みのエンドポイントから device-to-cloud メッセージを](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)読み取る
- [Power BI ダッシュボード](/power-bi/create-reports/service-dashboards)の概要

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- このチュートリアルでは、[ライン クロッシング サンプル](use-line-crossing.md)の使用に基づき、オブジェクトがライブ ビデオ フィード内の仮想線を超えたことを検出します。 他のパイプラインの視覚化を作成することもできます。**IoT Hub メッセージ シンクを持つパイプラインが必要です**。 ライブ パイプラインを作成しますが、そのアクティブ化は Stream Analytics ジョブの作成後にのみ行ってください。

  > [!TIP]
  >
  > - [ライン クロッシング サンプル](use-line-crossing.md)では 5 分間のビデオ録画が使用されます。 視覚化で最良の結果を得るには、[その他のデータセット](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset)から入手できる高速道路上の車両の 60 分間の録画を使用してください。
  > - rtsp シミュレーターにサンプル ビデオ ファイルを追加する方法については、[FAQ](faq-edge.yml) の「構成とデプロイ」セクションを参照してください。 追加したら、新しいビデオ ファイルを指すように `rtspUrl` 値を編集します。
  > - ライン クロッシング サンプルに従い、[AVA C# サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)を使用している場合は、properties -> parameters -> value にある operations.json ファイルを `"rtsp://rtspsim:554/media/camera-3600s.mkv"` に編集して、ビデオ ソースを 60 分の録画に変更します。

- [Power BI](https://powerbi.microsoft.com/) アカウント。

## <a name="create-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成と実行

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) は、データの高速移動ストリームを分析および処理するために設計された、フル マネージド リアルタイム分析サービスです。 このセクションでは、Stream Analytics ジョブを作成し、入力、出力、および必要なデータを取得するためのクエリを定義します。

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure Portal](https://portal.azure.com/) で、 **[リソースの作成]** を選択します。 検索ボックスに「_Stream Analytics ジョブ_」と入力し、ドロップダウン リストから選択します。 **[Stream Analytics ジョブ]** 概要ページで、 **[作成]** を選択します
2. ジョブの次の情報を入力します。

   - **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。
   - **サブスクリプション**: ライン クロッシング サンプルの設定に使用するのと同じサブスクリプションを選択します。
   - **リソース グループ**: ライン クロッシング サンプルの設定の一環として、IoT Edge ハブで使用されるのと同じリソース グループを使用します。
   - **[場所]** :リソース グループと同じ場所を使用します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="新しい Stream Analytics ジョブを作成するスクリーンショット。":::

3. **［作成］** を選択します

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。
2. [ジョブ トポロジ] で、 **[入力]** を選択します。
3. [入力] ウィンドウで、 **[Add stream input]\(ストリーム入力の追加\)** を選択し、ドロップダウン リストから **[IoT Hub]** を選択します。 新しい入力ウィンドウで、次の情報を入力します。

   - **入力のエイリアス**: 入力の一意のエイリアス ("iothubinput" など) を入力します。
   - **サブスクリプションから IoT Hub を選択する**: このラジオ ボタンを選択します。
   - **サブスクリプション**: この記事のために使用している Azure サブスクリプションを選択します。
   - **IoT Hub**: ライン クロッシング サンプルの設定に使用する IoT Hub を選択します。
   - **共有アクセス ポリシー名**:Stream Analytics ジョブで IoT ハブに使用する共有アクセス ポリシーの名前を選択します。 このチュートリアルでは、iothubowner を選択できます。 詳細については、「[アクセス制御とアクセス許可](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
   - **共有アクセス ポリシー キー**: このフィールドは、共有アクセス ポリシー名の選択内容に基づいて自動的に入力されます。

   他のすべてのフィールドは、既定値に設定したままにします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="IoT Hub 入力を Stream Analytics ジョブに追加するスクリーンショット。":::

4. **[保存]** を選択します。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. [ジョブ トポロジ] で、 **[出力]** を選択します。
2. [出力] ペインで **[追加]** を選択し、ドロップダウン リストから **[Power BI]** を選びます。
3. Power BI の [新しい出力] ウィンドウで、次の操作を行います。

   - **出力のエイリアス**: 出力の一意のエイリアス ("powerbioutput" など) を入力します。
   - **グループ ワークスペース**:ターゲットのグループ ワークスペースを選択します。
   - **認証モード**: テスト目的では、既定の "ユーザー トークン" のままにします。

   > [!NOTE]
   > 運用環境のジョブの場合は、[マネージド ID を使用して、Power BI に対して Stream Analytics ジョブを認証する](../../stream-analytics/powerbi-output-managed-identity.md)ことをお勧めします。

   - **データセット名**:データセットの名前を入力します。
   - **テーブル名**:テーブルの名前を入力します。
   - **承認**: [承認する] を選択し、プロンプトに従って Power BI アカウントにサインインします (トークンは 90 日間有効です)。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="Power BI 出力を Stream Analytics ジョブに追加するスクリーンショット。":::

4. **[保存]** を選択します。

   > [!NOTE]
   > Stream Analytics ジョブの出力として Power BI を使用する方法の詳細については、[こちら](../../stream-analytics/power-bi-output.md)をクリックしてください。 Power BI アカウントの[認証の更新](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization)の詳細を確認してください。

### <a name="configure-the-query-for-stream-analytics-job"></a>Stream Analytics ジョブのクエリを構成する

1. [ジョブ トポロジ] で、 **[クエリ]** を選択します。
2. クエリに次の変更を加えます。

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - 上記のクエリで、FROM 句の _i_ は、_inferences_ 配列に入れ子にされていない EventProcessedUtcTime の値をフェッチするために構文的に必要です。
   >   上記のクエリは、[ライン クロッシング チュートリアル](use-line-crossing.md)の AI 推論を取得するためにカスタマイズされています。
   > - 別のパイプラインを実行している場合は、パイプラインの AI 推論スキーマに従ってクエリをカスタマイズしてください。 [Stream Analytics ジョブでの JSON の解析](../../stream-analytics/stream-analytics-parsing-json.md)について詳細を確認してください。

3. [YourOutputAlias] を、「Stream Analytics ジョブへの出力の追加」のステップで使用した出力エイリアス ("powerbioutput" など) に置き換えます。 出力と入力のエイリアスが正しい順序になるよう注意してください。
4. [YourInputAlias] を、「Stream Analytics ジョブへの入力の追加」のステップで使用した入力エイリアス ("iothubinput" など) に置き換えます。
5. クエリは次のスクリーンショットのようになります。 **[クエリのテスト]** をクリックして、EventTotal と対応する EventProcessedUtcTime 値のテーブルとしてのテスト結果を確認します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="Stream Analytics ジョブでクエリをテストして保存するスクリーンショット。":::

6. **[クエリの保存]** を選択します。

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、[概要] を選択してから、 **[開始]**  >  **[Now]\(今すぐ\)**  >  **[開始]** を選択します。 ジョブが正常に開始されると、ジョブの状態が [作成済み] から **[実行中]** に変わります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="Stream Analytics ジョブを開始して実行するスクリーンショット。":::

> [!TIP]
> IoT Hub では、組み込みの Event Hubs に既定で 1 日間、最大 7 日間データを保持できます。 保持期間は IoT Hub の作成時に設定できます。 IoT Hub でのデータ保持期間は、選択したサービス レベルとユニットの種類によって異なります。 詳細については、[こちら](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)をクリックしてください。 データの保持期間を長くするには、[Azure Storage を出力として](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md)使用し、Power BI をストレージ アカウント内のファイルに接続します。

## <a name="run-a-sample-pipeline"></a>サンプル パイプラインの実行

上記のステップで作成された Stream Analytics ジョブが **実行中** 状態のときに、ライン クロッシング チュートリアルの「[サンプル プログラムを実行する](use-line-crossing.md#run-the-sample-program)」セクションに移動し、ライブ パイプラインをアクティブ化します。 ライブ パイプラインにより、AI 推論の結果が IoT Hub に送信され始め、これらは Stream Analytics ジョブによって取得されます。

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>AI イベントを視覚化するための Power BI ダッシュボードの作成

Power BI では、2 つの方法でストリーミング データを視覚化できます。

1. ストリーミング データセット用に作成されたテーブルからレポートを作成し、ダッシュボードにピン留めする
2. カスタム ストリーミング データセットを含むダッシュボード タイル

   > [!NOTE]
   > この記事では、最初の方法を使用してレポートを作成し、ダッシュボードにピン留めします。 この方法では、ビジュアル データを長期間保持し、受信データに基づいて自動的に集計します。 2 つ目の方法の詳細については、[Power BI でのカスタム ストリーミング データセットのセットアップ](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)に関する記事を参照してください。

### <a name="create-and-publish-a-power-bi-report"></a>Power BI レポートの作成と発行

次の手順では、Power BI サービスを使用してレポートの作成と公開を行う方法を示しています。

1. (パイプラインを実行するために前の手順で開始された) デバイスで、サンプル パイプラインがアクティブ化されていることを確認します。
2. [Power BI アカウント](https://powerbi.microsoft.com/)にサインインし、上部のメニューから **[Power BI サービス]** を選択します。
3. 使用したワークスペースを横のメニューから選択します。
4. **[すべて]** タブまたは **[データセット + データフロー]** タブに、「_Stream Analytics ジョブへの出力の追加_」のステップで指定したデータセットが表示されます。
5. Stream Analytics 出力を作成したときに指定したデータセット名をポイントし、 **[その他のオプション]** メニュー (データセット名の右側にある 3 つのドット) を選択して、 **[レポートの作成]** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="Power BI でレポートを作成するスクリーンショット。":::

6. 折れ線グラフを作成して、線を越えるイベントをリアルタイムで表示します。

   - レポート作成ページの **[視覚化]** ウィンドウで、**折れ線グラフ** のアイコンを選択して折れ線グラフを追加します。
   - **[フィールド]** ウィンドウで、Stream Analytics ジョブの出力を作成したときに指定したテーブルを展開します。
   - **EventProcessedUtcTime** を、 **[視覚化]** ウィンドウの **[軸]** にドラッグします。
   - **EventTotal** を **[値]** にドラッグします。
   - 折れ線グラフが作成されます。 x 軸は日付と時刻 (UTC タイム ゾーン) を示し、 y 軸には、ライブ パイプラインからの EventTotal の **合計** の自動集計値が表示されます。 [値] を EventTotal の **最大** に変更して、EventTotal の最新の値を取得します。 集計関数を変更して、平均や個数 (個別) などを表示できます。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Power BI でのライン クロッシング レポートのスクリーンショット。":::

7. 右上の **[保存]** をクリックしてレポートを保存します。

### <a name="pin-visual-to-a-dashboard"></a>ダッシュボードにビジュアルをピン留めする

右上にある **[Pin to a dashboard]\(ダッシュボードにピン留めする\)** をクリックし、このビジュアルをピン留めする場所 (既存のダッシュボードまたは新しいダッシュボード) を選択し、それに応じてプロンプトに従います。

必要に応じて、[プレーヤー ウィジェットを埋め込み](embed-player-in-power-bi.md)、レポートと並べてビデオ再生を表示することもできます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="レポートがピン留めされ、プレーヤー ウィジェットがタイルに追加された Power BI ダッシュボード。":::

> [!NOTE]
> レポートの生成に使用される方法により、ビデオの再生とレポートの作成は同期されません。 よりリアルタイムに近いエクスペリエンスについては、[Power BI でのカスタム ストリーミング データセットの設定](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)に関する記事を参照してください。

## <a name="interpret-the-dashboard"></a>ダッシュボードの解釈

lineCoordinates パラメーターを使用してトポロジで指定された線をオブジェクトが越えると、ライン クロッシング プロセッサ ノードによって検出されます。 オブジェクトがこれらの座標を越えると、以下を含むイベントがトリガーされます。

- EventTotal: ビデオの開始以降、任意のオブジェクトが任意の方向 (時計回りまたは反時計回り) で線を超えた回数の合計。 詳しくは、[ライン クロッシング イベントの推論](use-line-crossing.md#line-crossing-events)に関する記事を参照してください。
- イベントが処理された UTC 時刻: 各イベントが Stream Analytics ジョブによって処理された時刻。 または、Stream Analytics クエリをカスタマイズして、イベントが IoT Hub に到達して追加の処理が可能になった時刻を示す EventEnqueuedUtcTime をフェッチすることもできます。

上記のダッシュボードでは、時間の経過に伴い仮想線を越えるオブジェクトが増え、**EventTotal** の数が増えていくのを確認できます。 この視覚化により、高速道路を通る車両が、たとえば午後 3:52:00 から 3:53:30 の間に増加することがすばやく識別できます。 その後、これらの分析情報を使用して、高速道路の特定の時間に交通渋滞が発生する理由に関して、分析を絞り込むことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal のリソース グループに移動し、この記事の前提条件を実行して Stream Analytics ジョブを作成したリソース グループを選択して、 **[リソース グループの削除]** を選択します。

Power BI でデータセット **LineCrossingDataset** を作成しました。 それを削除するには、[Power BI](https://powerbi.microsoft.com/) アカウントにサインインします。 左側のメニューの **[ワークスペース]** で、使用したワークスペースを選択します。 **[データセット + データフロー]** タブの下にあるデータセットの一覧で、データセットの上にマウス ポインターを移動します。 データセット名の右側に表示される 3 つの垂直ドットを選択して **[その他のオプション]** メニューを開き、 **[削除]** を選択してプロンプトに従います。 データセットを削除すると、レポートも削除されます。

## <a name="next-steps"></a>次のステップ

- [プレーヤー ウィジェットを Power BI に埋め込む](embed-player-in-power-bi.md)ことによって、ビデオ再生とテレメトリを組み合わせます。
- [イベントの監視とログ記録](monitor-log-edge.md)に関するページで詳細を確認します。
