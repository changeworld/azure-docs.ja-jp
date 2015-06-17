<properties 
	pageTitle="診断ログを検索する" 
	description="Trace、NLog、または Log4Net で生成されたログを検索します。" 
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2015" 
	ms.author="awills"/>
 
# Application Insights 内での診断検索

従来の一般的なデバッグ方法の 1 つに、トレース ログを出力するコードの行を挿入する方法があります。[Application Insights][start] では、Web サーバーのログをキャプチャできます。ログの検索とフィルターに役立ちます。既に log4Net、NLog、または System.Diagnostics.Trace を使用している場合は、アダプターでこれらのログをキャプチャできます。または、Application Insights SDK に組み込まれている TrackTrace メソッドと TrackException メソッドを使用することができます。

検索結果には、[利用状況][usage]と[パフォーマンス][perf]のレポートの作成に使用される通常のページ ビューや要求イベントが、記述したすべての[カスタム TrackEvent 呼び出し][track]と共に含まれます。


2. [ログ フレームワーク用に、アダプターをインストールするかどうか](#capture)
+ [診断ログの呼び出しを挿入する](#pepper)
+ [例外](#exceptions)
+ [ログ データの表示](#view)
+ [ログ データを検索する](#search)
+ [トラブルシューティング](#questions)
+ [次のステップ](#next)



## <a name="capture"></a> ログ フレームワーク用に、アダプターをインストールするかどうか

プロジェクトに [Application Insights][start] をまだインストールしていない場合は、今すぐインストールします。

組み込みの Application Insights SDK Track*() 呼び出しを使用する場合、アダプターは必要ありません。[次のセクションに進みます。](#pepper)

log4Net、NLog、または System.Diagnostics.Trace を使用して生成されたログを検索するには、以下のように適切なアダプターをインストールします。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。 
2. ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理] を選択します。
3. [オンライン]、[すべて]、**[プレリリースを含める]** の順に選択し、"Microsoft.ApplicationInsights" を検索します。

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. 次のいずれかの適切なパッケージを選択します。
  + Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace 呼び出しをキャプチャするため)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet パッケージは、必要なアセンブリをインストールし、web.config または app.config も変更します。

## <a name="pepper"></a>3 診断ログの呼び出しを挿入する

選択したログ フレームワークを使用してイベント ログ呼び出しを挿入します。 

たとえば、Application Insights SDK を使用する場合は、次を挿入できます。

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

または、System.Diagnostics.Trace を使用する場合

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net、または NLog を使用する場合

    logger.Warn("Slow response - database01");

アプリケーションをデバッグ モードで実行または Web サーバーにデプロイします。

### <a name="exceptions"></a>例外

次のように、例外をログに送信します。

クライアント側の JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

サーバー側の C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

サーバー側の VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

プロパティと測定値のパラメーターは省略可能ですが、フィルター処理と、特別な情報を追加するのに便利です。たとえば、複数のゲームを実行できるアプリケーションを使用している場合、1 つのゲームに関連する例外レポートをすべて検索できます。必要な数だけ項目を各辞書に追加できます。

## <a name="view"></a>4. ログ データを表示する


1. Application Insights で、診断検索画面を開きます。

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-30openDiagnostics.png)
   
2. 確認するイベントの種類に対してフィルターを設定します。

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-331filterTrace.png)


イベントの種類は、次のとおりです。

* **トレース** - Web サーバーからキャプチャした診断ログを検索します。log4Net、NLog、System.Diagnostic.Trace、ApplicationInsights TrackTrace を呼び出しが含まれます。
* **要求** - ページの要求、データの要求、画像など Web アプリケーションのサーバー コンポーネントによって受信された HTTP 要求を検索します。表示されるイベントは、Application Insights サーバー SDK から送信された利用統計情報で、要求数のレポートを作成するために使用されます。
* **ページ ビュー** - ページ ビュー イベントを検索します。これらのイベントは、Web クライアントによって送信され、ページ ビュー レポートの作成に使用されます (ここで何も表示されない場合は、[Web クライアントの監視][usage]を設定します)。
* **カスタム イベント** - [利用状況の監視][track]のために、TrackEvent() および TrackMetric() への呼び出しを挿入した場合は、ここで検索できます。

目的のログ イベントを選択して詳細を表示します。 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-32detail.png)

(ワイルドカードを含まない) プレーン文字列を使用して、項目内のフィールド データをフィルター処理できます。

使用できるフィールドは、ログ フレームワークと、呼び出しで使用したパラメーターに依存します。


## <a name="search"></a>5. データを検索する

時間範囲を設定し、語句を検索します。範囲を狭くすると、検索時間が短くなります。 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-311search.png)

部分文字列ではなく語句を検索することに注意してください。語句とは、"."、"_" などのいくつかの句読文字を含む、英数字の文字列です。次に例を示します。

<table>
  <tr><th>語句</th><th>一致しない指定</th><th>一致する指定</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

使用できる検索表現を次に示します。

<table>
                    <tr>
                      <th>
                        <p>サンプル クエリ</p>
                      </th>
                      <th>
                        <p>効果</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">slow</span>
                        </p>
                      </td>
                      <td>
                        <p>日付範囲内でフィールドに "slow" という語句が含まれるすべてのイベントを検索します</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>database01、databaseAB などに一致します</p>
                        <p>? は、検索語句の先頭に使用できません</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>database、database01、databaseNNNN に一致します</p>
                        <p>* は、検索語句の先頭に使用できません</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">apple AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>両方の語句を含むイベントを検索します。"and" ではなく大文字の "AND" を使用します。</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple OR banana</span>
                        </p>
                        <p>
                          <span class="code">apple banana</span>
                        </p>
                      </td>
                      <td>
                        <p>どちらかの語句を含むイベントを検索します。"or" ではなく "OR" を使用します。</p>
                        <p>短縮形。</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple NOT banana</span>
                        </p>
                        <p>
                          <span class="code">apple -banana</span>
                        </p>
                      </td>
                      <td>
                        <p>ある用語を含む一方で他方の用語を含まないイベントを検索します。</p>
                        <p>短縮形。</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>app* AND banana NOT (grape OR pear)</p>
                        <p>
                          <span class="code">app* AND banana -(grape pear)</span>
                        </p>
                      </td>
                      <td>
                        <p>論理演算子とかっこの使用。</p>
                        <p>短縮形。</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>指定されたフィールドに一致します。既定では、すべてのフィールドが検索されます。使用できるフィールドを調べるには、イベントを選択して詳細を表示します。</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Q & A

### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました

Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプローラーで、`ApplicationInsights.config`を右クリックし、**[Application Insights の更新]** を選択します。Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。これで問題は修正されます。

### <a name="limits"></a>保持されるデータの量はどのくらいですか

各アプリケーションで、1 秒あたり 500 イベントまでです。イベントは 7 日間保持されます。

### <a name="cani"></a>以下のことは可能でしょうか。

- イベントと例外にアラートを設定する
- さらに詳しい分析ログをエクスポートする
- 特定のプロパティで検索する

現在はできませんが、これらのすべての機能には今後着手します。

## <a name="add"></a>次のステップ

* [可用性と応答性のテストの設定][availability]
* [トラブルシューティング][qna]





[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
 
