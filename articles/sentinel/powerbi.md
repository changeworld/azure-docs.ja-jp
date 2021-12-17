---
title: Microsoft Azure Sentinel のデータから Power BI レポートを作成する
description: Microsoft Azure Sentinel の Log Analytics からエクスポートされたクエリを使用して Power BI レポートを作成する方法について学習します。 レポートを Power BI サービスや Teams チャネルの他のユーザーと共有します。
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cd6c6de1fc1a83cecdf0ea96e912f989bdf6b904
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521322"
---
# <a name="tutorial-create-a-power-bi-report-from-microsoft-sentinel-data"></a>チュートリアル: Microsoft Azure Sentinel のデータから Power BI レポートを作成する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Power BI](https://powerbi.microsoft.com/) は、データをイマーシブで一貫性のある対話型の視覚化に変えるレポートおよび分析プラットフォームです。 Power BI を使用すると、データ ソースへの接続、リレーションシップの視覚化と検出、必要に応じた分析情報の共有を簡単に実行できます。

Microsoft Azure Sentinel の Log Analytics ワークスペースからのデータに基づいて Power BI レポートを作成し、Microsoft Azure Sentinel にアクセスできないユーザーとレポートを共有できます。 たとえば、Microsoft Azure Sentinel へのアクセスを許可することなく、失敗したサインイン試行に関する情報をアプリの所有者と共有できます。 Power BI の視覚化を使用すると、データを一目で確認できます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Log Analytics の Kusto クエリを Power BI の M 言語クエリにエクスポートします。
> * Power BI Desktop の M クエリを使用して、視覚化とレポートを作成します。
> * レポートを Power BI サービスに公開して、他のユーザーと共有します。
> * Teams チャネルにレポートを追加します。

Power BI サービスでアクセスが許可されたユーザーと Teams チャネルのメンバーは、Microsoft Azure Sentinel のアクセス許可を必要とすることなくレポートを表示できます。

> [!NOTE]
> このチュートリアルでは、Microsoft Azure Sentinel のデータの分析レポートを Power BI で表示するという、お客様からのよくある質問に対するシナリオ ベースの手順を提供します。 詳細については、「[データ ソースの接続](connect-data-sources.md)」と「[収集されたデータを視覚化する](get-visibility.md)」を参照してください。
>
## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- 少なくとも、サインイン試行を監視する Microsoft Azure Sentinel の Log Analytics ワークスペースへの読み取りアクセス。
- Log Analytics ワークスペースの読み取りアクセスを持つ Power BI アカウント。
- [Microsoft Store からインストールされた Power BI Desktop](https://aka.ms/pbidesktopstore)。

## <a name="export-a-query-from-log-analytics"></a>Log Analytics からクエリをエクスポートする

Microsoft Azure Sentinel の Log Analytics ワークスペースで、Kusto クエリを作成、実行、エクスポートします。 

1. 単純なクエリを作成するには、Microsoft Azure Sentinel の Log Analytics ワークスペースで **[ログ]** を選択します。 クエリ エディターの **[新しいクエリ 1]** で、次の Kusto クエリを入力します。
   
   ```kusto
   SigninLogs
   |  where TimeGenerated >ago(7d)
   | summarize Attempts = count(), Failed=countif(ResultType !=0), Succeeded = countif(ResultType ==0) by AppDisplayName
   |  top 10 by Failed
   | sort by Failed
   ```
   
   または、お気に入りの Microsoft Azure Sentinel の Log Analytics の Kusto クエリを使用します。
   
1. **[実行]** を選択してクエリを実行し、結果を生成します。
   
   :::image type="content" source="media/powerbi/query.png" alt-text="Log Analytics の Kusto のクエリと結果を示すスクリーンショット。":::
   
1. このクエリを Power BI の M クエリ形式にエクスポートするには、 **[エクスポート]** を選択してから、 **[Power BI (M クエリ) にエクスポート]** を選択します。 Log Analytics によって、*PowerBIQuery.txt* という名前のテキスト ファイルにクエリがエクスポートされます。
   
   :::image type="content" source="media/powerbi/export.png" alt-text="Power BI の M 形式へのクエリの [エクスポート] を示すスクリーンショット。":::
   
1. エクスポートされたファイルの内容をコピーします。

## <a name="get-the-data-in-power-bi-desktop"></a>Power BI Desktop でデータを取得する

エクスポートされた M クエリを Power BI Desktop で実行して、データを取得します。

1. Power BI Desktop を開いて、Log Analytics ワークスペースへの読み取りアクセスを持つ Power BI アカウントにサインインします。
   
   :::image type="content" source="media/powerbi/sign-in.png" alt-text="Power BI Desktop へのサインインを示すスクリーンショット。":::
   
1. Power BI のリボンで **[データの取得]** を選択してから、 **[空のクエリ]** を選択します。 **[Power Query エディター]** が開きます。
   
   :::image type="content" source="media/powerbi/blank-query.png" alt-text="Power BI Desktop の [データの取得] の下の [空のクエリ] を選択したところを示すスクリーンショット。":::
   
1. **[Power Query エディター]** で **[詳細エディター]** を選択します。
   
1. エクスポートされた *PowerBIQuery.txt* ファイルをコピーした内容を **[詳細エディター]** ウィンドウに貼り付けて、 **[完了]** を選択します。
   
   :::image type="content" source="media/powerbi/advanced-editor.png" alt-text="Power BI の [詳細エディター] に貼り付けられた M クエリを示すスクリーンショット。":::
   
1. **[Power Query エディター]** で、クエリの名前を "*App_signin_stats*" に変更し、 **[閉じて適用]** を選択します。
   
   :::image type="content" source="media/powerbi/close-apply.png" alt-text="名前が変更されたクエリと [Power Query エディター] の [閉じて適用] コマンドを示すスクリーンショット。":::

## <a name="create-visualizations-from-the-data"></a>データから視覚化を作成する

データが Power BI に取得されたので、データに関する分析情報を提供する視覚化を作成できます。

### <a name="create-a-table-visual"></a>テーブルの視覚化を作成する

まず、クエリのすべての結果を示すテーブルを作成します。

1. テーブルの視覚化を Power BI Desktop キャンバスに追加するには、 **[視覚化]** の下にある **テーブル** アイコンを選択します。
   
   :::image type="content" source="media/powerbi/table.png" alt-text="Power BI Desktop の [視覚化] の下にあるテーブル アイコンを示すスクリーンショット。":::
   
1. **[フィールド]** で、クエリ内のすべてのフィールドを選択して、テーブルにすべてが表示されるようにします。 テーブルにすべてのデータが表示されない場合は、選択ハンドルをドラッグしてテーブルを拡大します。
   
   :::image type="content" source="media/powerbi/select-fields.png" alt-text="テーブルの視覚化に対してすべてのフィールドが選択されていることを示すスクリーンショット。":::
   
### <a name="create-a-pie-chart"></a>円グラフを作成する

次に、サインイン試行が最も失敗したアプリケーションを示す円グラフを作成します。

1. テーブルの視覚化の外側をクリックまたはタップしてテーブルの視覚化の選択を解除し、 **[視覚化]** の下の **円グラフ** アイコンを選択します。
   
   :::image type="content" source="media/powerbi/pie-chart.png" alt-text="Power BI Desktop の [視覚化] の下にある円グラフ アイコンを示すスクリーンショット。":::
   
1. **[凡例]** ウェルで **[AppDisplayName]** を選択するか、 **[フィールド]** ペインからドラッグします。 **[値]** ウェルで **[失敗]** を選択するか、 **[フィールド]** からドラッグします。 円グラフに、アプリケーションごとの失敗したサインイン試行の回数が表示されるようになります。
   
   :::image type="content" source="media/powerbi/failed.png" alt-text="アプリケーションごとの失敗したサインイン試行の回数に関する円グラフを示すスクリーンショット。":::
   
### <a name="create-a-new-quick-measure"></a>新しいクイック メジャーを作成する

アプリケーションごとの失敗したサインイン試行のパーセンテージを表示することもできます。 クエリにはパーセンテージ列が含まれていないので、この情報を表示する新しいメジャーを作成できます。

1. **[視覚化]** で、**積み上げ縦棒グラフ** アイコンを選択して積み上げ縦棒グラフを作成します。
   
   :::image type="content" source="media/powerbi/column-chart.png" alt-text="Power BI Desktop の [視覚化] の下にある積み上げ縦棒グラフ アイコンを示すスクリーンショット。":::
   
1. 新しい視覚化を選択して、リボンの **[クイック メジャー]** を選択します。
   
1. **[クイック メジャー]** ウィンドウの **[計算]** で、 **[除算]** を選択します。 **[フィールド]** から **[分子]** フィールドに **[失敗]** を ドラッグし、 **[フィールド]** から **[分母]** に **[試行回数]** をドラッグします。
   
   :::image type="content" source="media/powerbi/quick-measures.png" alt-text="[クイック メジャー] ウィンドウの設定を示すスクリーンショット。":::
   
1. **[OK]** を選択します。 新しいメジャーが **[フィールド]** ペインに表示されます。
   
1. **[フィールド]** ペインで新しいメジャーを選択し、リボンの **[書式設定]** で **[パーセンテージ]** を 選択します。
   
   :::image type="content" source="media/powerbi/percentage.png" alt-text="[フィールド] ペインで新しいメジャーが選択され、リボンの [書式設定] で [パーセンテージ] が選択されていることを示すスクリーンショット。":::
   
1. キャンバスで縦棒グラフの視覚化を選択して、 **[AppDisplayName]** フィールドを選択するか、 **[軸]** ウェルにドラッグし、新しい **[失敗を試行回数で除算]** メジャーを選択するか、 **[値]** ウェルにドラッグします。 グラフには、アプリケーションごとの失敗したサインイン試行のパーセンテージが表示されるようになります。
   
   :::image type="content" source="media/powerbi/failed-percentage.png" alt-text="アプリケーションごとの失敗した試行のパーセンテージに関する縦棒グラフを示すスクリーンショット。":::
   
### <a name="refresh-the-data-and-save-the-report"></a>データを最新の情報に更新してレポートを保存する

1. **[最新の情報に更新]** を選択して、Microsoft Azure Sentinel から最新のデータを取得します。
   
   :::image type="content" source="media/powerbi/refresh.png" alt-text="リボンの [最新の情報に更新] ボタンを示すスクリーンショット。":::
   
1. **[ファイル]**  >  **[保存]** を選択して、Power BI レポートを保存します。

## <a name="create-a-power-bi-online-workspace"></a>Power BI オンライン ワークスペースを作成する

レポートを共有するための Power BI ワークスペースを作成するには、次の手順を実行します。

1. Power BI Desktop と Microsoft Azure Sentinel の読み取りアクセスに使用するのと同じアカウントで [powerbi.com](https://powerbi.com) にサインインします。
   
1. **[ワークスペース]** で **[ワークスペースの作成]** を選択します。 ワークスペースに "*管理レポート*" という名前を付けて、 **[保存]** を選択します。
   
   :::image type="content" source="media/powerbi/create-workspace.png" alt-text="Power BI サービスの [ワークスペースの作成] を示すスクリーンショット。":::
   
1. ユーザーとグループにワークスペースへのアクセスを許可するには、新しいワークスペース名の横にある **その他のオプション** のドットを選択し、 **[ワークスペースへのアクセス]** を選択します。
   
   :::image type="content" source="media/powerbi/workspace-access.png" alt-text="ワークスペースのその他のオプション メニューの [ワークスペースへのアクセス] を示すスクリーンショット。":::
   
1. **[ワークスペースへのアクセス]** サイド ペインでは、ユーザーの電子メール アドレスを追加して、各ユーザーにロールを割り当てることができます。 ロールは、[管理者]、[メンバー]、[共同作成者]、[表示者] です。

## <a name="publish-the-power-bi-report"></a>Power BI レポートの公開

これで、Power BI Desktop を使用して Power BI レポートを公開し、他のユーザーが表示できるようにすることができるようになりました。

1. Power BI Desktop の新しいレポートで **[公開]** を選択します。
   
   :::image type="content" source="media/powerbi/publish.png" alt-text="Power BI Desktop のリボンの [公開] が示されているスクリーンショット。":::
   
1. 公開先の **[管理レポート]** ワークスペースを選択し、 **[選択]** を選択します。
   
   :::image type="content" source="media/powerbi/select-workspace.png" alt-text="公開先の Power BI の [管理レポート] ワークスペースが選択されていることを示すスクリーンショット。":::
   
## <a name="import-the-report-to-a-microsoft-teams-channel"></a>Microsoft Teams チャネルにレポートをインポートする

Management Teams チャネルのメンバーがレポートを表示できるようにすることもできます。 Teams チャネルにレポートを追加するには、次の手順を実行します。

1. Management Teams チャネルで、 **+** を選択してタブを追加し、 **[タブの追加]** ウィンドウで **[Power BI]** を見つけて選択します。

   :::image type="content" source="media/powerbi/add-tab.png" alt-text="Teams の [タブの追加] ウィンドウで [Power BI] が選択されていることを示すスクリーンショット。":::
   
1. Power BI レポートの一覧から新しいレポートを選択して、 **[保存]** を選択します。 レポートが、Teams チャネルの新しいタブに表示されます。
   
   :::image type="content" source="media/powerbi/teams.png" alt-text="Teams チャネルのタブに Power BI レポートが表示されているスクリーンショット。":::

## <a name="schedule-report-refresh"></a>レポート更新のスケジュールを設定する

スケジュールに基づいて Power BI レポートを最新の情報に更新すると、レポートに更新されたデータが常に表示されるようになります。

1. Power BI サービスで、レポートの公開先のワークスペースを選択します。
   
1. レポートのデータセットの横にある **その他のオプション** >  **[設定]** を選択します。
   
   :::image type="content" source="media/powerbi/settings.png" alt-text="Power BI レポートのデータセットのその他のオプションの [設定] を示すスクリーンショット。":::
   
1. **[資格情報の編集]** を選択して、Log Analytics ワークスペースへの読み取りアクセスを持つアカウントの資格情報を指定します。
   
1. **[スケジュールされた更新]** で、スライダーを **[オン]** に設定し、レポートの更新スケジュールを設定します。
   
   :::image type="content" source="media/powerbi/schedule.png" alt-text="Power BI レポートのデータセットの [スケジュールされた更新] の設定を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [Azure Monitor ログ データを Power BI にインポートする](../azure-monitor/logs/log-powerbi.md)
- [Power Query M 式言語](/powerquery-m/)
