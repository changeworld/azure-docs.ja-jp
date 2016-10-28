<properties 
	pageTitle="Azure Data Factory のパイプラインの監視と管理" 
	description="監視と管理アプリを使用して Azure のデータ ファクトリとパイプラインを監視および管理する方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="spelluru"/>

# 新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理
> [AZURE.SELECTOR]
- [Azure ポータルと Azure PowerShell の使用](data-factory-monitor-manage-pipelines.md)
- [Monitoring and Management App の使用](data-factory-monitor-manage-app.md)

この記事では、**監視と管理アプリ**を使用して、パイプラインを監視、管理、デバッグする方法、およびアラートを作成して障害について通知を受け取る方法について説明します。監視と管理アプリの使用方法の詳細については、次のビデオを視聴することもできます。
   

> [AZURE.VIDEO azure-data-factory-monitoring-and-managing-big-data-piplines]
      
## 監視と管理アプリの起動
監視と管理アプリを起動するには、該当するデータ ファクトリの **[DATA FACTORY]** ブレードで **[監視と管理]** タイルをクリックします。

![Monitoring tile on Data Factory home page](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

監視と管理アプリが別のタブまたはウィンドウで起動したことがわかります。

![Monitoring and Management App](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] 承認中であることを示すメッセージが表示されたまま Web ブラウザーが固まってしまう場合は、**サード パーティの Cookie とサイト データをブロック**する設定を無効にしてください。または、有効な状態のまま **login.microsoftonline.com** に対する例外を作成し、そのうえで、もう一度アプリを起動してください。


下部にある一覧でアクティビティ ウィンドウを確認できない場合は、ツールバーの **[更新]** ボタンをクリックして、一覧を更新します。さらに、**[開始時刻]** および **[終了時刻]** フィルターに適切な値を設定します。


## 監視と管理アプリについて
左側には 3 つのタブ (**[リソース エクスプローラー]**、**[監視ビュー]**、**[アラート]**) があり、既定では、1 つ目のタブ ([リソース エクスプローラー]) が選択されています。

### Resource Explorer
次のように表示されます。

- 左側ウィンドウにリソース エクスプローラーの**ツリー ビュー**
- 上部に**ダイアグラム ビュー**
- 中央ウィンドウの下部に**アクティビティ ウィンドウ**
- 右側ウィンドウに **[プロパティ]** タブと **[Activity Window Explorer (アクティビティ ウィンドウ エクスプローラー)]** タブ

リソース エクスプローラーでは、データ ファクトリのすべてのリソース (パイプライン、データセット、リンクされたサービス) がツリー ビューに表示されます。リソース エクスプローラーでオブジェクトを選択する際は、次の点に注意します。

- 関連付けられている Data Factory エンティティはダイアグラム ビューで強調表示されます。
- 関連付けられているアクティビティ ウィンドウ (アクティビティ ウィンドウの詳細については[こちら](data-factory-scheduling-and-execution.md)を参照してください) は、下部にある [アクティビティ ウィンドウ] の一覧で強調表示されます。
- 選択したオブジェクトのプロパティは、右側のウィンドウにある [プロパティ] ウィンドウに表示されます。
- 該当する場合は、選択したオブジェクトの JSON 定義が表示されます。例: リンクされたサービス、データセット、パイプライン

![Resource Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

アクティビティ ウィンドウの概念の詳細については、記事「[スケジュールと実行](data-factory-scheduling-and-execution.md)」を参照してください。

### ダイアグラム ビュー
Data Factory のダイアグラム ビューでは、Data Factory とその資産を監視および管理する 1 つのウィンドウが提供されます。ダイアグラム ビューで Data Factory エンティティ (データセットまたはパイプライン) を選択する際は、次の点に注意します。
 
- Data Factory エンティティはツリー ビューで選択されます。
- 関連付けられているアクティビティ ウィンドウは [アクティビティ ウィンドウ] の一覧で強調表示されます。
- 選択したオブジェクトのプロパティは [プロパティ] ウィンドウに表示されます。

パイプラインは、有効な場合 (一時停止状態ではない場合) は緑の線付きで示されます。

![Pipeline Running](./media/data-factory-monitor-manage-app/PipelineRunning.png)

ダイアグラム ビューには、パイプライン用に 3 つのコマンド ボタンがあります。2 番目のボタンを使用すると、パイプラインを一時停止できます。これによって、現在実行中のアクティビティが終了することはありません。アクティビティは完了するまで続行されます。3 番目のボタンは、パイプラインを一時停止し、その既に実行中のアクティビティを終了します。1 番目のボタンは、パイプラインを再開します。パイプラインが一時停止されると、パイプラインのタイルの色が次のように変わります。

![Pause/Resume on Tile](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

(Ctrl キーを使用して) 複数のパイプラインを選択し、コマンド バーのボタンを使用すると、複数のパイプラインを同時に一時停止/再開することができます。

![Pause/Resume on Command bar](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

パイプラインのすべてのアクティビティを表示するには、パイプラインのタイルを右クリックし、**[パイプラインを開く]** をクリックします。

![Open Pipeline menu](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

開かれたパイプラインのビューには、パイプラインのすべてのアクティビティが表示されます。この例では、コピー アクティビティというアクティビティが 1 つだけあります。前のビューに戻るには、上部にある階層リンク メニューのデータ ファクトリ名をクリックします。

![Opened Pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

パイプライン ビューで、出力データセットをクリックするか出力データセットにマウスカーソルを重ねると、そのデータセットの [アクティビティ ウィンドウ] ポップアップが表示されます。

![Activity Windows popup](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

アクティビティ ウィンドウをクリックすると、右側にある **[プロパティ]** ウィンドウにその詳細が表示されます。

![Activity Window Properties](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

右側のウィンドウで、**[アクティビティ ウィンドウ エクスプローラー]** に切り替えてさらに詳細を表示します。

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

アクティビティの実行が試みられるたびに **[試行]** セクションに **解決された変数**も表示されます。

![解決された変数](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

**[スクリプト]** タブに切り替えて、選択したオブジェクトの JSON スクリプト定義を確認します。

![[スクリプト] タブ](./media/data-factory-monitor-manage-app/ScriptTab.png)

アクティビティ ウィンドウは、次の 3 か所で確認できます。

- ダイアグラム ビューに表示される [アクティビティ ウィンドウ] ポップアップ (中央のウィンドウ)。
- 右側のウィンドウにあるアクティビティ ウィンドウ エクスプローラー。
- 下部のウィンドウにある [アクティビティ ウィンドウ] の一覧。

[アクティビティ ウィンドウ] ポップアップとアクティビティ ウィンドウ エクスプローラーでは、左矢印と右矢印を使用して前の週と次の週にスクロールすることができます。

![Activity Window Explorer Left/Right Arrows](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

ダイアグラム ビューの下部には、拡大、縮小、画面に合わせたサイズ変更、100 % 表示、レイアウト ロックを行うためのボタンが表示されます。レイアウト ロック ボタンは、ダイアグラム ビューでテーブルやパイプラインが誤って動かされないようにするためのものであり、既定ではオンになっています。このボタンをオフにすると、ダイアグラム内でエンティティを移動できます。オフにするときに、最後のボタンを使用してテーブルやパイプラインを自動的に配置できます。また、マウス ホイールを使用して拡大/縮小することもできます。

![Diagram View Zoom commands](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### [アクティビティ ウィンドウ] の一覧
中央のウィンドウの下部にある [アクティビティ ウィンドウ] の一覧には、リソース エクスプローラーまたはダイアグラム ビューで選択したデータセットのアクティビティ ウィンドウすべてが表示されます。既定では、この一覧は降順になっています。つまり、最新のアクティビティ ウィンドウが先頭に表示されます。

![Activity Windows List](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

この一覧は自動的に更新されないため、ツール バーの更新ボタンを使用するか、手動で更新してください。


アクティビティ ウィンドウは、次の状態のいずれかになります。

<table>
<tr>
	<th align="left">状態</th><th align="left">副状態</th><th align="left">Description</th>
</tr>
<tr>
	<td rowspan="8">待機中</td><td>ScheduleTime</td><td>アクティビティ ウィンドウが実行される時刻になっていません。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>アップ ストリームの依存関係の準備ができていません。</td>
</tr>
<tr>
<td>ComputeResources</td><td>コンピューティング リソースが使用できません。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>すべてのアクティビティ インスタンスが、他のアクティビティ ウィンドウを実行しています。</td>
</tr>
<tr>
<td>ActivityResume</td><td>アクティビティは一時停止しており、再開されるまでアクティビティ ウィンドウを実行できません。</td>
</tr>
<tr>
<td>Retry</td><td>アクティビティの実行が再試行されます。</td>
</tr>
<tr>
<td>検証</td><td>検証がまだ開始されていません。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>検証の再試行を待機しています。</td>
</tr>
<tr>
&lt; tr
<td rowspan="2">InProgress</td><td>Validating</td><td>検証を実行中です。</td>
</tr>
<td></td>
<td>アクティビティ ウィンドウが処理されています。</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>アクティビティで許可されている実行時間を超過しました。</td>
</tr>
<tr>
<td>Canceled</td><td>ユーザーの操作によって取り消されました。</td>
</tr>
<tr>
<td>検証</td><td>検証が失敗しました。</td>
</tr>
<tr>
<td></td><td>アクティビティ ウィンドウの生成またはアクティビティ ウィンドウの検証に失敗しました。</td>
</tr>
<td>Ready</td><td></td><td>アクティビティ ウィンドウは使用可能な状態です。</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>アクティビティ ウィンドウが処理されていません。</td>
</tr>
<tr>
<td>なし</td><td></td><td>別のステータスで存在していたが、リセットされたアクティビティ ウィンドウです。</td>
</tr>
</table>


一覧でアクティビティ ウィンドウをクリックすると、右側にある **[アクティビティ ウィンドウ エクスプローラー]** または**[プロパティ]** ウィンドウに詳細が表示されます。

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### アクティビティ ウィンドウの更新  
詳細は自動的に更新されないため、コマンド バーの **[更新]** ボタン (2 番目のボタン) を使用して、アクティビティ ウィンドウの一覧を手動で更新します。
 

### [プロパティ] ウィンドウ
[プロパティ] ウィンドウは、監視と管理アプリの右端のウィンドウにあります。

![Properties window](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

ここには、リソース エクスプローラー (ツリー ビュー)、ダイアグラム ビュー、または [アクティビティ ウィンドウ] の一覧で選択した項目のプロパティが表示されます。

### Activity Window Explorer

**[アクティビティ ウィンドウ エクスプローラー]** ウィンドウは、監視と管理アプリの右端のウィンドウにあります。ここには、[アクティビティ ウィンドウ] ポップアップまたは [アクティビティ ウィンドウ] の一覧で選択したアクティビティ ウィンドウの詳細が表示されます。

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

別のアクティビティ ウィンドウに切り替えるには、上部にあるカレンダー ビューでそのアクティビティ ウィンドウをクリックします。上部にある**左矢印**/**右矢印**ボタンを使用すると、前/次の週のアクティビティ ウィンドウを表示することもできます。

下部のウィンドウにあるツール バー ボタンを使用して、アクティビティ ウィンドウを**再実行**したり、そのウィンドウの詳細を**更新**したりすることができます。

### スクリプト 
**[スクリプト]** タブを使用して、選択した Data Factory エンティティ (リンクされたサービス、データセット、およびパイプライン) の JSON 定義を表示することができます。

![[スクリプト] タブ](./media/data-factory-monitor-manage-app/ScriptTab.png)

## システム ビューの使用
監視と管理アプリには、既成のシステム ビュー (**最近のアクティビティ ウィンドウ**、**失敗したアクティビティ ウィンドウ**、**進行中のアクティビティ ウィンドウ**) が用意されています。これらを使用すると、該当するデータ ファクトリの最近のアクティビティ ウィンドウ、失敗したアクティビティ ウィンドウ、進行中のアクティビティ ウィンドウを確認できます。

左側の **[監視ビュー]** タブをクリックして切り替えます。

![Monitoring Views tab](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

現時点でサポートされているシステム ビューは 3 つあります。(中央のウィンドウの下部にある) [アクティビティ ウィンドウ] の一覧に最近のアクティビティ ウィンドウ、失敗したアクティビティ ウィンドウ、または進行中のアクティビティ ウィンドウを表示するオプションを選択します。

**[最近のアクティビティ ウィンドウ]** オプションを選択すると、最近のアクティビティ ウィンドウすべてが**最終試行時刻**の降順で表示されます。

**[失敗したアクティビティ ウィンドウ]** ビューを使用すると、失敗したアクティビティ ウィンドウすべてを一覧に表示できます。一覧で失敗したアクティビティ ウィンドウを選択すると、その詳細が **[プロパティ]** ウィンドウまたは**アクティビティ ウィンドウ エクスプローラー**に表示されます。また、失敗したアクティビティ ウィンドウのログをダウンロードすることもできます。


## アクティビティ ウィンドウの並べ替えとフィルター処理
アクティビティ ウィンドウをフィルター処理するには、コマンド バーの **[開始時刻]** と **[終了時刻]** の設定を変更します。開始時刻と終了時刻を変更したら、終了時刻の横にあるボタンをクリックして、[アクティビティ ウィンドウ] の一覧を更新します。

![Start and End Times](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] 現時点では、監視と管理アプリの時刻はすべて UTC 形式になっています。

**[アクティビティ ウィンドウ]** の一覧で、列の名前 (例: 状態) をクリックします。

![Activity Windows List column menu](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

以下の操作を行うことができます。

- 昇順で並べ替える。
- 降順で並べ替える。
- 1 つ以上の値 ("準備完了"、"待機中" など) でフィルター処理する。

列にフィルターを指定すると、その列のフィルター ボタンが有効になり、列の値がフィルター処理された値を示していることがわかります。

![Filter in column of Activity Windows list](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

フィルターをクリアするには、同じポップアップ ウィンドウを使用できます。[アクティビティ ウィンドウ] の一覧のフィルターをすべてクリアするには、コマンド バーにある [フィルターのクリア] ボタンをクリックします。

![Clear all filters in Activity Windows list](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## バッチ操作の実行

### 選択したアクティビティ ウィンドウを再実行する
アクティビティ ウィンドウを選択して、1 つ目のコマンド バー ボタンの下向き矢印をクリックし、**[再実行]** または **[パイプラインのアップストリームによる再実行]** を選択します。**[パイプラインのアップストリームによる再実行]** オプションを選択すると、アップストリーム アクティビティ ウィンドウもすべて再実行されます。![Rerun a activity window](./media/data-factory-monitor-manage-app/ReRunSlice.png)

また、一覧で複数のアクティビティ ウィンドウを選択して、それらを同時に再実行することもできます。状態 (例: **失敗**) でアクティビティ ウィンドウをフィルター処理してから、アクティビティ ウィンドウが失敗した原因となる問題を解決した後に、失敗したアクティビティ ウィンドウを再実行することが必要になる場合もあります。一覧にあるアクティビティ ウィンドウのフィルター処理の詳細については、次のセクションを参照してください。

### 複数のパイプラインを一時停止/再開する
(Ctrl キーを使用して) 複数のパイプラインを選択し、コマンド バーのボタン (次の図で赤の四角形で囲まれています) を使用すると、選択したパイプラインを同時に一時停止または再開することができます。

![Suspend/Resume on Command bar](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## アラートの作成 
[アラート] ページでは、アラートの作成のほか、既存のアラートの表示、編集、削除を実行できます。また、アラートを無効/有効にすることもできます。[アラート] ページを表示するには、[アラート] タブをクリックします。

![Alerts tab](./media/data-factory-monitor-manage-app/AlertsTab.png)

### アラートを作成するには

1. **[アラートの追加]** をクリックしてアラートを追加します。[詳細] ページが表示されます。

	![Create Alerts - Details page](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. アラートの**名前**と**説明**を指定し、**[次へ]** をクリックします。**[フィルター]** ページが表示されます。

	![Create Alerts - Filters page](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

2. Data Factory サービスからアラートを受け取る**イベント**、**状態**、および**副状態** (オプション) を選択し、**[次へ]** をクリックします。**[宛先]** ページが表示されます。

	![Create Alerts - Recipients page](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png)
3. **[サブスクリプション管理者に電子メールを送る]** オプションを選択するか **[追加する管理者の電子メール]** を入力して、**[完了]** をクリックします。アラートが一覧に表示されます。
	
	![Alerts list](./media/data-factory-monitor-manage-app/AlertsList.png)

[アラート] の一覧で、アラートの編集/削除/無効化/有効化を実行するには、アラートに関連付けられたボタンを使用します。

### イベント/状態/副状態
次の表では、使用できるイベントと状態 (および副状態) の一覧を示します。

イベント名 | 状態 | 副状態
-------------- | ------ | ----------
アクティビティの実行が開始されました | 開始済み | 開始中
アクティビティの実行が完了しました | Succeeded | Succeeded 
アクティビティの実行が完了しました | Failed| リソースの割り当ての失敗<br/><br/>実行の失敗<br/><br/>タイムアウト<br/><br/>検証の失敗<br/><br/>中止
オンデマンド HDI クラスターの作成が開始されました | 開始済み | &nbsp; |
オンデマンド HDI クラスターが正常に作成されました | Succeeded | &nbsp; |
オンデマンド HDI クラスターが削除されました | Succeeded | &nbsp; |
### アラートを編集/削除/無効にするには


![Alerts buttons](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0907_2016-->