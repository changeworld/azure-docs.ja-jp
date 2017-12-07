---
title: "Azure Log Analytics データを Power BI にインポートする | Microsoft Docs"
description: "Power BI は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  この記事では Log Analytics のデータを Power BI にインポートする構成と、それを自動的に更新する構成について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Power BI への Azure Log Analytics データのインポート


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  Log Analytics ログ検索の結果を Power BI のデータセットにインポートできます。そうすることでさまざまなソースのデータを組み合わせたり、Web およびモバイル デバイス上でレポートを共有したりするような機能を利用できます。

この記事では Log Analytics のデータを Power BI にインポートし、自動的に更新されるようスケジュールする方法を説明します。  [アップグレードされた](#upgraded-workspace)ワークスペースと[レガシ](#legacy-workspace) ワークスペースでは異なるプロセスが含まれます。

## <a name="upgraded-workspace"></a>アップグレードされたワークスペース


[Log Analytics のアップグレードされたワークスペース](log-analytics-log-search-upgrade.md)のデータを Power BI にインポートするには、Log Analytics のログ検索クエリに基づくデータセットを Power BI で作成します。  クエリはデータセットが更新されるたびに実行されます。  それからデータセットのデータを使用した Power BI レポートを作成できます。  Power BI でデータセットを作成するには、Log Analytics から [Power Query (M) 言語](https://msdn.microsoft.com/library/mt807488.aspx)にクエリをエクスポートします。  次にこのクエリを使用して Power BI Desktop でクエリを作成し、Power BI にデータセットとして発行します。  このプロセスの詳細を以下で説明します。

![Power BI への Log Analytics](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>クエリをエクスポートする
まず、Power BI データセットに設定するデータを Log Analytics が返すような[ログ検索](log-analytics-log-search-new.md)を作成します。  次にそのクエリを Power BI Desktop で使用できる [Power Query (M) 言語](https://msdn.microsoft.com/library/mt807488.aspx)にエクスポートします。

1. データセットからデータを抽出するために、Log Analytics でログ検索を作成します。
2. ログ検索ポータルを使用する場合は、**[Power BI]** をクリックします。  Analytics ポータルを使用する場合は、**[エクスポート]** > **[Power BI Query (M)]** の順に選択します。  このオプションはどちらも **PowerBIQuery.txt** という名前のテキスト ファイルをエクスポートします。 

    ![ログ検索のエクスポート](media/log-analytics-powerbi/export-logsearch.png) ![ログ検索のエクスポート](media/log-analytics-powerbi/export-analytics.png)

3. テキスト ファイルを開き、その内容をコピーします。

### <a name="import-query-into-power-bi-desktop"></a>Power BI Desktop にクエリをインポートする
Power BI Desktop は、Power BI に発行可能なデータセットおよび レポートを作成できるデスクトップ アプリケーションです。  これを使用して、Log Analytics からエクスポートした Power Query 言語を使ってクエリを作成することもできます。 

1. まだお持ちでない場合は [Power BI Desktop](https://powerbi.microsoft.com/desktop/) をインストールし、アプリケーションを開きます。
2. **[データの取得]** > **[空のクエリ]** の順に選択して新しいクエリを開きます。  次に **[詳細エディター]** を選択し、エクスポートしたファイルの内容をクエリに貼り付けます。 **[Done]**をクリックします。

    ![Power BI Desktop クエリ](media/log-analytics-powerbi/desktop-new-query.png)

5. クエリが実行され、結果が表示されます。  Azure に接続するための資格情報を要求される場合があります。  
6. クエリにわかりやすい名前を入力します。  規定値は "**Query1**" です。 **[閉じて適用]** をクリックして、レポートにデータセットを追加します。

    ![Power BI Desktop の名前](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Power BI に発行する
Power BI に発行すると、データセットとレポートが作成されます。  Power BI Desktop でレポートを作成すると、お使いのデータを使用したレポートが発行されます。  そうしない場合は、空のレポートが作成されます。  Power BI でレポートを変更したり、データセットに基づく新しいレポートを作成したりできます。

8. お使いのデータに基づくレポートを作成します。  不慣れな場合には [Power BI Desktop のドキュメント](https://docs.microsoft.com/power-bi/desktop-report-view)を使用してください。  Power BI に送信する準備ができたら、**[発行]** をクリックします。  メッセージが表示されたら、Power BI アカウント内で発行先を選択します。  特定の発行先がない限りは、**[マイ ワークスペース]** を使用します。

    ![Power BI Desktop の発行](media/log-analytics-powerbi/desktop-publish.png)

3. 発行が完了したら **[Power BI で開く]** をクリックし、新しいデータセットで Power BI を開きます。


### <a name="configure-scheduled-refresh"></a>スケジュールされた更新を構成する
Power BI で作成されたデータセットには、前に Power BI Desktop で見たのと同じデータがあります。  再度クエリを実行して Log Analytics の最新のデータを設定するために、データセットを定期的に更新する必要があります。  

1. レポートをアップロードしたワークスペースをクリックし、**[データセット]** メニューを選択します。 新しいデータセットの横にあるコンテキスト メニューを選択し、**[設定]** を選択します。 **[データ ソースの資格情報]** に、資格情報が無効であることを示すメッセージがあるはずです。  これは、そのデータを更新するときにデータセットが使用する資格情報を、まだ指定していないためです。  **[資格情報の編集]** をクリックし、Log Analytics にアクセスできる資格情報を指定します。

    ![Power BI のスケジュール](media/log-analytics-powerbi/powerbi-schedule.png)

5. **[スケジュールされている更新]** で、**[データを最新の状態に保つ]** オプションをオンにします。  必要に応じて **[更新の頻度]** および更新を実行する 1 つまたは複数の特定の時間を変更できます。

    ![Power BI の更新](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>レガシ ワークスペース
[Log Analytics レガシ ワークスペース](log-analytics-powerbi.md)で Power BI を構成する場合は、Power BI の対応するデータセットに結果をエクスポートするログ クエリを作成します。  クエリとエクスポートは、Log Analytics によって収集された最新データでデータセットを保持することを定義するスケジュール上で自動的に実行を続けます。

![Power BI への Log Analytics](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Power BI スケジュール
*Power BI スケジュール* には、OMS リポジトリから Power BI の対応するデータセットにデータのセットをエクスポートするログ検索と、この検索を実行してデータセットを最新のまま保持する頻度を定義するスケジュールが含まれます。

データセットのフィールドは、ログ検索によって返されるレコードのプロパティと一致します。  検索によってさまざまな種類のレコードが返される場合、データセットには、含まれているレコードの各種類のプロパティがすべて含まれます。  

### <a name="connecting-oms-workspace-to-power-bi"></a>OMS ワークスペースを Power BI に接続する
Log Analytics から Power BI にエクスポートする前に、次の手順に従って、OMS ワークスペースを Power BI アカウントに接続する必要があります。  

1. OMS コンソールで **[設定]** タイルをクリックします。
2. **[アカウント]**を選択します。
3. **[Workspace Information (ワークスペース情報)]** セクションで **[Connect to Power BI Account (Power BI アカウントに接続する)]** をクリックします。
4. Power BI アカウントの資格情報を入力します。

### <a name="create-a-power-bi-schedule"></a>Power BI スケジュールの作成
次の手順に従って、データセットごとに Power BI スケジュールを作成します。

1. OMS コンソールで **[Log Search]** (ログの検索) タイルをクリックします。
2. 新しいクエリで入力するか、または **Power BI**にエクスポートするデータを返す保存された検索を選択します。  
3. ページの上部にある **[Power BI]** ボタンをクリックして、**[Power BI]** ダイアログ ボックスを開きます。
4. 次の表にある情報を入力して、 **[保存]**をクリックします。

| プロパティ | Description |
|:--- |:--- |
| 名前 |Power BI スケジュールの一覧を表示するときにスケジュールを識別する名前。 |
| 保存する検索条件 |実行するログ検索。  現在のクエリを選択するか、またはドロップダウン ボックスから既存の保存された検索を選択します。 |
| スケジュール |保存された検索を実行して Power BI データセットにエクスポートする頻度。  値は 15 分～ 24 時間の範囲で指定する必要があります。 |
| データセット名 |Power BI のデータセットの名前。  存在しない場合は作成し、存在する場合は更新します。 |

### <a name="viewing-and-removing-power-bi-schedules"></a>Power BI スケジュールの表示と削除
次の手順で既存の Power BI スケジュールの一覧を表示します。

1. OMS コンソールで **[設定]** タイルをクリックします。
2. **[Power BI]**を選択します。

スケジュールの詳細に加えて、スケジュールが過去 1 週間に実行した回数と、前回の同期の状態が表示されます。  同期にエラーが発生した場合は、リンクをクリックして、エラーの詳細を含むレコードのログ検索を実行することができます。

スケジュールを削除するには、**[削除]** 列で **[X]** をクリックします。  スケジュールを無効にするには、 **[オフ]**を選択します。  スケジュールを変更するには、それを削除し、新しい設定で再作成する必要があります。

![Power BI スケジュール](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>サンプルのチュートリアル
次のセクションでは、Power BI スケジュールを作成し、そのデータセットを使用して、簡単なレポートを作成する例を段階的に説明します。  この例では、コンピューターのセットのすべてのパフォーマンス データを Power BI にエクスポートし、線グラフを作成してプロセッサ使用率を表示します。

#### <a name="create-log-search"></a>ログ検索の作成
まず、データセットに送信するデータのログ検索を作成します。  この例では、 *srv*で始まる名前を持つコンピューターのすべてのパフォーマンス データを返すクエリを使用します。  

![Power BI スケジュール](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Power BI 検索の作成
**[Power BI]** ボタンをクリックして、[Power BI] ダイアログ ボックスを開き、必要な情報を入力します。  この検索を 1 時間あたり 1 回実行するとします。そこで、*Contoso Perf* という名前のデータセットを作成します。  必要なデータを作成する検索は既に開かれているため、**[Saved Search (保存した検索条件)]** は既定の *[Use current search query (現在の検索クエリを使用する)]* のままにしておきます。

![Power BI 検索](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Power BI 検索の確認
スケジュールが正しく作成されたことを確認するには、OMS のダッシュボードの **[設定]** タイルにある Power BI 検索の一覧を表示します。  数分間待機し、同期が実行されたことを報告するまで、このビューを更新します。  通常、データセットは自動的に更新されるようにスケジュールします。

![Power BI 検索](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Power BI でのデータセットの確認
[powerbi.microsoft.com](http://powerbi.microsoft.com/) でアカウントにログインして、左側のウィンドウの下部にある **[データセット]** までスクロールします。  エクスポートが正常に実行されたことを示す *[Contoso Perf]* データセットが表示されていることを確認できます。

![Power BI データセット](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>データセットに基づくレポートの作成
**[Contoso Perf]** データセットを選択し、右側の **[フィールド]** ウィンドウにある **[結果]** をクリックして、このデータセットの一部であるフィールドを表示します。  各コンピューターのプロセッサ使用率を示す線グラフを作成するには、次の操作を実行します。

1. 線グラフの視覚エフェクトを選択します。
2. **[ObjectName]** を **[Report level filter (レポート レベルのフィルター)]** にドラッグして、**[プロセッサ]** チェック ボックスをオンにします。
3. **[CounterName]** を **[Report level filter (レポート レベルのフィルター)]** にドラッグして、**[% Processor Time (% プロセッサ時間)]** チェック ボックスをオンにします。
4. **[CounterValue]** を **[値]** にドラッグします。
5. **[Computer]** を **[凡例]** にドラッグします。
6. **[TimeGenerated]** を **[軸]** にドラッグします。

この例のデータセットからデータを結果として得られる線グラフが表示されることがわかります。

![Power BI 線グラフ](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>レポートの保存
画面の上部にある [保存] ボタンをクリックしてレポートを保存し、左側のウィンドウの [レポート] セクションにレポートが一覧表示されていることを検証します。

![Power BI レポート](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、Power BI にエクスポートできるクエリを作成します。
* [Power BI](http://powerbi.microsoft.com) について詳しく学習し、Log Analytics のエクスポートに基づく視覚エフェクトを作成します。
