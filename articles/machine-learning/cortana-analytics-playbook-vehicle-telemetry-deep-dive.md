<properties 
	pageTitle="車両テレメトリ分析ソリューション プレイブック: ソリューションの詳細 | Microsoft Azure" 
	description="Cortana Analytics の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの情報と予測的な情報を得ます。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# 車両テレメトリ分析ソリューション プレイブック: ソリューションの詳細

この**メニュー**は、このプレイブック内の各セクションにリンクされています。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

このセクションでは、ソリューション アーキテクチャで示されている各ステージの詳細について、手順やカスタマイズのアドバイスと共に説明します。

## データ ソース

このソリューションでは、次の 2 つのデータ ソースを使用します。

- **シミュレートされた車両信号と診断データセット** 
- **車両カタログ**

車両テレマティックス シミュレーターは、このソリューションの一部として含まれています。診断情報と信号を出力し、信号は特定の時点での車両の状態と運転パターンに対応しています。[ここ](http://go.microsoft.com/fwlink/?LinkId=717075)をクリックして、**車両テレマティックス シミュレーター Visual Studio ソリューション**をダウンロードしてください。要件に基づいて、それをカスタマイズします。車両カタログには、参照データセットと、モデル マッピングに対する VIN が含まれています。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*図 2 - 車両テレマティックス シミュレーター*

これは JSON 形式のデータセットであり、以下のスキーマが含まれています。

分割 | 説明 | 値 |  
------- | ----------- | --------- | 
VIN | ランダムに生成された車両識別番号 | ランダムに生成された 10,000 個の車両識別番号を持つマスター リストから取得されます | 
Outside temperature | 車両運転時の外部温度 | ランダムに生成された 0 ～ 100 の数値 | 
Engine temperature | 車両のエンジン温度 | ランダムに生成された 0 ～ 500 の数値 | 
速度 | 車両を運転するエンジン回転数 | ランダムに生成された 0 ～ 100 の数値 | 
Fuel | 車両の燃料レベル | ランダムに生成された 0 ～ 100 の数値 (燃料レベルの割合を示す) | 
EngineOil | 車両のエンジン オイル レベル | ランダムに生成された 0 ～ 100 の数値 (エンジン オイル レベルの割合を示す) | 
Tirepressure | 車両のタイヤ空気圧 | ランダムに生成された 0 ～ 50 の数値 (タイヤ空気圧レベルの割合を示す) | 
Odometer | 車両の走行距離計の数値 | ランダムに生成された 0 ～ 200000 の数値 | 
Accelerator\_pedal\_position | 車両のアクセル ペダルの位置 | ランダムに生成された 0 ～ 100 の数値 (アクセル レベルの割合を示す) | 
Parking\_brake\_status | 車両が駐車されているかどうか | True または False | 
Headlamp\_status | ヘッドライトがついているかどうか | True または False | 
Brake\_pedal\_status | ブレーキ ペダルが踏まれているかどうか | True または False | 
Transmission\_gear\_position | 車両のギアの位置 | 状態: first、second、third、fourth、fifth、sixth、seventh、eighth | 
Ignition\_status | 車両が走行または停車しているかどうか | True または False | 
Windshield\_wiper\_status | フロント ガラス ワイパーが動作しているかどうか | True または False | 
ABS | ABS が動作中かどうか | True または False | 
タイムスタンプ | データ ポイントが作成されたときのタイムスタンプ | 日付 | 
City | 車両の場所 | このソリューションでは、次の 4 つの都市: ベルビュー、レドモンド、サマミッシュ、シアトル | 


車両モデル参照データセットには、VIN とモデルのマッピングが含まれています。

VIN | モデル |
--------------|------------------
FHL3O1SA4IEHB4WU1 | セダン |
8J0U8XCPRGW4Z3NQE | ハイブリッド |
WORG68Z2PLTNZDBI7 | ファミリー サルーン |
JTHMYHQTEPP4WBMRN | セダン |
W9FTHG27LZN1YWO0Y | ハイブリッド |
MHTP9N792PHK08WJM | ファミリー サルーン |
EI4QXI2AXVQQING4I | セダン |
5KKR2VB4WHQH97PF8 | ハイブリッド |
W9NSZ423XZHAONYXB | ファミリー サルーン |
26WJSGHX4MA5ROHNL | コンバーチブル |
GHLUB6ONKMOSI7E77 | ステーション ワゴン |
9C2RHVRVLMEJDBXLP | コンパクト カー |
BRNHVMZOUJ6EOCP32 | 小型 SUV |
VCYVW0WUZNBTM594J | スポーツ カー |
HNVCE6YFZSA5M82NY | 中型 SUV |
4R30FOR7NUOBL05GJ | ステーション ワゴン |
WYNIIY42VKV6OQS1J | 大型 SUV |
8Y5QKG27QET1RBK7I | 大型 SUV |
DF6OX2WSRA6511BVG | クーペ |
Z2EOZWZBXAEW3E60T | セダン |
M4TV6IEALD5QDS3IR | ハイブリッド |
VHRA1Y2TGTA84F00H | ファミリー サルーン |
R0JAUHT1L1R3BIKI0 | セダン |
9230C202Z60XX84AU | ハイブリッド |
T8DNDN5UDCWL7M72H | ファミリー サルーン |
4WPYRUZII5YV7YA42 | セダン |
D1ZVY26UV2BFGHZNO | ハイブリッド |
XUF99EW9OIQOMV7Q7 | ファミリー サルーン
8OMCL3LGI7XNCC21U | コンバーチブル |
……. | |


### シミュレートされたデータを生成するには
1.	車両テレマティックス シミュレーター ノードの右上隅にある矢印をクリックして、データ シミュレーター パッケージをダウンロードします。ローカルのコンピューターにファイルを保存して、展開します。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *図 3 – 車両テレメトリ分析ソリューションのブループリント*

2.	ローカル コンピューター上で、車両テレマティックス シミュレーター パッケージを展開したフォルダーに移動します。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*図 4 - 車両テレマティックス シミュレーター フォルダー*

3.	アプリケーション **CarEventGenerator.exe** を実行します。

### 参照
[車両テレマティックス シミュレーター Visual Studio ソリューション](http://go.microsoft.com/fwlink/?LinkId=717075) [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/) [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## データの取り込み
車両信号および診断イベントと、リアルタイム分析および一括分析を取り込むために、Azure Event Hubs、Stream Analytics、および Data Factory の組み合わせが利用されます。これらのすべてのコンポーネントは、ソリューション デプロイの一部として作成および構成されます。

### リアルタイム分析
車両テレマティックス シミュレーターによって生成されたイベントは、Event Hub SDK を使用してイベント ハブに公開されます。Stream Analytics ジョブは、イベント ハブからこれらのイベントを取り込み、リアルタイムでデータを処理して、車両の状態を分析します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*図 5 - Event Hub ダッシュボード*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*図 6 - Stream Analytics ジョブによるデータの処理*

Stream Analytics ジョブは、イベント ハブからデータを取り込み、参照データと結合して、車両 VIN を対応するモデルにマップします。また、それらを Azure Blob Storage に保持して、さまざまな一括分析で利用できるようにします。以下の Stream Analytics クエリを使用すると、データを Azure Blob Storage に保持できます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*図 7 - データを取り込むための Stream Analytics ジョブ クエリ*

### 一括分析
さまざまな一括分析のために、シミュレートされた車両信号と診断データセットの追加のボリュームも生成されます。これは、一括処理で適切な代表的データ ボリュームを使用するために必要です。そのために、Azure Data Factory ワークフローで "PrepareSampleDataPipeline" という名前のパイプラインを使用して、1 年分のシミュレートされた車両信号および診断データセットを生成しています。[ここ](http://go.microsoft.com/fwlink/?LinkId=717077)をクリックして、Data Factory カスタム DotNet アクティビティ Visual Studio ソリューションをダウンロードしてください。要件に基づいて、それをカスタマイズします。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*図 8 - 一括処理ワークフローのサンプル データの準備*

パイプラインは、以下に示すカスタム ADF .NET アクティビティで構成されています。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*図 9 - PrepareSampleDataPipeline*

パイプラインが正常に実行され、"RawCarEventsTable" データセットが "Ready" としてマークされると、1 年分のシミュレートされた車両信号と診断データが生成されます。ストレージ アカウントの "connectedcar" コンテナーの下に、作成された以下のフォルダーとファイルが表示されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*図 10 - PrepareSampleDataPipeline Output*

### 参照
[ストリーム取り込み用の Azure Event Hub SDK](event-hubs-csharp-ephcs-getstarted.md) [Azure Data Factory のデータ移動機能](data-factory-data-movement-activities.md) [Azure Data Factory の DotNet アクティビティ](data-factory-use-custom-activities.md) [サンプル データを準備するための Azure Data Factory DotNet アクティビティ Visual Studio ソリューション](http://go.microsoft.com/fwlink/?LinkId=717077)


## 準備
>[AZURE.ALERT]ソリューションのこの手順は、一括処理だけで行います。

生の半構造化された車両信号と診断データセットは、データの準備の手順で、YEAR/MONTH 形式に分割されます。これは、クエリを効率化し、拡張性の高い長期保持を可能にするためです (つまり、1 つの BLOB アカウントがいっぱいになったら、次のアカウントにフェールオーバーできます)。*PartitionedCarEventsTable* というラベルの出力データは、お客様の "Data Lake" 内に、基本的な "最も加工されていない" 形式のデータとして、長期間保持する必要があります。このパイプラインへの入力データは、出力データが入力に対して完全に忠実であれば、通常は破棄されます。後で使用する場合にだけ、格納 (および分割) されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*図 11 - 車両イベントの分割のワークフロー*

生データは、"PartitionCarEventsPipeline" の Hive HDInsight アクティビティを使用して分割されます。手順 1. で生成された 1 年分のサンプル データは、YEAR/MONTH で分割され、1 年の各月に対応する車両信号と診断データのパーティション (合計 12 パーティション) が生成されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*図 12 - PartitionCarEventsPipeline*

分割には、以下に示す "partitioncarevents.hql" という名前の Hive スクリプトが使用されます。このスクリプトは、ダウンロードした zip の \\demo\\src\\connectedcar\\scripts フォルダーにあります。

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
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

*図 13 - PartitionConnectedCarEvents Hive スクリプト*

パイプラインが正常に実行されると、ストレージ アカウントの "connectedcar" コンテナーの下に、生成された以下のパーティションが表示されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*図 14 - 分割された出力*

データは最適化され、管理しやすくなって、さまざまな一括調査を処理する準備ができました。

## データ分析

このセクションでは、車両の状態と運転の習慣に対するさまざまな高度な分析を行うために、Azure Stream Analytics、Azure Machine Learning、Azure Data Factory、および Azure HDInsight の組み合わせをどのように使用したかがわかります。このセクションには、以下の 3 つのサブセクションがあります。

1.	**機械学習**: このサブセクションには、サービス メンテナンスを必要とする車両と、安全性の問題のためにリコールを必要とする車両を予測するためにこのソリューションで使用した、異常検出実験に関する情報が含まれています。
2.	**リアルタイム分析**: このサブセクションには、Stream Analytics クエリ言語を使用し、カスタム アプリケーションによるリアルタイムでの機械学習実験を運用可能にしたリアルタイム分析に関する情報が含まれています。
3.	**一括分析**: このサブセクションには、Azure Data Factory によって運用可能になった Azure HDInsight と Azure Machine Learning を使用した一括データの変換と処理に関する情報が含まれています。

### Machine Learning

ここでの目標は、特定の状態の統計データに基づいて、メンテナンスやリコールを必要とする車両を予測することです。想定した条件は、以下のとおりです。

- 次の 3 つの条件のいずれかに該当する場合、車両は**サービス メンテナンス**を必要とする。
	- タイヤ空気圧が低い
	- エンジン オイルのレベルが低い
	- エンジン温度が高い

- 次の条件のいずれかに該当する場合、車両には**安全性の問題**があり、**リコール**を必要とする。
	- エンジン温度が高いが、外部温度が低い
	- エンジン温度が低いが、外部温度が高い

上の要件に基づき、異常を検出するための 2 つの異なるモデルを作成しました。1 つは車両メンテナンスの検出用で、もう 1 つは車両リコールの検出用です。どちらのモデルでも、異常検出のために、組み込みの主成分分析 (PCA) アルゴリズムが使用されています。

**メンテナンス検出モデル** メンテナンス検出モデルでは、3 つのインジケーター (タイヤ空気圧、エンジン オイル、およびエンジン温度) のいずれかがそれぞれの条件を満たす場合に、異常が報告されます。そのため、モデルの構築では、これらの 3 つの変数だけを考慮すれば済みます。Azure Machine Learning による実験では、まず、**プロジェクト列**モジュールを使用して、これらの 3 つの変数を抽出します。次に、PCA ベースの異常検出モジュールを使用して、異常検出モデルを構築します。

主成分分析 (PCA) は、機能の選択、分類、および異常検出に使用できる、機械学習で確立された手法です。PCA では、関連を持つ可能性がある変数を含むケースのセットを、主要成分と呼ばれる値のセットに変換します。PCA ベースのモデルの基本的な考え方は、機能や異常をより簡単に識別できるように、データをより低い次元の空間に投影することです。
 
異常検出の場合、異常検出機能は新しい入力のそれぞれに対して、まず、固有ベクトルへの投影を計算し、次に正規化された再構築エラーを計算します。この正規化されたエラーが、異常スコアです。エラーのスコアが高いほど、より異常なインスタンスになります。

メンテナンス検出の問題では、各レコードを 3 次元空間内の点として考えることができます。この点は、タイヤ空気圧、エンジン オイル、およびエンジン温度の座標によって定義されます。これらの異常をキャプチャするには、PCA を使用して、3 次元空間内のデータを 2 次元空間に投影します。こうすることで、PCA で使用する Number of components パラメーターを 2 に設定できます。このパラメーターは、PCA ベースの異常検出を適用するときに、重要な役割を果たします。PCA を使用してデータを投影すると、これらの異常をより簡単に特定できます。

**リコール異常検出モデル** リコール異常検出モデルでは、プロジェクト列と PCA ベースの異常検出モジュールを同じような方法で使用します。具体的には、まず、**プロジェクト列**モジュールを使用して、3 つの変数 (エンジン温度、外部温度、および速度) を抽出します。エンジン温度は、通常、速度と関連しているため、速度の変数も含めます。次に、PCA ベースの異常検出モジュールを使用して、3 次元空間から 2 次元空間にデータを投影します。エンジン温度と外部温度が大きく負相関している場合、リコール条件が満たされ、車両のリコールが必要になります。PCA ベースの異常検出アルゴリズムを使用すると、PCA の実行後、異常をキャプチャできます。

どちらのモデルをトレーニングするときも、PCA ベースの異常検出モデルをトレーニングするための入力データとして、メンテナンスやリコールを必要としない通常のデータを使用する必要があることに注意してください。スコア付け実験では、トレーニング済みの異常検出モデルを使用して、車両がメンテナンスやリコールを必要とするかどうかを調べます。


### リアルタイム分析

次の Stream Analytics SQL クエリは、異常を検出し、アラートを発し、特定の地域で運転されている車両の全体的な状態を判断して、人口統計に関連付けるために、重要なすべての車両パラメーター (車両速度、燃料レベル、エンジン温度、走行距離計の数値、タイヤ空気圧、エンジン オイル レベルなど) の平均を取得する目的で使用されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

図 15 - リアルタイム処理のための Stream Analytics クエリ

すべての平均は、3 秒間のタンブリング ウィンドウで計算します。ここでタンブリング ウィンドウを使用しているのは、重複せずに連続する時間間隔が必要であるためです。

Azure Stream Analytics のすべての "ウィンドウ化" 機能の詳細については、[ここ](https://msdn.microsoft.com/library/azure/dn835019.aspx)をクリックしてください。

**リアルタイムの予測**

機械学習モデルをリアルタイムで運用できるようにするアプリケーションが、ソリューションの一部として含まれています。"RealTimeDashboardApp" と呼ばれるこのアプリケーションは、ソリューションのデプロイの一環として作成および構成されます。このアプリケーションは、以下の処理を行います。

1.	Stream Analytics がイベントを連続するパターンで発行しているイベント ハブ インスタンスをリッスンします。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*図 16 – 出力のイベント ハブ インスタンスにデータを発行するための Stream Analytics クエリ* 

2.	このアプリケーションが受け取るすべてのイベントに対して、次の処理を行います。

	- Machine Learning Request-Response Scoring (RRS) エンドポイントを使用して、データを処理します。RRS エンドポイントは、デプロイの一環として自動的に発行されます。
	- RRS 出力は、プッシュ API を使用して PowerBI データセットに発行されます。

このパターンは、基幹業務アプリケーションをアラート、通知、メッセージングなどのシナリオ用のリアルタイム分析フローと統合するシナリオにも適用できます。

[ここ](http://go.microsoft.com/fwlink/?LinkId=717078)をクリックして、RealtimeDashboardApp Visual Studio ソリューションをダウンロードし、カスタマイズします。

****リアルタイム ダッシュボード アプリケーションを実行するには **

1.	ダイアグラム ビューで PowerBI ノードをクリックし、プロパティ ウィンドウで [Download Real-time Dashboard Application] リンクをクリックします。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *図 17 – PowerBI ダッシュボードのセットアップ手順*
2.	ローカルに展開し、保存します。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *図 18 – RealtimeDashboardApp フォルダー*
3.	RealtimeDashboardApp.exe アプリケーションを実行します。
4.	有効な Power BI 資格情報を指定し、サインインして、[Accept] をクリックします。![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*図 19 – RealtimeDashboardApp: PowerBI へのサインイン*

>[AZURE.NOTE]注: PowerBI データセットをフラッシュする場合は、次のように、flushdata パラメーターを指定して RealtimeDashboardApp を実行します。

	RealtimeDashboardApp.exe -flushdata

### 一括分析

ここでの目標は、Contoso Motors がビッグ データを活用するために Azure コンピューティング機能をどのように使用しているかを示すことです。運転パターン、使用行動、および車両の状態についてさまざまな情報を得て、次のようなことに役立てています。

- カスタマー エクスペリエンスを向上させ、運転の習慣や燃費のよい運転行動についての知見を提供して、コストを下げます。
- 顧客とその運転パターンについて事前に学習して、ビジネスの意思決定を制御したり、最高クラスの製品やサービスを提供したりします。

このソリューションでは、以下のメトリックを対象にします。

1.	**アグレッシブな運転行動** モデル、場所、運転条件、および時期の傾向を識別して、アグレッシブな運転パターンを把握し、それを Contoso Motors がマーケティング キャンペーン、新しい個人用の機能の促進、走行距離ベースの保険料などに使用できるようにします。
2.	**低燃費な運転行動** モデル、場所、運転条件、および時期の傾向を識別して、低燃費な運転パターンを把握し、それを Contoso Motors がマーケティング キャンペーン、新機能の促進、低コストで環境にやさしい運転習慣についての運転者への積極的な広報などに使用できるようにします。 
3.	**リコール モデル** 異常検出の機械学習実験を実施して、リコールを必要とするモデルを識別します。

以下で、各メトリックの詳細を説明します。


**アグレッシブな運転パターン**

分割された車両信号と診断データが、"AggresiveDrivingPatternPipeline" という名前のパイプラインで Hive を使用して処理され、アグレッシブな運転パターンを示すモデル、場所、車両、および運転条件などが特定されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *図 20 – アグレッシブな運転パターンのワークフロー*

アグレッシブな運転条件パターンを分析するために使用される "aggresivedriving.hql" という名前の Hive スクリプトは、ダウンロードした zip ファイルの \\demo\\src\\connectedcar\\scripts フォルダーにあります。

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
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

*図 21 – アグレッシブな運転パターンの Hive クエリ*

このスクリプトは、車両のギアの位置、ブレーキ ペダルの状態、および速度の組み合わせを使用して、高速度でのブレーキ使用パターンに基づいて無謀/アグレッシブな運転行動を検出します。

パイプラインが正常に実行されると、ストレージ アカウントの "connectedcar" コンテナーの下に、生成された以下のパーティションが表示されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*図 22 – AggressiveDrivingPatternPipeline の出力*


**低燃費な運転パターン**

分割された車両信号と診断データが、"FuelEfficientDrivingPatternPipeline" という名前のパイプラインで Hive を使用して処理され、低燃費な運転パターンを示すモデル、場所、車両、および運転条件などが特定されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*図 23 – 低燃費な運転パターンのワークフロー*

アグレッシブな運転条件パターンを分析するために使用される "fuelefficientdriving.hql" という名前の Hive スクリプトは、ダウンロードした zip ファイルの \\demo\\src\\connectedcar\\scripts フォルダーにあります。

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
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


*図 24 – 低燃費な運転パターンの Hive クエリ*

このスクリプトは、車両のギアの位置、ブレーキ ペダルの状態、速度、およびアクセス ペダルの位置の組み合わせを使用して、加速度、ブレーキ使用、および速度のパターンに基づいて低燃費な運転行動を検出します。

パイプラインが正常に実行されると、ストレージ アカウントの "connectedcar" コンテナーの下に、生成された以下のパーティションが表示されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*図 25 - FuelEfficientDrivingPatternPipeline の出力*


**リコール予測**

機械学習実験はプロビジョニングされ、ソリューション デプロイの一環として、Web サービスとして発行されます。このワークフローでは、バッチ スコアリング エンドポイントが使用されます。データ ファクトリのリンクされたサービスとして登録され、データ ファクトリのバッチ スコアリング アクティビティを使用して運用可能にされます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*図 26 – データ ファクトリのリンクされたサービスとして登録された機械学習エンドポイント*

登録された、リンクされたサービスは、DetectAnomalyPipeline で、異常検出モデルを使用してデータにスコアを付けるために使用されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*図 27 – データ ファクトリでの Azure Machine Learning バッチ スコアリング アクティビティ*

このパイプラインで、バッチ スコアリング Web サービスとの運用が可能になるようにデータを準備する手順は、ごくわずかです。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*図 28 – リコールが必要な車両を予測するための DetectAnomalyPipeline*

スコア付けが完了すると、HDInsight アクティビティを使用してデータが処理および集計され、確率スコアが 0.60 以上のデータはモデルによって異常として分類されます。

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
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

*図 29 – リコール集計の Hive クエリ*

パイプラインが正常に実行されると、ストレージ アカウントの "connectedcar" コンテナーの下に、生成された以下のパーティションが表示されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*図 30 – DetectAnomalyPipeline の出力*


## [発行]

### リアルタイム分析

Stream Analytics ジョブのクエリの 1 つでは、出力のイベント ハブ インスタンスにイベントが発行されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*図 31 - Stream Analytics ジョブから出力のイベント ハブ インスタンスへの発行*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*図 32 - 出力のイベント ハブ インスタンスに発行するための Stream Analytics クエリ*

このイベントのストリームは、ソリューションに含まれている RealTimeDashboardApp で使用されます。このアプリケーションは、リアルタイム スコアリングのために Machine Learning Request-Response Web サービスを利用し、結果のデータを PowerBI データセットで使用するために発行します。

### 一括分析

バッチとリアルタイム処理の結果は、Azure SQL Database テーブルで使用するために発行されます。Azure SQL Server、データベース、およびテーブルは、セットアップ スクリプトの一部として自動的に作成されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*図 33 - バッチ処理の結果をデータ マートにコピーするワークフロー*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*図 34 - Stream Analytics ジョブからデータ マートへの発行*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*図 35 – Stream Analytics ジョブでのデータ マートの設定*


## 使用

Power BI は、このソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。

PowerBI レポートとダッシュボードを設定するための詳細な手順については、ここをクリックしてください。最終的に、ダッシュボードは、次のようになります。

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*図 36 - PowerBI ダッシュボード*

## まとめ

このドキュメントには、車両テレメトリ分析ソリューションの詳細な説明が含まれています。これは、予測と行動によるリアルタイム分析と一括分析用のラムダ アーキテクチャ パターンを示しています。このパターンは、ホット パス (リアルタイム) 分析およびコールド パス (一括) 分析を必要とする幅広いユース ケースに適用されます。

<!---HONumber=AcomDC_1203_2015-->