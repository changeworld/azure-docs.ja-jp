---
title: 車両の状態と運転習慣を予測する方法の詳細 - Azure | Microsoft Docs
description: Cortana Intelligence の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの予測的な洞察を得ます。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 8c4946ebef8d17d2016d482010768207d5e859ff
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300950"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>車両テレメトリ分析ソリューション プレイブック: ソリューションの詳細

この記事では、ソリューション アーキテクチャで示されている各段階について詳しく説明します。 カスタマイズのための説明とアドバイスが含まれています。 

このソリューションの概要説明を確認するには、「[車両テレメトリ分析ソリューション プレイブック](cortana-analytics-playbook-vehicle-telemetry.md)」をご覧ください。


## <a name="data-sources"></a>データ ソース
このソリューションでは、次の 2 つのデータ ソースを使用します。

* シミュレートされた車両信号と診断データセット
* 車両カタログ

次のスクリーンショットで示されているように、車両テレマティックス シミュレーターは、このソリューションの一部として含まれています。 診断情報と、特定の時点での車両の状態と運転パターンに対応する信号を出力します。  車両カタログには、車両識別番号 (VIN) をモデルにマップする参照データ セットが含まれています。 注: 現在、車両テレマティックス シミュレーター Visual Studio ソリューションのデータ セットは、提供されていません。 

![車両テレマティックス シミュレーター](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


JSON 形式のこのデータ セットには、次のスキーマが含まれています。

| 列 | 説明 | 値 |
| --- | --- | --- |
| VIN |ランダムに生成された VIN |ランダムに生成された 10,000 個の VIN が含まれているマスター リストから取得される |
| Outside temperature |車両運転時の外部温度 |ランダムに生成された 0 - 100 の数値 |
| Engine temperature |車両のエンジン温度 |ランダムに生成された 0 - 500 の数値 |
| 速度 |車両運転時のエンジン回転数 |ランダムに生成された 0 - 100 の数値 |
| Fuel |車両の燃料レベル |ランダムに生成された 0 - 100 の数値 (燃料レベルの割合を示す) |
| EngineOil |車両のエンジン オイル レベル |ランダムに生成された 0 - 100 の数値 (エンジン オイル レベルの割合を示す) |
| タイヤ空気圧 |車両のタイヤ空気圧 |ランダムに生成された 0 - 50 の数値 (タイヤ空気圧レベルの割合を示す) |
| Odometer |車両の走行距離計の数値 |ランダムに生成された 0 - 200,000 の数値 |
| Accelerator_pedal_position |車両のアクセル ペダルの位置 |ランダムに生成された 0 - 100 の数値 (アクセル レベルの割合を示す) |
| Parking_brake_status |車両が駐車されているかどうか |True または False |
| Headlamp_status |ヘッドライトがついているかどうか |True または False |
| Brake_pedal_status |ブレーキ ペダルが踏まれているかどうか |True または False |
| Transmission_gear_position |車両のギアの位置 |状態: first、second、third、fourth、fifth、sixth、seventh、eighth |
| Ignition_status |車両が走行しているか停車しているか |True または False |
| Windshield_wiper_status |フロント ガラス ワイパーが動作しているかどうか |True または False |
| ABS |ABS が動作中かどうか |True または False |
| Timestamp |データ ポイントが作成されたときのタイムスタンプ |日付 |
| City |車両の場所 |このソリューションでは、ベルビュー、レドモンド、サマミッシュ、シアトルの 4 つの都市 |

車両モデルの参照データ セットは、VIN をモデルにマップします。 

| VIN | モデル |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |セダン |
| 8J0U8XCPRGW4Z3NQE |ハイブリッド |
| WORG68Z2PLTNZDBI7 |ファミリー サルーン |
| JTHMYHQTEPP4WBMRN |セダン |
| W9FTHG27LZN1YWO0Y |ハイブリッド |
| MHTP9N792PHK08WJM |ファミリー サルーン |
| EI4QXI2AXVQQING4I |セダン |
| 5KKR2VB4WHQH97PF8 |ハイブリッド |
| W9NSZ423XZHAONYXB |ファミリー サルーン |
| 26WJSGHX4MA5ROHNL |コンバーチブル |
| GHLUB6ONKMOSI7E77 |ステーション ワゴン |
| 9C2RHVRVLMEJDBXLP |コンパクト カー |
| BRNHVMZOUJ6EOCP32 |小型 SUV |
| VCYVW0WUZNBTM594J |スポーツ カー |
| HNVCE6YFZSA5M82NY |中型 SUV |
| 4R30FOR7NUOBL05GJ |ステーション ワゴン |
| WYNIIY42VKV6OQS1J |大型 SUV |
| 8Y5QKG27QET1RBK7I |大型 SUV |
| DF6OX2WSRA6511BVG |クーペ |
| Z2EOZWZBXAEW3E60T |セダン |
| M4TV6IEALD5QDS3IR |ハイブリッド |
| VHRA1Y2TGTA84F00H |ファミリー サルーン |
| R0JAUHT1L1R3BIKI0 |セダン |
| 9230C202Z60XX84AU |ハイブリッド |
| T8DNDN5UDCWL7M72H |ファミリー サルーン |
| 4WPYRUZII5YV7YA42 |セダン |
| D1ZVY26UV2BFGHZNO |ハイブリッド |
| XUF99EW9OIQOMV7Q7 |ファミリー サルーン |
| 8OMCL3LGI7XNCC21U |コンバーチブル |
| ……. | |

## <a name="ingestion"></a>データの取り込み
車両信号、診断イベント、リアルタイム分析、一括分析を取り込むために、Azure Event Hubs、Azure Stream Analytics、Azure Data Factory の組み合わせが使用されます。 これらのすべてのコンポーネントは、ソリューション デプロイの一部として作成および構成されます。 

### <a name="real-time-analysis"></a>リアルタイム分析
車両テレマティックス シミュレーターによって生成されたイベントは、Event Hub SDK を使用してイベント ハブに公開されます。  

![Event Hub ダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Stream Analytics ジョブは、イベント ハブからこれらのイベントを取り込み、リアルタイムでデータを処理して、車両の状態を分析します。

![Stream Analytics ジョブによるデータの処理](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Stream Analytics ジョブは以下の処理を行います。

* イベント ハブからデータを取り込む
* 参照データと結合して、車両 VIN を対応するモデルにマップする 
* それらを Azure Blob Storage に保持して、さまざまな一括分析で利用できるようにする 

以下の Stream Analytics クエリを使用して、データを Blob Storage に保持します。 

![データを取り込むための Stream Analytics ジョブ クエリ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>一括分析
さまざまな一括分析のために、シミュレートされた車両信号と診断データセットの追加のボリュームも生成されます。 この追加のボリュームは、バッチ処理で適切な代表的データ ボリュームを使用するために必要です。 そのために、Data Factory ワークフローで PrepareSampleDataPipeline を使用して、1 年分のシミュレートされた車両信号と診断データセットが生成されます。 Data Factory カスタム .NET アクティビティ Visual Studio ソリューションをダウンロードし、要件に基づいてカスタマイズするには、[Data Factory カスタム アクティビティ](https://go.microsoft.com/fwlink/?LinkId=717077)に関する Web ページをご覧ください。 

このワークフローでは、バッチ処理用に準備されているサンプル データを示します。

![バッチ処理ワークフロー用に準備されているサンプル データ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


パイプラインは、カスタム Data Factory .NET アクティビティで構成されています。

![PrepareSampleDataPipeline アクティビティ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

パイプラインが正常に実行され、RawCarEventsTable データ セットが "Ready" としてマークされると、1 年分のシミュレートされた車両信号と診断データが生成されます。 ストレージ アカウントの connectedcar コンテナーの下に、次のフォルダーとファイルが作成されます。

![PrepareSampleDataPipeline の出力](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>データ セットの分割
データ準備の手順で、未加工の半構造化された車両信号と診断データセットは、YEAR/MONTH 形式で分割されます。 この分割により、クエリが効率化されます。また、フェールオーバーが可能になるため、スケーラブルに長期保存できるようになります。 たとえば、最初の BLOB アカウントがいっぱいになったら次のアカウントにフェールオーバーします。 

>[!NOTE] 
>ソリューションのこの手順は、バッチ処理の場合にだけ行います。

入出力データの管理:

* **出力データ** (ラベルは PartitionedCarEventsTable) は、基本的な "最も加工されていない" 形式のデータとして、顧客の Data Lake に長期間保存されます。 
* 出力データは入力に忠実であるため、このパイプラインへの**入力データ**は、通常は破棄されます。 後続の使用に役立つように保存 (分割) されます。

車両イベントの分割のワークフロー。

![車両イベントの分割のワークフロー](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


生データは、次のスクリーン ショットに示すように、PartitionCarEventsPipeline 内の Azure HDInsight Hive アクティビティを使用して分割されます。 データ準備の手順で生成された 1 年間のサンプル データは、YEAR/MONTH 形式で分割されます。 分割は、1 年の各月の車両信号と診断データの生成に使用されます (合計 12 個の分割)。 

![PartitionCarEventsPipeline アクティビティ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**PartitionConnectedCarEvents Hive スクリプト**

分割には、Hive スクリプト partitioncarevents.hql を使用します。 これは、ダウンロードした zip ファイルの \demo\src\connectedcar\scripts フォルダーにあります。 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

パイプラインが正常に実行されると、ストレージ アカウントの connectedcar コンテナーの下に、次の分割が生成されます。

![分割された出力](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

これで、データは最適化され、管理しやすくなり、さらに処理を進めて、分析情報をまとめて、多量に入手する準備ができました。 

## <a name="data-analysis"></a>データ分析
このセクションでは、Stream Analytics、Azure Machine Learning、Data Factory、HDInsight を組み合わせて、車両の状態と運転習慣に対してさまざまな高度な分析を行う方法について説明します。

### <a name="machine-learning"></a>機械学習
ここでの目的は、次のことを前提とし、特定の状態統計データに基づいて、メンテナンスやリコールを必要とする車両を予測することです。

* 次の 3 つの条件のいずれかに該当する場合、車両はサービス メンテナンスを必要とします。
  
  * タイヤ空気圧が低い
  * エンジン オイルのレベルが低い
  * エンジン温度が高い

* 次の条件のいずれかに該当する場合、車両は安全性に問題があり、リコールを必要とします。
  
  * エンジン温度が高いが、外気温が低い
  * エンジン温度が低いが、外気温が高い

前の要件に基づいて、2 つの別個のモデルが異常を検出します。 1 つのモデルは車両メンテナンス検出用で、1 つのモデルは車両リコール検出用です。 どちらのモデルでも、異常検出のために、組み込みの主成分分析 (PCA) アルゴリズムが使用されています。 

#### <a name="maintenance-detection-model"></a>**メンテナンス検出モデル**

3 つのインジケーター (タイヤ空気圧、エンジン オイル、エンジン温度) のいずれかがそれぞれの条件を満たす場合、メンテナンス検出モデルは異常を報告します。 そのため、モデルの構築では、これら 3 つの変数だけを考慮すれば済みます。 機械学習での実験では、**データセット内の列の選択**モジュールを使用して、この 3 つの変数を抽出します。 次に、PCA ベースの異常検出モジュールを使用して、異常検出モデルを構築します。 

PCA は、機能の選択、分類、異常検出に使用できる、機械学習における確立された手法です。 PCA では、関連を持つ可能性がある変数を含むケースのセットを、主成分と呼ばれる値のセットに変換します。 PCA ベースのモデル構築の基本的な目的は、機能や異常をより簡単に識別できるように、データをより低い次元の空間に投影することです。

検出モデルへの新しい入力のそれぞれに対して、異常検出機能はまず、固有ベクトルへの投影を計算します。 次に、正規化された再構成エラーを計算します。 この正規化されたエラーは異常性スコアであり、エラーが重大なほど、インスタンスの異常性が高くなります。 

メンテナンス検出の問題では、各レコードをタイヤ空気圧、エンジン オイル、エンジン温度の座標によって定義される 3 次元空間内の点として考えることができます。 これらの異常をキャプチャするには、PCA を使用して、3 次元空間内の元データを 2 次元空間に投影します。 そのため、PCA で使用する number of components パラメーターは 2 に設定されます。 このパラメーターは、PCA ベースの異常検出を適用するときに、重要な役割を果たします。 PCA を使用してデータを投影すると、これらの異常がより簡単に識別されます。

#### <a name="recall-anomaly-detection-model"></a>**リコール異常検出モデル**

リコール異常検出モデルでは、**データセット内の列の選択**モジュールと PCA ベースの異常検出モジュールを同じような方法で使用します。 具体的には、まず、**データセット内の列の選択**モジュールを使用して、3 つの変数 (エンジン温度、外気温、速度) を抽出します。 エンジン温度は、通常、速度と関連しているため、速度の変数も含めます。 次に、PCA ベースの異常検出モジュールを使用して、3 次元空間から 2 次元空間にデータを投影します。 リコール条件が満たされています。 エンジン温度と外気温が大きく負相関している場合、車両のリコールが必要になります。 PCA の実行後、PCA ベースの異常検出アルゴリズムを使用して、異常をキャプチャします。 

どちらかのモデルをトレーニングするときは、通常データを入力データとして使用して、PCA ベースの異常検出モデルをトレーニングします。 (通常データは、メンテナンスまたはリコールを必要としません。)スコア付け実験では、トレーニング済みの異常検出モデルを使用して、車両がメンテナンスやリコールを必要とするかどうかを調べます。 

### <a name="real-time-analysis"></a>リアルタイム分析
次の Stream Analytics SQL クエリを使用して、すべての重要な車両パラメーターの平均値を取得できます。 これらのパラメーターには、車両速度、燃料レベル、エンジン温度、走行距離計の数値、タイヤ空気圧、エンジン オイル レベルなどがあります。 この平均値を使用して、異常を検出し、アラートを発し、特定の地域で運転されている車両の全体的な状態を判断します。 平均値は、その後、人口統計に関連付けられます。 

![リアルタイム処理のための Stream Analytics クエリ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

すべての平均値は、3 秒間のタンブリング ウィンドウで計算されます。 タンブリング ウィンドウが使用されるのは、重複しない、連続した期間が必要であるためです。 

Stream Analytics のウィンドウ化機能の詳細については、「[Windowing (Azure Stream Analytics) (ウィンドウ化 (Azure Stream Analytics))](https://msdn.microsoft.com/library/azure/dn835019.aspx)」をご覧ください。

#### <a name="real-time-prediction"></a>**リアルタイムの予測**

機械学習モデルをリアルタイムで運用できるようにするアプリケーションが、ソリューションの一部として含まれています。 RealTimeDashboardApp というアプリケーションが、ソリューションのデプロイの一環として作成および構成されます。 アプリケーションでは

* Stream Analytics があるパターンで連続的にイベントを発行しているイベント ハブ インスタンスをリッスンします。

    ![データを公開するための Stream Analytics クエリ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* イベントを受け取ります。 このアプリケーションが受け取るすべてのイベントに対して、次の処理を行います。 
   
   * データは、Machine Learning Request-Response Scoring (RRS) エンドポイントを使用して処理されます。 RRS エンドポイントは、デプロイの一環として自動的に発行されます。
   * RRS 出力は、プッシュ API を使用して Power BI データ セットに発行されます。

このパターンは、基幹業務アプリケーションをリアルタイム分析フローと統合するシナリオにも適用できます。 これらのシナリオには、アラート、通知、メッセージングがあります。

注: 現在、RealtimeDashboardApp Visual Studio ソリューションのデータは、提供されていません。

#### <a name="execute-the-real-time-dashboard-application"></a>**リアルタイム ダッシュボード アプリケーションを実行する**
1. RealtimeDashboardApp を展開し、ローカルに保存します。

    ![RealTimeDashboardApp フォルダー](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. RealtimeDashboardApp.exe アプリケーションを実行します。

3. ご自分の有効な Power BI 資格情報を入力し、**[サインイン]** を選択します。  

    ![リアルタイム ダッシュボード アプリのサインイン ウィンドウ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. **[Accept]\(承認\)** を選択します。

    ![リアルタイム ダッシュボード アプリの最終的なサインイン ウィンドウ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Power BI データセットをフラッシュする場合は、"flushdata" パラメーターを指定して RealtimeDashboardApp を実行します。 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>一括分析
ここでの目的は、どのように Contoso Motors が Azure のコンピューティング機能を利用してビッグ データを活用しているかを説明することです。 このデータにより、運転パターン、利用行動、車両の状態に関する豊富な分析情報が得られます。 この情報により、次のことができるようになります。

* カスタマー エクスペリエンスを向上させ、運転の習慣や燃費のよい運転行動についての知見を提供してコストを下げる。
* 顧客とその運転パターンについて事前に学習して、ビジネスの意思決定を制御したり、最高クラスの製品やサービスを提供したりする。

このソリューションでは、以下のメトリックを対象にします。

* **アグレッシブな運転行動**: モデル、場所、運転条件、時期の傾向を識別して、アグレッシブな運転パターンを把握します。 Contoso Motors は、パーソナライズされた新機能、および走行距離ベースの保険料を紹介するマーケティング キャンペーンにこれらの分析情報を利用できます。
* **低燃費な運転行動**: モデル、場所、運転条件、時期の傾向を識別して、低燃費な運転パターンを把握します。 Contoso Motors は、新機能、および低コストで環境にやさしい運転習慣についての運転者への事前報告を紹介するマーケティング キャンペーンにこれらの知見を利用できます。
* **リコール モデル**: 異常検出の機械学習実験を実施して、リコールを必要とするモデルを識別します。

次に、各メトリックの詳細を説明します。

#### <a name="aggressive-driving-behavior-patterns"></a>**アグレッシブな運転行動パターン**

分割された車両信号と診断データは、次のワークフローで示すように、AggresiveDrivingPatternPipeline で処理されます。 Hive を使用して、アグレッシブな運転パターンを示すモデル、場所、車両、運転条件などのパラメーターが特定されます。

![アグレッシブな運転パターンのワークフロー](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***アグレッシブな運転パターンの Hive クエリ***

アグレッシブな運転条件パターンを分析するには、Hive スクリプト aggresivedriving.hql を使用します。 これは、ダウンロードした zip ファイルの \demo\src\connectedcar\scripts フォルダーにあります。 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


このスクリプトは、車両のギアの位置、ブレーキ ペダルの状態、速度の組み合わせを使用して、高速度でのブレーキ使用パターンに基づいた無謀/アグレッシブな運転行動を検出します。 

パイプラインが正常に実行されると、ストレージ アカウントの connectedcar コンテナーの下に、次の分割が生成されます。

![AggressiveDrivingPatternPipeline の出力](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**低燃費な運転行動パターン**

分割された車両信号と診断データは、次のワークフローで示すように、FuelEfficientDrivingPatternPipeline で処理されます。 Hive を使用して、低燃費な運転パターンを示すモデル、場所、車両、運転条件などのプロパティが特定されます。

![低燃費な運転パターン](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***低燃費な運転パターンの Hive クエリ***

低燃費な運転条件パターンを分析するには、Hive スクリプト fuelefficientdriving.hql を使用します。 これは、ダウンロードした zip ファイルの \demo\src\connectedcar\scripts フォルダーにあります。 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


このスクリプトは、車両のギアの位置、ブレーキ ペダルの状態、速度、アクセル ペダルの位置の組み合わせを使用して、加速度、ブレーキ使用、速度のパターンに基づいて低燃費な運転行動を検出します。 

パイプラインが正常に実行されると、ストレージ アカウントの connectedcar コンテナーの下に、次の分割が生成されます。

![FuelEfficientDrivingPatternPipeline の出力](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**リコール モデルの予測**

機械学習実験はプロビジョニングされ、ソリューション デプロイの一環として、Web サービスとして発行されます。 このワークフローでは、バッチ スコアリング エンドポイントが使用されます。 これは、データ ファクトリにリンクされているサービスとして登録されており、データ ファクトリのバッチ スコアリング アクティビティを使用して動作可能になっています。

![Machine Learning のエンドポイント](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

登録されているリンクされたサービスは、異常検出モデルを使用してデータにスコアを付けるために、DetectAnomalyPipeline で使用されます。 

![データ ファクトリでの Machine Learning バッチ スコアリング アクティビティ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

このパイプラインで、バッチ スコアリング Web サービスとの運用が可能になるようにデータを準備するいくつかの手順を実行します。 

![リコール予測のための DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***異常を検出する Hive クエリ***

スコア付けが完了すると、HDInsight アクティビティは、モデルが異常として分類したデータを処理して集計します。 モデルでは、0.60 以上の確率スコアが使用されます。

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


パイプラインが正常に実行されると、ストレージ アカウントの connectedcar コンテナーの下に、次の分割が生成されます。

![DetectAnomalyPipeline の出力](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>[発行]

### <a name="real-time-analysis"></a>リアルタイム分析
Stream Analytics ジョブのクエリの 1 つでは、出力のイベント ハブ インスタンスにイベントが発行されます。 

![Stream Analytics ジョブから出力のイベント ハブ インスタンスへの発行](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

出力のイベント ハブ インスタンスに発行するために、次の Stream Analytics クエリが使用されます。

![出力のイベント ハブ インスタンスに発行するための Stream Analytics クエリ](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

イベントのこのストリームは、ソリューションに含まれている RealTimeDashboardApp で使用されます。 このアプリケーションでは、リアルタイム のスコア付けのために機械学習要求 - 応答 Web サービスが使用されます。 結果のデータが、Power BI データ セットでの使用のために発行されます。 

### <a name="batch-analysis"></a>一括分析
バッチおよびリアルタイム処理の結果は、Azure SQL Database テーブルでの使用のために発行されます。 SQL Server、データベース、テーブルは、セットアップ スクリプトの一部として自動的に作成されます。 

バッチ処理の結果は、データ マート ワークフローにコピーされます。

![データ マート ワークフローへのバッチ処理結果のコピー](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Stream Analytics ジョブは、データ マートに発行されます。

![データ マートへの Stream Analytics ジョブの発行](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

データ マートの設定は、Stream Analytics ジョブ内にあります。

![Stream Analytics ジョブでのデータ マートの設定](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>使用
Power BI は、このソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。 

ダッシュボードは最終的に、次のようになります。

![Power BI ダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>まとめ
このドキュメントには、車両テレメトリ分析ソリューションの詳細な説明が含まれています。 ラムダ アーキテクチャ パターンは、予測と行動によるリアルタイム分析と一括分析のために使用されます。 このパターンは、ホット パス (リアルタイム) 分析およびコールド パス (一括) 分析を必要とする幅広いユース ケースに適用されます。 

### <a name="references"></a>参照

* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [ストリームの挿入に関する Azure Event Hubs SDK](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure Data Factory のデータ移動機能](../../data-factory/copy-activity-overview.md)
* [Azure Data Factory .NET アクティビティ](../../data-factory/transform-data-using-dotnet-custom-activity.md)
* [サンプル データを準備するための Azure Data Factory .NET アクティビティ Visual Studio ソリューション](https://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="next-steps"></a>次の手順

このソリューション用に Power BI レポートとダッシュボードを構成する方法については、「[車両テレメトリ分析ソリューション テンプレートの Power BI ダッシュボードのセットアップ手順](cortana-analytics-playbook-vehicle-telemetry-powerbi.md)」をご覧ください。
