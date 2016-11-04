---
title: Application Insights から Power BI へのエクスポート
description: 記事
services: application-insights
documentationcenter: ''
author: noamben
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/05/2016
ms.author: awills

---
# Application Insights のデータを Power BI に入力する
[Power BI](http://www.powerbi.com/) は、データを分析し、洞察を共有する一連のビジネス分析ツールです。あらゆるデバイスで機能豊富なダッシュボードを利用できます。[Visual Studio Application Insights](app-insights-overview.md) など、さまざまなソースのデータを組み合わせることができます。

作業を開始するには、[Power BI での Application Insights のデータの表示](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)に関するページをご覧ください。

最初のダッシュボードを入手したら、Application Insights のグラフを他のソースのグラフと組み合わせてカスタマイズできます。さらに多くのグラフを入手できるビジュアル ギャラリーがあり、各グラフにはユーザーが設定できるパラメーターがあります。

![](./media/app-insights-export-power-bi/010.png)

最初のインポート後は、ダッシュボードとレポートが毎日更新されます。データセットの更新スケジュールを管理できます。

**サンプリング。** アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。これは、SDK または取り込みでサンプリングを手動で設定した場合にも当てはまります。[サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## Application Insights のデータを表示する別の方法
* Azure 以外のデータを表示する必要がない場合は、[Application Insights のグラフが含まれた Azure ダッシュボード](app-insights-dashboards.md)をより適切に設定することができます。たとえば、いくつかの Azure サービス モニターと共に、システムのさまざまなコンポーネントを監視する Application Insights のグラフのダッシュボードを設定する場合、Azure ダッシュボードが最適です。Azure ダッシュボードは既定でより頻繁に更新されます。 
* [連続エクスポート](app-insights-export-telemetry.md)では、受信データが Azure Storage にコピーされます。コピーされたデータは、Azure Storage から移動して自由に処理できます。
* [Analytics](app-insights-analytics.md) を使用すると、Application Insights で保持されている生データに対して複雑なクエリを実行できます。

## Stream Analytics を使用した独自の Power BI アダプターの作成
Application Insights 用 Power BI コンテンツ パックでは、ユーザーのニーズを満たすと考えられるアプリケーションのテレメトリの便利なサブセットが表示されます。ただし、提供されるものよりもさらに広範なテレメトリを取得する場合や、生のテレメトリの一部のデータを計算する場合は、Azure Stream Analytics サービスを使用して独自のアダプターを作成できます。

ここでは、Application Insights から Azure Storage にデータをエクスポートします。[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) はそこからデータをプルし、一部のフィールドの名前を変更して処理した後、Power BI にパイプ処理します。Stream Analytics は、データの継続的なストリームに対して、フィルター、集計、計算を実行できるサービスです。

![SA を介した PBI へのエクスポートのブロック図](./media/app-insights-export-power-bi/020.png)

> [!TIP]
> Power BI で Application Insights のデータを表示するために、(Stream Analytics を使用する) **この記事の以降の手順に従う必要はありません**。さらに簡単な方法があります (代わりに、[無料のアダプターを使用](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)します)。無料のアダプターで必要なデータがすべて提供されるわけではない場合や、データに対して独自の集計や関数を定義する場合にのみ、この記事の以降の手順に従ってください。
> 
> 

### Azure でのストレージの作成
連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. [Power BI 用 Application Insights コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)を使ってみましたか。 このコンテンツ パックがニーズを満たしている場合は、この記事の以降の手順は不要です。
2. [Azure ポータル](https://portal.azure.com)で、サブスクリプションの "クラシック" ストレージ アカウントを作成します。
   
   ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-export-power-bi/030.png)
3. コンテナーを作成する
   
    ![新しいストレージで、[コンテナー] を選択し、[コンテナー] タイルをクリックし、[追加] を選択します](./media/app-insights-export-power-bi/040.png)
4. ストレージ アクセス キーのコピー
   
    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。
   
    ![ストレージで、[設定]、[キー] の順に開き、プライマリ アクセス キーのコピーを取ります](./media/app-insights-export-power-bi/045.png)

### Azure Storage への連続エクスポートの開始
[連続エクスポート](app-insights-export-telemetry.md)は、Application Insights から Azure のストレージにデータを移動します。

1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。
   
    ![[参照]、[Application Insights]、アプリケーションの順に選択します](./media/app-insights-export-power-bi/050.png)
2. 連続エクスポートを作成します。
   
    ![[設定]、[連続エクスポート]、[追加] の順に選択します](./media/app-insights-export-power-bi/060.png)

    以前に作成したストレージ アカウントを選択します。

    ![エクスポート先の設定](./media/app-insights-export-power-bi/070.png)

    表示するイベントの種類を設定します。

    ![イベントの種類の選択](./media/app-insights-export-power-bi/080.png)

1. データを蓄積します。しばらく待機し、ユーザーにアプリケーションを使用してもらいます。テレメトリが開始し、統計グラフが[メトリックス エクスプローラー](app-insights-metrics-explorer.md)に表示され、個々のイベントが[診断検索](app-insights-diagnostic-search.md)に表示されます。
   
    また、データはストレージにもエクスポートされます。
2. エクスポートされたデータを検査します。Visual Studio で、**[表示]、[Cloud Explorer]** の順に選択します。[Azure]、[Storage] の順に開きます (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります。[新しいプロジェクト] ダイアログを開き、[Visual c#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。
   
    ![](./media/app-insights-export-power-bi/04-data.png)
   
    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。

イベントが JSON 形式で BLOB ファイルに書き込まれます。各ファイルに 1 つ以上のイベントが含まれる場合があります。このため、イベント データを読み取って必要なフィールドをフィルター処理します。データの処理に関して実行できることは多数ありますが、今日の計画は、Stream Analytics を使用してデータを Power BI に移動することです。

### Azure Stream Analytics インスタンスの作成
[従来の Azure ポータル](https://manage.windowsazure.com/)で、Azure Stream Analytics サービスを選択し、新しい Stream Analytics ジョブを作成します。

![](./media/app-insights-export-power-bi/090.png)

![](./media/app-insights-export-power-bi/100.png)

新しいジョブが作成された後、その詳細を展開します。

![](./media/app-insights-export-power-bi/110.png)

#### BLOB の場所の設定
連続エクスポート BLOB から入力を取るよう設定します。

![](./media/app-insights-export-power-bi/120.png)

ここで、ストレージ アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。ストレージ アカウント キーとしてこれを設定します。

![](./media/app-insights-export-power-bi/130.png)

#### パスのプレフィックス パターンの設定
![](./media/app-insights-export-power-bi/140.png)

**日付の書式は YYYY-MM-DD (ダッシュ付き) に設定してください。**

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する場所を指定します。連続エクスポートによるデータ格納方法と一致するように設定する必要があります。次のように設定します。

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

次の点に注意してください。

* `webapplication27` は Application Insights リソースの名前です。**すべて小文字で指定します。**
* `1234...` は Application Insights リソースのインストルメンテーション キーです。**ダッシュは省略します。** 
* `PageViews` は分析するデータの種類です。使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。エクスポートされたデータを調べて、その他の使用可能な種類を確認します。「[Application Insights エクスポート データ モデル](app-insights-export-data-model.md)」を参照してください。
* `/{date}/{time}` はそのまま書き込まれるパターンです。

> [!NOTE]
> ストレージを検査して、正しいパスを取得されることを確認してください。
> 
> 

#### 初期セットアップの完了
シリアル化の書式を確認します。

![[確認]をクリックしてウィザードをして閉じます](./media/app-insights-export-power-bi/150.png)

ウィザードを閉じ、セットアップが完了するまで待機します。

> [!TIP]
> サンプルのコマンドを使用し、データをダウンロードします。クエリをデバッグするために、それをテスト サンプルとして保存します。
> 
> 

### 出力の設定
では、ジョブを選択し、出力を設定しましょう。

![新しいチャネルを選択して、[出力]、[追加]、[Power BI] をクリックします。](./media/app-insights-export-power-bi/160.png)

**作業または学校のアカウント**を指定し、Power BI リソースにアクセスする許可を Stream Analytics に与えます。次に、出力に名前を付け、ターゲット Power BI データセットと表に名前を付けます。

![3 つの名前を作成します。](./media/app-insights-export-power-bi/170.png)

### クエリの設定
クエリでは、入力から出力への変換を制御します。

![ジョブを選択し、[クエリ] をクリックします。以下のサンプルを貼り付けます。](./media/app-insights-export-power-bi/180.png)

テスト機能を使用し、出力が正しいことを確認します。入力ページから取得したサンプル データを指定します。

#### イベントの数を表示するためのクエリ
このクエリを貼り付けます。

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input は、ストリーム入力に付けたエイリアスです。
* pbi-output は、定義した出力エイリアスです。
* イベントの名前は JSON 配列にネストされているため、[OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) を使用します。Select でイベント名と、期間内にその名前を持つインスタンスの個数を取得します。[Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 句では、1 分という期間内に要素をグループ化します。

#### メトリックの値を表示するためのクエリ
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* このクエリはメトリックス テレメトリをドリルダウンし、イベント時刻とメトリック値を取得します。メトリック値は配列内に置かれます。そのため、OUTER APPLY GetElements パターンを使用し、行を抽出します。「myMetric」がこのケースのメトリックの名前になります。 

#### ディメンション プロパティの値を追加するクエリ
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* このクエリは、次元配列の固定インデックスにある特定のディメンションに依存せず、ディメンション プロパティの値を追加します。

### ジョブを実行する
ジョブを開始する過去の日付を選択できます。

![ジョブを選択し、[クエリ] をクリックします。以下のサンプルを貼り付けます。](./media/app-insights-export-power-bi/190.png)

ジョブが実行されるまで待ちます。

### Power BI で結果を確認します。
職場または学校のアカウントで Power BI を開き、Stream Analytics ジョブの出力として定義したデータセットとテーブルを選択します。

![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-power-bi/200.png)

このデータセットを、[Power BI](https://powerbi.microsoft.com) のレポートやダッシュボードで使用できるようになりました。

![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-power-bi/210.png)

### データが表示されない場合
* [日付の書式が YYYY-MM-DD (ダッシュ付き) に正しく設定されている](#set-path-prefix-pattern)ことを確認してください。

### ビデオ
Noam Ben Zeev で、Power BI にエクスポートする方法を確認できます。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## 関連項目
* [連続エクスポート](app-insights-export-telemetry.md)
* [データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)

<!---HONumber=AcomDC_0420_2016-->