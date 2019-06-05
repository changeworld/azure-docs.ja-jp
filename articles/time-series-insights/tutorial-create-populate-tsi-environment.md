---
title: チュートリアル:Azure Time Series Insights 環境の作成 | Microsoft Docs
description: シミュレートされたデバイスからのデータが入力された Time Series Insights 環境を作成する方法について説明します。
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244159"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>チュートリアル:Azure Time Series Insights 環境を作成する

このチュートリアルでは、シミュレートされたデバイスのデータが入力される Azure Time Series Insights 環境を作成するプロセスについて説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Time Series Insights 環境を作成します。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Time Series Insights 環境にデータをストリーミングする。
> * シミュレートされたテレメトリ データを確認する。

## <a name="video"></a>ビデオ

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Azure IoT ソリューション アクセラレータを使用して、データを生成し、Time Series Insights の使用を開始する方法について説明します。 </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* また、Azure のサインイン アカウントは、サブスクリプションの**所有者**ロールのメンバーである必要があります。 詳細については、[ロールベースのアクセス制御と Azure portal を使用したアクセスの管理](/azure/role-based-access-control/role-assignments-portal)に関するページをご覧ください。

## <a name="overview"></a>概要

Time Series Insights 環境とは、デバイス データが収集、格納される場所のことです。 データが格納された後は、[Azure Time Series Insights エクスプローラー](time-series-quickstart.md)と [Time Series Insights クエリ API](/rest/api/time-series-insights/ga-query-api) を使用して、データのクエリおよび分析を行うことができます。 Azure IoT Hub は、Azure クラウドに安全に接続してデータを転送するために、すべての (シミュレートされた、または物理的な) デバイスによって使用される、コネクション ポイントです。 [Time Series Insights の概要](time-series-insights-overview.md)に関するページで説明されているように、Azure IoT Hub はデータを Time Series Insights 環境にストリーミングするためのイベント ソースとしても機能します。 このチュートリアルでは [IoT ソリューション アクセラレータ](/azure/iot-accelerators/)を使用し、サンプルのテレメトリ データを生成して IoT Hub にストリーミングします。

>[!TIP]
> IoT ソリューション アクセラレータによって、カスタム IoT ソリューションの開発を高速化するために使用できる、エンタープライズ レベルのあらかじめ構成されたソリューションが提供されます。

## <a name="create-an-environment"></a>環境の作成

まず、Azure サブスクリプションに Time Series Insights 環境を作成します。

1. Azure サブスクリプション アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。 
1. 左上隅の **[+ リソースの作成]** を選択します。 
1. **[モノのインターネット (IoT)]** カテゴリを選択し、 **[Time Series Insights]** を選択します。 

   [![Time Series Insights 環境リソースを選択する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. **[Time Series Insights 環境]** ページで、必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **環境名** | Time Series Insights 環境の一意の名前を選択します。 その名前は Time Series Insights エクスプローラーとクエリ API で使用されます。
   **サブスクリプション** | サブスクリプションとは、Azure リソース用のコンテナーです。 Time Series Insights 環境を作成するサブスクリプションを選択します。
   **リソース グループ** | リソース グループとは、Azure リソース用のコンテナーです。 Time Series Insights 環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。
   **場所** | Time Series Insights 環境のデータ センター リージョンを選択します。 帯域幅のコストや待機時間の増加を防ぐために、Time Series Insights 環境は他の IoT リソースと同じリージョンにします。
   **[Pricing SKU]\(価格 SKU\)** | 必要なスループットを選択します。 コストと初期容量を最小限に抑えるために、`S1` を選択します。
   **Capacity** | 容量は、イングレス レート、ストレージ容量、選択した SKU に関連するコストに適用される乗数です。 この容量は、作成後に変更できます。 コストを最小限に抑えるために、容量として 1 を選択します。

   完了したら、 **[作成]** を選択してプロビジョニング プロセスを開始します。

   [![Time Series Insights 環境リソースを作成する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. **[通知]** パネルを確認して、デプロイの完了を監視します。 

   [![Time Series Insights 環境のデプロイ成功](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

次に、Time Series Insights 環境に入力されるテスト データを生成する、デバイス シミュレーション ソリューションを作成します。

1. 別のウィンドウまたはタブで [azureiotsolutions.com](https://www.azureiotsolutions.com) に移動します。 同じ Azure サブスクリプション アカウントを使用してサインインし、 **[デバイス シミュレーション]** アクセラレータを選択します。

   [![デバイス シミュレーション アクセラレータを実行する](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **[ソリューション名]** | この一意の値は、新しいリソース グループを作成するために使用されます。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。
   **サブスクリプション** | 前のセクションで Time Series Insights 環境の作成に使用したものと同じサブスクリプションを指定します。
   **[リージョン]** | 前のセクションで Time Series Insights 環境の作成に使用したものと同じリージョンを指定します。
   **[オプションの Azure リソースをデプロイする]** | **[IoT Hub]** はオンのままにします。 シミュレートされたデバイスでは、それを使用してデータを接続またはストリーミングします。

   完了したら、 **[ソリューションの作成]** を選択してソリューションの Azure リソースをプロビジョニングします。 この処理が完了するまでに 6 分から 7 分かかることがあります。

   [![デバイス シミュレーション ソリューションをプロビジョニングする](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. プロビジョニング完了後、新しいソリューションの上にあるテキストが "**プロビジョニング中...** " から "**準備完了**" に変わります。

   >[!IMPORTANT]
   > **[起動]** はまだ選択しないでください。 後で戻ってくるので、この Web ページは開いたままにしておきます。

   [![デバイス シミュレーション ソリューションのプロビジョニング完了](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. これで Azure portal に戻り、サブスクリプション内に新しく作成されたリソースを検査します。 ポータルの **[リソース グループ]** ページに、前の手順で指定した **[ソリューション名]** を使用して作成した新しいリソース グループを確認できます。 また、デバイス シミュレーション ソリューションをサポートするように作成されたすべてのリソースを確認できます。

   [![デバイス シミュレーション ソリューションのリソース](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>IoT Hub に環境を接続する

ここまでで、2 セットのリソースを次のリソース グループ内に作成する方法について学習しました。

- 空の Time Series Insights 環境。
- ソリューション アクセラレータによって生成されたデバイス シミュレーション ソリューションのリソース (IoT ハブを含む)。

シミュレートされたデバイスは、IoT Hub に接続してデバイス データをストリーミングする必要があります。 Time Series Insights 環境にデータが流れるようにするには、IoT ハブと Time Series Insights 環境の両方の構成を変更する必要があります。

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT ハブの構成:コンシューマー グループを定義する

IoT Hub には、機能を他のアクターと共有する各種エンドポイントが用意されています。 "イベント" エンドポイントにより、データが IoT Hub のインスタンスにストリーミングされるときに、他のアプリケーションからそのデータを使用する方法が提供されます。 具体的には、"コンシューマー グループ" によって、IoT ハブからデータをリッスンしてプルするメカニズムがアプリケーションに提供されます。

次に、デバイス シミュレーション ソリューションの IoT ハブの**イベント エンドポイント**上に、新しい "**コンシューマー グループ**" プロパティを定義します。

1. Azure portal で、デバイス シミュレーション ソリューションに作成したリソース グループの **[概要]** ページに移動します。 IoT ハブ リソースを選択します。

   [![デバイス シミュレーション ソリューションのリソース グループ](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   ソリューション用に生成された IoT ハブ リソースの**名前**を書き留めておいてください。 後でそれを参照します。

1. 下にスクロールして **[エンドポイント]** ページを選択し、 **[イベント]** エンドポイントを選択します。 エンドポイントの **[プロパティ]** ページで、[$Default] コンシューマー グループの下のエンドポイントに一意の名前を入力します。 **[保存]** を選択します。

   [![デバイス シミュレーション ソリューションの IoT Hub のエンドポイント](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>環境の構成: イベント ソースを定義する

ここでは新しい IoT Hub **コンシューマー グループ**のイベント エンドポイントを Time Series Insights 環境に**イベント ソース**として接続します。

1. Time Series Insights 環境に作成したリソース グループの **[概要]** ページに移動します。 Time Series Insights 環境を選択します。

   [![Time Series Insights 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Time Series Insights 環境のページで、 **[イベント ソース]** を選択します。 次に、 **[+ 追加]** を選択します。

   [![Time Series Insights 環境の概要](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. **[新しいイベント ソース]** ページで、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **[イベント ソース名]** | イベント ソースの名前に使用される一意の値が必要です。
   **ソース** | **[IoT Hub]** を選択します。
   **[インポート オプション]** | 既定の `Use IoT hub from available subscriptions` を選択します。 このオプションにより、次のドロップダウン リストに使用可能なサブスクリプションが表示されます。
   **サブスクリプション** | Time Series Insights 環境とデバイス シミュレーションのリソースを作成したのと同じサブスクリプションを選択します。
   **[IoT Hub 名]** | 既定で前にメモした IoT Hub の名前が指定されます。 されていない場合は、正しい IoT Hub を選択します。
   **[IoT Hub ポリシー名]** | **[iothubowner]** を選びます。
   **[Iot Hub コンシューマー グループ]** | 既定で前にメモした IoT Hub コンシューマー グループの名前が指定されます。 されていない場合は、正しいコンシューマー グループ名を選択します。
   **イベントのシリアル化の形式** | 既定値の `JSON` をそのまま使用します。
   **[タイムスタンプのプロパティ名]** | `timestamp` として指定します。

   終わったら、 **[作成]** を選択してイベント ソースを追加します。 リソース グループの **[概要]** ページに戻ると、Time Series Insights 環境のリソースとともに、新しい「Time Series Insights イベント ソース」リソースが表示されます。

   [![Time Series Insights 環境の新しいイベント ソース](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>デバイス シミュレーションを実行してデータをストリーミングする

これですべての構成作業が完了しました。次は、シミュレートされたデバイスからのサンプル データを Time Series Insights 環境に入力します。

「[デバイス シミュレーションを作成する](#create-a-device-simulation)」セクションで、ソリューションをサポートする複数の Azure リソースがアクセラレータによって作成されました。 前に説明した IoT Hub とともに、シミュレートされたデバイス テレメトリを作成して転送する Azure App Service Web アプリケーションが生成されました。

1. [ソリューション アクセラレータのダッシュ ボード](https://www.azureiotsolutions.com/Accelerators#dashboard)に戻ります。 必要に応じて、このチュートリアルで使用している同じ Azure アカウントを使用して再度サインインします。 これで "デバイス シミュレーション" ソリューションの **[起動]** を選択できるようになります。

     [![ソリューション アクセラレータのダッシュボード](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. この時点でデバイス シミュレーション Web アプリが起動します。初期読み込みに数秒かかる場合があります。 また、Web アプリケーションに "サインインとプロファイルの読み取り" アクセス許可を付与することに同意するよう求められます。 このアクセス許可により、アプリケーションがアプリケーションの機能をサポートするのに必要なユーザー プロファイル情報を取得することを許可します。

     [![デバイス シミュレーション Web アプリケーションの同意](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. **[Simulation setup]\(シミュレーションのセットアップ\)** ページの読み込み後、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** を選択します。
   **[デバイス モデル]** | **[Chiller]** を選択します。
   **[Number of devices]\(デバイスの数\)**  | **[数量]** に `1000` と入力します。
   **[Telemetry frequency]\(テレメトリ頻度\)** | `10` 秒を入力します。
   **[Simulation duration]\(シミュレーション期間\)** | **[End in:]\(実行時間:\)** を選択し、`5` 分と入力します。

   完了したら、 **[シミュレーションの開始]** を選択します。 シミュレーションは、合計で 5 分間実行されます。 1,000 台のシミュレートされたデバイスから 10 秒ごとにデータを生成します。 

   [![デバイス シミュレーションのセットアップ](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. シミュレーションの実行中、約 10 秒ごとに **[Total messages]\(メッセージの総数\)** フィールドと **[Messages per second]\(1 秒ごとのメッセージ数\)** フィールドが更新されることに注目してください。 シミュレーションは約 5 分後に終了し、 **[Simulation setup]\(シミュレーションのセットアップ\)** に戻ります。

   [![実行中のデバイス シミュレーション](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>テレメトリ データを確認する

この最後のセクションでは、テレメトリ データが作成され、 Time Series Insights 環境に格納されたことを確認します。 データを確認するには、Time Series Insights エクスプローラーを使用します。これは、テレメトリ データのクエリと分析に使用します。

1. Time Series Insights 環境のリソース グループの **[概要]** ページに戻ります。 Time Series Insights 環境を選択します。

   [![Time Series Insights 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Time Series Insights 環境の **[概要]** ページで、 **[Time Series Insights エクスプローラーの URL]** を選択して Time Series Insights エクスプローラーを開きます。

   [![Time Series Insights エクスプローラー](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights エクスプローラーでは、Azure portal アカウントを使用して読み込みと認証が行われます。 最初のビューのグラフ領域で、シミュレートされたテレメトリ データが Time Series Insights 環境に入力されたことを確認できます。 時間の範囲を限定するには、左上隅のドロップダウンを選択します。 デバイス シミュレーションの期間が含まれる時間の範囲を入力します。 次に、検索の虫眼鏡をクリックします。

   [![Time Series Insights エクスプローラーの時間の範囲フィルター](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 時間の範囲を限定すると、IoT Hub や Time Series Insights 環境に対する明らかなデータ転送バーストの部分にグラフをズームインできます。 また、右上隅の "**Streaming complete**" (ストリーミングが完了しました) のテキストには、見つかったイベントの合計数が表示されます。 **[Interval size]\(間隔のサイズ\)** スライダーをドラッグして、グラフのプロット細分性を制御することもできます。

   [![Time Series Insights エクスプローラーの時間の範囲フィルターが適用されたビュー](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 最後に、リージョンを左クリックして、範囲をフィルター処理することもできます。 その後右クリックして **[イベントの探索]** を使用してタブ形式の **[イベント]** ビューでイベントの詳細を表示します。

   [![Time Series Insights エクスプローラーの時間の範囲フィルターが適用されたビューとイベント](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、稼働する Azure サービスをいくつか作成し、Time Series Insights 環境とデバイス シミュレーション ソリューションをサポートします。 このチュートリアル シリーズの作業を中止または延期する場合は、不要なコストが発生しないようにすべてのリソースを削除してください。

Azure portal の左側のメニューで、次のようにします。

1. **[リソース グループ]** アイコンを選択します。 次に、Time Series Insights 環境に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

1. **[リソース グループ]** アイコンを選択します。 次に、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Time Series Insights 環境を作成します。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Time Series Insights 環境にデータをストリーミングする。
> * シミュレートされたテレメトリ データを確認する。

独自の Time Series Insights 環境を作成する方法を確認したので、Time Series Insights 環境のデータを使用する Web アプリケーションを構築する方法について確認します。

> [!div class="nextstepaction"]
> [Azure Time Series Insights のシングルページ Web アプリを作成する](tutorial-create-tsi-sample-spa.md)
