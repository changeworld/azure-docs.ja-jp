---
title: 車両の状態および運転習慣の Power BI ダッシュボード - Azure | Microsoft Docs
description: Cortana Intelligence の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの予測的な洞察を得ます。
services: machine-learning
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: e6601093577eb9e3dfba4ed27e1e0510cad17de7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421051"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>車両テレメトリ分析ソリューション テンプレートの Power BI ダッシュボードのセットアップ手順
このメニューは、このプレイブック内の各章にリンクされています。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

車両テレメトリ分析ソリューションでは、自動車販売店、自動車製造業者、保険会社が Cortana Intelligence の機能をどのように使用できるかを紹介します。 車両の状態と運転習慣についてリアルタイムの分析情報と予測的な分析情報を得て、カスタマー エクスペリエンス、研究と開発、マーケティング キャンペーンを改善できます。 このステップバイステップの手順では、ご自分のサブスクリプションでソリューションをデプロイした後に Power BI のレポートとダッシュボードを構成する方法を示します。 

## <a name="prerequisites"></a>前提条件
* [車両テレメトリ分析](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90)ソリューションをデプロイします。 
* [Power BI Desktop をインストール](http://www.microsoft.com/download/details.aspx?id=45331)します。
* [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)を取得します。 Azure サブスクリプションがない場合は、Azure 無料サブスクリプションで開始します。
* Power BI アカウントを開きます。

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence Suite のコンポーネント
車両テレメトリ分析ソリューション テンプレートの一部として、ご自分のサブスクリプション内に以下の Cortana Intelligence サービスがデプロイされます。

* **Azure Event Hubs** では、数百万の車両テレメトリ イベントが Azure に取り込まれます。
* **Azure Stream Analytics** では、車両の状態に関するリアルタイムの分析情報を得て、より高度な一括分析のために、そのデータを長期保管します。
* **Azure Machine Learning** では、リアルタイムで異常を検出し、バッチ処理を使用して予測的な情報を提供します。
* **Azure HDInsight** は、大規模なデータ変換を行います。
* **Azure Data Factory** では、オーケストレーション、スケジュール設定、リソース管理、バッチ処理のパイプラインの監視が処理されます。

**Power BI** は、このソリューションに機能豊富なダッシュボードを提供し、データおよび予測分析を視覚化します。 

このソリューションでは、次の 2 つのデータ ソースを使用します。

* シミュレートされた車両信号と診断データセット
* 車両カタログ

車両テレマティックス シミュレーターは、このソリューションの一部として含まれています。 診断情報と、特定の時点での車両の状態と運転パターンに対応する信号を出力します。 

車両カタログは、VIN をモデルにマップする参照データ セットです。

## <a name="power-bi-dashboard-preparation"></a>Power BI ダッシュボードの準備
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Power BI リアルタイム ダッシュボードのセットアップ

#### <a name="start-the-real-time-dashboard-application"></a>リアルタイム ダッシュボード アプリケーションの開始
デプロイが完了した後は、手動操作の手順に従います。

1. リアルタイム ダッシュボード アプリケーションの RealtimeDashboardApp.zip をダウンロードして解凍します。

1.  解凍したフォルダーで、アプリケーション構成ファイル RealtimeDashboardApp.exe.config を開きます。Event Hubs、Azure Blob Storage、Azure Machine Learning サービス接続の appSettings を手動操作手順の値に置き換えます。 変更を保存します。

1. RealtimeDashboardApp.exe アプリケーションを実行します。 サインイン ウィンドウで、ご自分の有効な Power BI 資格情報を入力します。 

   ![Power BI のサインイン ウィンドウ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
1. **[Accept]\(承認\)** を選択します。 アプリが動作し始めます。

   ![Power BI ダッシュボードのアクセス許可](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

1. Power BI Web サイトにサインインし、リアルタイム ダッシュボードを作成します。

これで、Power BI ダッシュボードを構成する準備ができました。  

### <a name="configure-power-bi-reports"></a>Power BI レポートを構成
リアルタイムのレポートとダッシュボードが完成するまでには、30 - 45 分ほどかかります。 

1. [Power BI](http://powerbi.com) Web ページを閲覧し、サインインします。

    ![Power BI のサインイン ページ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

1. Power BI で、新しいデータセットが生成されます。 **ConnectedCarsRealtime** データセットを選択します。

    ![ConnectedCarsRealtime データセット](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

1. 空のレポートを保存するには、Ctrl + S キーを押します。

    ![空のレポート](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

1. レポート名として「**Vehicle Telemetry Analytics Real time - Reports**」を入力します。

    ![レポート名](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>リアルタイム レポート
このソリューションには、次の 3 つのリアルタイム レポートがあります:

* Vehicles in Operation
* Vehicles Requiring Maintenance
* Vehicle Health Statistics

3 つのすべてのレポートを構成することも、いずれかの段階の後で止めることもできます。 その後、バッチ レポートの構成方法に関する次のセクションに進むことができます。 3 つのレポートをすべて作成して、ソリューションのリアルタイム パスに対する詳細な分析情報を視覚化することをお勧めします。  

### <a name="vehicles-in-operation-report"></a>Vehicles in Operation レポート
1. **[ページ 1]** をダブルクリックして、名前を **Vehicles in Operation** に変更します。

    ![Vehicles in Operation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

1. **[フィールド]** タブで **[vin]** を選択します。 **[視覚化]** タブで、**[カード]** 視覚エフェクトを選択します。  

    次の図に示されているような、**カード**の視覚エフェクトが作成されます。

    ![[vin] の選択](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[フィールド]** タブで、**[city]** と **[vin]** を選択します。 **[視覚化]** タブで、**[マップ]** 視覚エフェクトを選択します。 **[vin]** を **[値]** 領域にドラッグします。 **[city]** を **[凡例]** 領域にドラッグします。 

    ![カードの視覚エフェクト](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

1. **[視覚化]** タブで、**[書式]** セクションを選択します。 **[タイトル]** を選択し、**[テキスト]** を **Vehicles in operation by city** に変更します。

    ![Vehicles in operation by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    最終的な視覚エフェクトは、次の例のようになります。

    ![最終的な視覚エフェクト](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[フィールド]** タブで、**[city]** と **[vin]** を選択します。 **[視覚化]** タブで、**[集合縦棒グラフ]** 視覚エフェクトを選択します。 **[city]** を **[軸]** 領域にドラッグします。 **[vin]** を **[値]** 領域にドラッグします。

1. **[Count of vin]** でグラフを並べ替えます。

    ![[Count of vin]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

1. グラフの **[タイトル]** を **Vehicles in operation by city** に変更します。 

1. **[書式]** セクション、**[データの色]** の順に選択します。 **[すべて表示]** を **[オン]** に変更します。

    ![[データの色]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

1. 個々の都市の色を変更するには、カラー アイコンを選択します。

    ![色の変更](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[視覚化]** タブで、**[集合縦棒グラフ]** 視覚エフェクトを選択します。 **[フィールド]** タブで、**[city]** を **[軸]** 領域にドラッグします。 **[Model]** を **[凡例]** 領域にドラッグします。 **[vin]** を **[値]** 領域にドラッグします。

    ![[集合縦棒グラフ]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    このグラフは、次の画像のように表示されます。

    ![表示](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

1. ページが次の例のように表示されるように、すべての視覚エフェクトを再配置します。

    ![視覚化機能を備えたダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

これで、"Vehicles in Operation" レポートが正しく構成されました。 次のリアルタイム レポートを作成することも、ここで終了して、ダッシュボードを構成することもできます。 

### <a name="vehicles-requiring-maintenance-report"></a>Vehicles Requiring Maintenance レポート

1. ![[追加]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) を選択し、新しいレポートを追加します。 その名前を **Vehicles Requiring Maintenance** に変更します。

    ![Vehicles Requiring Maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

1. **[フィールド]** タブで **[vin]** を選択します。 **[視覚化]** タブで、**[カード]** 視覚エフェクトを選択します。

    ![VIN のカードの視覚エフェクト](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    データ セットには、**MaintenanceLabel** という名前のフィールドがあります。 このフィールドには "0" または "1" の値を指定できます。 この値は、ソリューションの一部としてプロビジョニングされた、機械学習モデルによって設定されます。 これはリアルタイム パスと統合されます。 値 "1" は、車両にメンテナンスが必要であることを示します。 

1. **ページ レベル フィルター** を追加して、メンテナンスが必要な車両についてデータを表示するには、次の手順に従います。 

   a. **[MaintenanceLabel]** フィールドを **[ページ レベル フィルター]** にドラッグします。
  
      ![[ページ レベル フィルター]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. **[ページ レベル フィルター] の [MaintenanceLabel]** の下部で、**[基本フィルター]** 　を選択します。

      ![[基本フィルター]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. フィルター値を **1** に設定します。

      ![フィルター値](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[視覚化]** タブで、**[集合縦棒グラフ]** 視覚エフェクトを選択します。 

    ![VIN のカード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![[集合縦棒グラフ]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

1. **[フィールド]** タブで、**[Model]** を **[軸]** 領域にドラッグします。 **[vin]** を **[値]** 領域にドラッグします。 次に、**[Count of vin]** で視覚エフェクトを並べ替えます。 グラフの **[タイトル]** を **Vehicles requiring maintenance by model** に変更します。 

1. **[視覚化]** タブの **[フィールド]** ![[フィールド] の画像](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png)セクションで、**[vin]** を **[色の彩度]** にドラッグします。

    ![[色の彩度]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

1. **[書式]** セクションで、視覚エフェクトの **[データの色]** を変更します。 

    a. **[最小値]** の色を **F2C812** に変更します。

    b. **[最大値]** の色を **FF6300** に変更します。

    ![新しいデータ色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    視覚エフェクトの新しい色は、次の例のようになります。

    ![視覚エフェクトの新しい色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[視覚化]** タブで、**[集合縦棒グラフ]** を選択します。 **[vin]** を **[値]** 領域にドラッグします。 **[city]** を **[軸]** 領域にドラッグします。 **[Count of vin]** でグラフを並べ替えます。 グラフの **[タイトル]** を **Vehicles requiring maintenance by city** に変更します。

    ![Vehicles requiring maintenance by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

1. **[視覚化]** タブで、**[複数の行カード]** 視覚エフェクトを選択します。 **[Model]** と **[vin]** を **[フィールド]** 領域にドラッグします。

    ![[複数の行カード]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

1. 最終的なレポートが次の例のように表示されるように、すべての視覚エフェクトを再配置します。 

    ![再配置された最終的なレポート](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

これで、"Vehicles Requiring Maintenance" リアルタイム レポートが正しく構成されました。 次のリアルタイム レポートを作成することも、ここで終了して、ダッシュボードを構成することもできます。 

### <a name="vehicle-health-statistics-report"></a>Vehicle Health Statistics レポート

1. ![[追加]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) を選択し、新しいレポートを追加します。 その名前を **Vehicles Health Statistics** に変更します。 

1. **[視覚化]** タブで、**[ゲージ]** 視覚エフェクトを選択します。 **[speed]** を **[値]**、**[最小値]**、**[最大値]** 領域にドラッグします。

   ![Vehicles Health Statistics](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

1. **[値]** 領域で、**[speed]** の既定の集計を **[平均]** に変更します。

1. **[最小値]** 領域で、**[speed]** の既定の集計を **[最小値]** に変更します。

1. **[最大値]** 領域で、**[speed]** の既定の集計を **[最大値]** に変更します。

   ![速度の値](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

1. **[Gauge Title]\(ゲージ タイトル\)** の名前を **Average speed** に変更します。

   ![ゲージ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。  

    同様に、**Average engine oil**、**Average fuel**、**Average engine temperature** の**ゲージ**を追加します。  

1. **Average speed** ゲージで前に行った手順に従って、各ゲージのフィールドの既定の集計を変更します。

    ![その他のゲージ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。

1. **[視覚化]** タブで、**[折れ線グラフおよび集合縦棒グラフ]** 視覚エフェクトを選択します。 **[city]** を **[共有の軸]** にドラッグします。 **[tirepressure]**、**[engineoil]**、**[speed]** を **[各棒の値]** 領域にドラッグします。 それらの集計の種類を **[平均]** に変更します。 

1. **[engineTemperature]** を **[線の値]** 領域にドラッグします。 その集計の種類を **[平均]** に変更します。 

    ![[各棒の値] と [線の値]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

1. グラフの **[タイトル]** を **Average speed, tire pressure, engine oil and engine temperature** に変更します。  

    ![折れ線グラフおよび集合縦棒グラフのタイトル](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。

1. **[視覚化]** タブで、**[ツリーマップ]** 視覚エフェクトを選択します。 **[Model]** を **[グループ]** 領域にドラッグします。 **[MaintenanceProbability]** を **[値]** 領域にドラッグします。

1. グラフの **[タイトル]** を **Vehicle models requiring maintenance** に変更します。

    ![ツリーマップのタイトル](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。

1. **[視覚化]** タブで、**[100% 積み上げ横棒グラフ]** 視覚エフェクトを選択します。 **[city]** を **[軸]** 領域にドラッグします。 **[MaintenanceProbability]** と **[RecallProbability]** を **[値]** 領域にドラッグします。

    ![[軸] 領域と [値] 領域](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

1. **[書式]** セクションで、**[データの色]** を選択します。 **[MaintenanceProbability]** の色を **F2C80F** という値に設定します。

1. グラフの **[タイトル]** を **Probability of Vehicle Maintenance & Recall by City** に変更します。

    ![100% 積み上げ横棒グラフのタイトル](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。

1. **[視覚化]** タブで、**[面グラフ]** 視覚エフェクトを選択します。 **[Model]** を **[軸]** 領域にドラッグします。 **[engineOil]**、**[tirepressure]**、**[speed]**、**[MaintenanceProbability]** を **[値]** 領域にドラッグします。 それらの集計の種類を **[平均]** に変更します。 

    ![集計の種類](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

1. グラフの **[タイトル]** を **Average engine oil, tire pressure, speed, and maintenance probability by model** に変更します。

    ![面グラフのタイトル](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

1. 新しい視覚エフェクトを追加するために、空の領域を選択します。

1. **[視覚化]** タブで、**[散布図]** 視覚エフェクトを選択します。 **[Model]** を **[詳細]** 領域と **[凡例]** 領域にドラッグします。 **[fuel]** を **[X 軸]** 領域にドラッグします。 その集計を **[平均]** に変更します。 **[engineTemperature]** を **[Y 軸]** 領域にドラッグします。 その集計を **[平均]** に変更します。 **[vin]** を **[サイズ]** 領域にドラッグします。

    ![[詳細]、[凡例]、[軸]、[サイズ] 領域](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

1. グラフの **[タイトル]** を **Average of fuel, Average of engineTemperature, and Count of vin by Model** に変更します。

    ![散布図のタイトル](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    最終的なレポートは、次の例のようになります。

    ![最終的なレポート](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>リアルタイム ダッシュボードへのレポートの視覚化のピン留め
1. **[ダッシュボード]** の横にあるプラス記号を選択して、空のダッシュボードを作成します。 その名前として **Vehicle Telemetry Analytics Dashboard** を入力します。

    ![ダッシュボードのプラス記号](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

1. 前のレポートからこのダッシュボードに視覚エフェクトをピン留めします。 

    ![ダッシュボードのピン留め記号](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    3 つのすべてのレポートをダッシュボードにピン留めすると、次のようになります。 レポートの一部を作成していない場合、ダッシュボードの外観はこの図とは異なったものになることがあります。 

    ![レポートが表示されたダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

これで、リアルタイム ダッシュボードが正常に作成されました。 続けて CarEventGenerator.exe と RealtimeDashboardApp.exe を実行すると、ダッシュボードにライブ更新が表示されるようになります。 以下の手順を完了するには、10 - 15 分ほどかかります。

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Power BI バッチ処理ダッシュボードのセットアップ
> [!NOTE]
> エンド ツー エンドのバッチ処理パイプラインの実行が完了し、1 年分の生成済みデータが処理されるまでに、(デプロイの正常な完了から) 約 2 時間かかります。 処理が終了するまで待ってから、次の手順に進んでください。
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Power BI デザイナー ファイルのダウンロード

1. 事前に構成された Power BI デザイナー ファイルは、デプロイの手動操作手順の一部として含まれています。 「2. Set up the Power BI batch processing dashboard (Power BI バッチ処理ダッシュボードのセットアップ)」をご覧ください。

1. ここで、**ConnectedCarsPbiReport.pbix** という名前のバッチ処理ダッシュボード用 Power BI テンプレートをダウンロードします。

1. それをローカルに保存します。

### <a name="configure-power-bi-reports"></a>Power BI レポートを構成

1. Power BI Desktop を使用して、デザイナー ファイル **ConnectedCarsPbiReport.pbix** を開きます。 Power BI Desktop をまだインストールしていない場合は、[Power BI Desktop のインストール](http://www.microsoft.com/download/details.aspx?id=45331)に関する Web サイトからインストールします。

1. **[クエリを編集]** を選択します。

    ![クエリの編集](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

1. **[ソース]** をダブルクリックします。

    ![ソース](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

1. デプロイの一部としてプロビジョニングされた Azure SQL Server を使用してサーバー接続文字列を更新します。 手動操作手順の Azure SQL Database　の下の項目を参照してください。

    * サーバー: somethingsrv.database.windows.net
    * データベース: connectedcar
    * ユーザー名: username
    * パスワード: SQL Server のパスワードは Azure Portal で管理できます。

1. **[データベース]** の設定を **connectedcar**のままにします。

    ![Database](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

1. **[OK]** を選択します。

1. **[Windows 資格情報]** タブは既定で選択されています。 右側の **[データベース]** タブを選択して、それを **[データベース資格情報]** に変更します。

1. デプロイのセットアップ中に指定した Azure SQL Database の**ユーザー名**と**パスワード**を入力します。

    ![データベース資格情報](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

1. **[接続]** を選択します。

1. 右側のウィンドウにある残り 3 つの各クエリについて、上記の手順を繰り返します。 その後、データ ソース接続の詳細を更新します。

1. **[閉じる] と [読み込む]** を選択します。 Power BI Desktop ファイルのデータセットが SQL データベース テーブルに接続されます。

1. **[閉じる]** を選択して Power BI Desktop ファイルを閉じます。

    ![閉じます](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

1. **[保存]** を選択して変更を保存します。 

これで、ソリューション内のバッチ処理パスに対応するすべてのレポートを構成しました。 

## <a name="upload-to-powerbicom"></a>powerbi.com へのアップロード
1. [Power BI Web ポータル](http://powerbi.com) にアクセスし、サインインします。

1. **[データの取得]** を選択します。

1. Power BI Desktop ファイルをアップロードします。 **[データの取得]** > **[Files Get]\(取得ファイル\)** > **[ローカル ファイル]** を選択します。

1. **ConnectedCarsPbiReport.pbix** に移動します。

1. ファイルをアップロードしたら、ご自分の Power BI ワークスペースに戻ります。 ご自分用にデータセット、レポート、空のダッシュボードが作成されます。  

1. Power BI 内の **Vehicle Telemetry Analytics Dashboard** という名前の新しいダッシュボードにグラフをピン留めします。 前に作成した空のダッシュボードを選択し、**[レポート]** セクションに移動します。 新しくアップロードされたレポートを選択します。  

    ![新しい Power BI ダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    レポートには、次の 6 つのページがあります。

    ページ 1: 車両の密度  
    ページ 2: リアルタイムの車両状態  
    ページ 3: アグレッシブに運転されている車両   
    ページ 4: リコールされた車両  
    ページ 5: 低燃費で運転されている車両  
    ページ 6: Contoso Motors のロゴ  

    ![6 つのページがある Power BI レポート](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

1. **ページ 3 から**、以下の内容をピン留めします。  

    a. **[Count of vin]**  

   ![ページ 3 の [Count of vin]](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Aggressively driven vehicles by model – ウォーターフォール図** 

   ![ページ 3 のグラフ 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

1. **ページ 5** から、以下の内容をピン留めします。 

    a. **[Count of vin]**

   ![ページ 5 のグラフ 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Fuel-efficient vehicles by model: 集合縦棒グラフ**

   ![ページ 5 のグラフ 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

1. **ページ 4 から**、以下の内容をピン留めします。  

    a. **[Count of vin]** 

   ![ページ 4 のグラフ 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Recalled vehicles by city, model: ツリーマップ**

   ![ページ 4 のグラフ 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

1. **ページ 6 から**、以下の内容をピン留めします。  

    * **Contoso Motors のロゴ**

    ![Contoso Motors のロゴ ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>ダッシュボードの整理  

1. ダッシュボードに戻ります。

1. 各グラフをポイントします。 次の完成したダッシュボードの例で示されている命名に基づいて各グラフの名前を変更します。

   ![ダッシュボード編成](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
1. 以下のダッシュボード例と同じになるように、グラフを移動します。

    ![再配置されたダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

1. このドキュメントで示されているすべてのレポートを作成すると、最終的な完成したダッシュボードは次のようになります。 

   ![最終的なダッシュボード](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

レポートとダッシュボードが正常に作成され、車両の状態と運転習慣に関するリアルタイム情報や予測情報を一括して把握できるようになりました。  
