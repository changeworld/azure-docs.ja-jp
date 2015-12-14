<properties 
	pageTitle="車両テレメトリ分析ソリューション テンプレートの PowerBI ダッシュボードのセットアップ手順 | Microsoft Azure" 
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


# 車両テレメトリ分析ソリューション テンプレートの PowerBI ダッシュボードのセットアップ手順

この**メニュー**は、このプレイブック内の各章にリンクされています。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


車両テレメトリ分析ソリューションでは、自動車販売代理店、自動車メーカー、および保険会社が Cortana Analytics の機能を使用して車両の状態と運転の習慣に関するリアルタイムの情報と予測情報を把握し、カスタマー エクスペリエンス、R&D、およびマーケティング キャンペーンの分野での向上を促進するための方法を紹介します。このドキュメントには、サブスクリプション内でソリューションがデプロイされた後で PowerBI のレポートとダッシュボードを構成する方法の詳細な手順が含まれています。


## 前提条件
1.	[https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3) に移動して、車両テレメトリ分析ソリューションをデプロイします。  
2.	[Microsoft Power BI Desktop をインストール](http://www.microsoft.com/download/details.aspx?id=45331)します。
3.	[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)。Azure サブスクリプションを持っていない場合は、Azure 無料サブスクリプションで開始します。
4.	Microsoft PowerBI アカウント
	

## Cortana Analytics Suite のコンポーネント
車両テレメトリ分析ソリューション テンプレートの一部として、サブスクリプション内に以下の Cortana Analytics サービスがデプロイされます。

- **Event Hubs** 数百万の車両テレメトリ イベントを Azure に取り込みます。
- **Stream Analytics** 車両の状態に関するリアルタイムの洞察を得て、より高度な一括分析のために長期的なストレージにそのデータを保持します。
- **Machine Learning** リアルタイムで異常検出を行い、バッチ処理で予測分析を行います。
- **HDInsight** 大量のデータを変換します。
- **Data Factory** オーケストレーション、スケジュール設定、リソース管理、およびバッチ処理パイプラインの監視を行います。

**Power BI** このソリューションに機能豊富なダッシュボードを提供し、リアルタイムのデータおよび予測分析を視覚化します。

ソリューションは、2 つの異なるデータ ソースを使用します。1 つは**シミュレートされた車両信号と診断のデータセット**で、もう 1 つは**車両カタログ**です。

車両テレマティックス シミュレーターは、このソリューションの一部として含まれています。診断情報と信号を出力し、信号は特定の時点での車両の状態と運転パターンに対応しています。

車両カタログは、モデルのマッピングに対する VIN を含む参照データセットです。


## PowerBI ダッシュボードの準備

### デプロイ

デプロイが完了すると、次の図のように、すべてのコンポーネントが緑色でマークされます。

- 緑色のノードの右上隅にある矢印をクリックして、対応するサービスに移動し、正常にデプロイされたかどうかを確認します。
- 車両テレマティックス シミュレーター ノードの右上隅にある矢印をクリックして、データ シミュレーター パッケージをダウンロードします。ローカルのコンピューターにファイルを保存して、展開します。 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

これで、PowerBI ダッシュボードを構成する準備ができました。ダッシュボードの豊富な視覚化機能で、車両の状態と運転の習慣に関するリアルタイム情報や予測情報を把握できます。すべてのレポートを作成し、ダッシュボードを構成するには、45 分から 1 時間ほどかかります。


### Power BI リアルタイム ダッシュボードのセットアップ

**シミュレートされたデータの生成**

1. ローカル コンピューター上で、車両テレマティックス シミュレーター パッケージを展開したフォルダーに移動します。 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)

2.	***CarEventGenerator.exe*** アプリケーションを実行します。
3.	診断情報と信号を出力し、信号は特定の時点での車両の状態と運転パターンに対応しています。これが、デプロイの一部として構成されている Azure Event Hub インスタンスに発行されます。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
リアルタイム ダッシュボード アプリケーションを開始します。アプリケーションは、PowerBI でリアルタイムのダッシュボードを生成するために、ソリューションの一部として含まれています。このアプリケーションは、Stream Analytics が連続的なパターンでイベントを発行している Event Hub インスタンスをリッスンしています。このアプリケーションは、受け取った各イベントで、Machine Learning Request-Response スコアリング エンドポイントを使用してデータを処理します。結果のデータセットは、視覚化のために、PowerBI プッシュ API に発行されます。アプリケーションをダウンロードするには:

1.	ダイアグラム ビューで PowerBI ノードをクリックし、プロパティ ウィンドウで **[Download Real-time Dashboard Application]** リンクをクリックします。

2.	ローカルに展開し、保存します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	RealtimeDashboardApp.exe アプリケーションを実行します。
4.	有効な Power BI 資格情報を指定し、サインインして、**[Accept]** をクリックします。
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## リアルタイム Power BI レポートの作成 

### PowerBI レポートの構成

Power BI では、新しいデータセットが生成されます。**ConnectedCarsRealtime** データセットをクリックします。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

**Ctrl + S** キーを使用して、空のレポートを保存します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

レポート名として、「*Vehicle Telemetry Analytics Real time - Reports*」を指定します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### リアルタイムのレポートの作成: *Vehicles in operation*
  
**[ページ 1]** をダブルクリックして、名前を「Vehicles in operation」に変更します。![Connected Cars - Vehicles in operation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

**[フィールド]** で **[vin]** フィールドを選択し、視覚化の種類として **[カード]** を選択します。

図に示されているような、カードの視覚化が作成されます。![Connected Cars - Select vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

新しい視覚化を追加するために、空の領域をクリックします。

フィールドから **[City]** と **[vin]** を選択します。視覚化を **[地図]** に変更します。**[vin]** を値領域にドラッグします。フィールドから **[city]** を **[凡例]** 領域にドラッグします。![Connected Cars - Card Visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
**[視覚化]** の **[書式]** を選択し、**[タイトル]** をクリックしてテキストを **"Vehicles in operation by city"** に変更します。![Connected Cars - Vehicles in operation by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

最終的に、視覚化は次の図のようになります。![Connected Cars - Final visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

新しい視覚化を追加するために、空の領域をクリックします。

**[City]** と **[vin]** を選択し、視覚化の種類を **[集合縦棒グラフ]** に変更します。**[軸]** 領域の **[City]** フィールドと、**[値]** 領域の **[vin]** を確認します。

**[Count of vin]** でグラフを並べ替えます。![Connected Cars - Count of vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

グラフの **[タイトル]** を **"Vehicles in operation by city"** に変更します。

**[書式]** セクションをクリックし、**[データの色]** を選択して、**[すべての表示]** の **[オン]** をクリックします。![Connected Cars - Show all Data Colors](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

色のアイコンをクリックして、各都市の色を変更します。![Connected Cars - Change Colors](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[集合縦棒グラフ]** を選択し、**[city]** フィールドを **[軸]** 領域に、**[Model]** を **[凡例]** 領域に、**[vin]** を **[値]** 領域にドラッグします。![Connected Cars - Clustered Column Chart](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Connected Cars - Rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
このページのすべての視覚化を、次の図のように再配置します。![Connected Cars - Visualizations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### 新しいレポートの追加: *Vehicles Requiring Maintenance*
  
![[追加] のいずれかを](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) をクリックして、新しいレポートを追加し、名前を **"Vehicles Requiring Maintenance"** に変更します。

![Connected Cars - Vehicles Requiring Maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

**[vin]** フィールドを選択し、視覚化の種類を **[カード]** に変更します。![Connected Cars - Vin Card Visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

データセットに、**"MaintenanceLabel"** という名前のフィールドがあります。このフィールドは、値として **"0"** と **"1"** を持ちます。値 "1" は、車両にメンテナンスが必要であることを示します。

メンテナンスを必要とする車両データを表示するための**ページ レベル** フィルターを追加します。

1. **"MaintenanceLabel"** フィールドを **[ページ レベル フィルター]** にドラッグします。![Connected Cars - Page Level Filters](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. MaintenanceLabel ページ レベル フィルターの下部にある **[基本フィルター]** メニューをクリックします。![Connected Cars - Basic Filtering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  そのフィルター値を **"1"** に設定します。![Connected Cars - Filter Value](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]グラフ レベルでフィルターを設定できます。このフィルターは、**ビジュアル レベル フィルター**と呼ばれます。特定の視覚化だけに適用できます。ページ レベル フィルターは、レポート上のすべての視覚化に適用できます。

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[集合縦棒グラフ]** を選択します。![Connected Cars - Vind Card Visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Connected Cars - Clustered Column Chart](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

フィールド **[Model]** を **[軸]** 領域に、**[Vin]** を **[値]** 領域にドラッグします。次に、**[Count of vin]** で視覚化を並べ替えます。グラフの **[タイトル]** を **"Vehicles requiring maintenance by model"** に変更します。

**[vin]** フィールドを、**[視覚化]** タブの **[フィールド]** ![フィールド](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) セクションにある **[色の彩度]** にドラッグします。![Connected Cars - Color Saturation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

**[書式]** セクションの視覚化の **[データの色]** を変更します。最小値の色を **F2C812** に変更します。最大値の色を **FF6300** に変更します。![Connected Cars - Color Changes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Connected Cars - New Visualization Colors](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[集合縦棒グラフ]** を選択し、**[vin]** フィールドを **[値]** 領域に、**[City]** フィールドを **[軸]** 領域にドラッグします。**[Count of vin]** でグラフを並べ替えます。グラフの **[タイトル]** を **"Vehicles requiring maintenance by city"** に変更します。![Connected Cars - Vehicles requiring maintenance by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[複数の行カード]** を選択し、**[Model]** と **[vin]** を **[フィールド]** 領域にドラッグします。![Connected Cars - Multi Row Card](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

すべての視覚化を再配置します。最終的に、レポートは次の図のようになります。![Connected Cars - Multi Row Card](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### 新しいレポートの追加: *Vehicles Health Statistics*
  
![[追加] のいずれかを](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) をクリックして、新しいレポートを追加し、名前を **"Vehicles Health Statistics"** に変更します。

視覚化から **[ゲージ]** を選択し、**[Speed]** フィールドを **[値]、[最小値]、[最大値]** 領域にドラッグします。![Connected Cars - Multi Row Card](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

**[値]** 領域の **[speed]** の既定の集計を **[平均]** に変更します。

**[最小]** 領域の **[speed]** の既定の集計を **[最小]** に変更します。

**[最大]** 領域の **[speed]** の既定の集計を **[最大]** に変更します。

![Connected Cars - Multi Row Card](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

**Gauge Title** という名前を **"Average speed"** に変更します。
 
![Connected Cars - Gauge](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

新しい視覚化を追加するために、空の領域をクリックします。

同様に、**[average engine oil]**、**[average fuel]**、および **[average engine temperate]** に **[ゲージ]** を追加します。

上の **"Average speed"** ゲージでの手順に従って、各ゲージのフィールドの既定の集計を変更します。

![Connected Cars - Gauges](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[折れ線グラフおよび集合縦棒グラフ]** を選択し、**[City]** フィールドを **[共有の軸]** に、**[speed]**、**[tirepressure]、および [engineoil]** フィールドを **[各棒の値]** 領域にドラッグして、集計の種類を **[平均]** に変更します。

**[engineTemperature]** フィールドを **[線の値]** 領域にドラッグし、集計の種類を **[平均]** に変更します。

![Connected Cars - Visualizations Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

グラフの **[タイトル]** を **"Average speed, tire pressure, engine oil and engine temperature"** に変更します。

![Connected Cars - Visualizations Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[ツリーマップ]** を選択し、**[Model]** フィールドを **[グループ]** 領域に、**[MaintenanceProbability]** フィールドを **[値]** 領域にドラッグします。

グラフの **[タイトル]** を **"Vehicle models requiring maintenance"** に変更します。

![Connected Cars - Change Chart Title](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[100% 積み上げ横棒グラフ]** を選択し、**[city]** フィールドを **[軸]** 領域にドラッグし、**[MaintenanceProbability]**、**[RecallProbability]** フィールドを **[値]** 領域にドラッグします。

![Connected Cars - Add New Visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

**[書式]** をクリックし、**[データの色]** を選択して、**[MaintenanceProbability]** の色の値を **"F2C80F"** に変更します。

グラフの **[タイトル]** を **"Probability of Vehicle Maintenance & Recall by City"** に変更します。

![Connected Cars - Add New Visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

新しい視覚化を追加するために、空の領域をクリックします。

視覚化から **[面グラフ]** を選択し、**[Model]** フィールドを **[軸]** 領域にドラッグし、**[engineOil]、[tirepressure]、[speed]、および [MaintenanceProbability]** フィールドを **[値]** 領域にドラッグします。それらの集計の種類を **[平均]** に変更します。

![Connected Cars - Change Aggregation Type](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

グラフのタイトルを **"Average engine oil, tire pressure, speed and maintenance probability by model"** に変更します。

![Connected Cars - Change Chart Title](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

新しい視覚化を追加するために、空の領域をクリックします。

1. 視覚化から **[散布図]** を選択します。
2. **[Model]** フィールドを **[詳細]** および **[凡例]** 領域にドラッグします。 
3. **[fuel]** フィールドを **[X 軸]** 領域にドラッグし、集計を [平均] に変更します。
4. **[engineTemparature]** を **[Y 軸]** にドラッグし、集計を **[平均]** に変更します。
5. **[vin]** フィールドを **[サイズ]** 領域にドラッグします。

![Connected Cars - Add new visualization](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

グラフの **[タイトル]** を **"Averages of Fuel, Engine Temperature by Model"** に変更します。

![Connected Cars - Change Chart Title](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最終的に、レポートは次の図のようになります。

![Connected Cars-Final Report](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### リアルタイム ダッシュボードへのレポートの視覚化のピン留め
  
上のレポートの視覚化を、セクション 2「**Power BI ダッシュボードのセットアップ**」で作成したリアルタイム ダッシュボードにピン留めします。

ダッシュボードは、次のようになります。

![Connected Cars-Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  Power BI バッチ処理ダッシュボードのセットアップ

>[AZURE.ALERT]エンド ツー エンドのバッチ処理パイプラインの実行が完了するまでに、約 2 時間かかります。次のステップに進む前に、しばらくお待ちください。

**PowerBI デザイナー ファイルのダウンロード** • PowerBI デザイナー ファイルは、デプロイの一部として含まれています。 • ダイアグラム ビューで PowerBI ノードをクリックし、プロパティ ウィンドウの "Download the PowerBI designer file" リンクをクリックします。 • ローカルに保存します。

**PowerBI レポートの構成** • PowerBI Desktop を使用して、デザイナー ファイル "VehicleTelemetryAnalytics - Desktop Report.pbix" を開きます。PowerBI Desktop をまだ持っていない場合は、http://www.microsoft.com/ja-JP/download/details.aspx?id=45331 でインストールします。 • **[クエリを編集]** をクリックします。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]Azure SQL Database の**名前/パスワード**は、デプロイのセットアップ中に指定したものです。

- **[Source]** をダブルクリックします。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- データ サーバー接続文字列
- **[データベース]** に *connectedcar* を設定します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- **[OK]** をクリックします。
- 既定で選択されている **[Windows 資格情報]** タブが表示されるので、右側の **[データベース]** タブをクリックして、**[データベース資格情報]** に変更します。
- **[ユーザー名]** および **[パスワード]** を指定します。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- **[接続]** をクリックします。
- 右側のウィンドウにある残りの 3 つのクエリに対して、上の手順を繰り返し、データ ソース接続の詳細を更新します。
- **[閉じて読み込む]** をクリックします。Power BI Desktop のファイル データセットは、SQL Azure Database テーブルに接続されます。
- Power BI Desktop ファイルを**閉じます**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- **[保存]** ボタンをクリックして、変更を保存します。 
 
***powerbi.com* にアップロードします。**powerbi.com に移動して、ログインします。![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	[データの取得] をクリックします。  
2.	Power BI Desktop ファイルをアップロードします。  
3.	アップロードするには、**[データの取得]、[ファイル] の [取得]、[ローカル ファイル]** の順にクリックします。  
4.	**"Connected Cars – Desktop Report.pbix"** の場所に移動します。  
5.	ファイルがアップロードされると、Power BI のワークスペースに戻ります。  

	データセット、レポート、および空のダッシュボードが作成されます。

	>**注:** ダッシュボードの横にあるプラス記号のアイコンをクリックして、空のダッシュボードを作成しなければならない場合もあります。

**ダッシュボードへのグラフのピン留め** Power BI で、上で作成した空のダッシュボードをクリックし、[レポート] セクションに移動して、新しくアップロードされたレポートをクリックします。

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**レポートには、次の 6 つのページがあります。** ページ 1: 車両の密度 ページ 2: リアルタイムの車両状態 ページ 3: アグレッシブに運転されている車両 ページ 4: リコールされた車両 ページ 5: 低燃費で運転されている車両 ページ 6: Contoso ロゴ

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**ページ 3 から**、以下のものをピン留めします。 1.Count of VIN ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2.Aggressively driven vehicles by model – ウォーター フォール図 ![Vehicle Telemetry - Pin Charts 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**ページ 5 から**、以下のものをピン留めします。 1.Count of vin ![Vehicle Telemetry - Pin Charts 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2.Fuel efficient vehicles by model : 集合縦棒グラフ ![Vehicle Telemetry - Pin Charts 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**ページ 4 から**、以下のものをピン留めします。

1.	Count of vin ![Vehicle Telemetry - Pin Charts 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Recalled vehicles by city, model : ツリーマップ ![Vehicle Telemetry - Pin Charts 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**ページ 6 から**、以下のものをピン留めします。

1.	Contoso Motors のロゴ ![Vehicle Telemetry - Pin Charts 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**ダッシュボードの整理**

1.	ダッシュボードに移動します。![Vehicle Telemetry - Organize Dashboard 1](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	各グラフにポインターを合わせ、以下の完成したダッシュボードの画像で指定されている名前に合わせて名前を変更します。また、以下のダッシュボードと同じになるように、グラフも移動します。 ![Vehicle Telemetry - Organize Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->