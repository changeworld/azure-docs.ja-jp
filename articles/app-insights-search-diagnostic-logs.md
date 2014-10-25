<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights での診断ログの検索

System.Diagnostics.Trace、NLog、および Log4Net の診断データをキャプチャして検索できます。Application Insights には、アプリケーションの健全性監視機能を補足する効率的で使いやすいツールが用意されています。このツールを使用すると、1 つまたは複数のソースから記録されたイベントを収集してその内容を調べることができます。

監視対象の Web アプリケーションは内部設置環境または仮想マシンでホストされる Web アプリケーションであっても、Microsoft Azure Web サイトであってもかまいません。

1.  [ログ アダプターの追加][ログ アダプターの追加]
-   [診断コレクションの構成][診断コレクションの構成]
-   [ログ ステートメントの挿入、ビルド、デプロイ][ログ ステートメントの挿入、ビルド、デプロイ]
-   [ログ データの表示][ログ データの表示]
-   [データの検索][データの検索]
-   [次のステップ][次のステップ]

## <a name="add"></a>1. ログ アダプターの追加

1.  この操作をまだ行っていない場合は、Visual Studio で [Application Insights を Web サービス プロジェクトに追加][Application Insights を Web サービス プロジェクトに追加]してください。

    ログ機能をプロジェクトに追加した後で Application Insights を追加した場合は、ログ アダプターが既に設定および構成されています。[プロジェクトを再デプロイ][ログ ステートメントの挿入、ビルド、デプロイ]するだけで[データを表示][ログ データの表示]できます。

2.  ソリューション エクスプローラーで、プロジェクトのコンテキスト メニューの **[NuGet パッケージの管理]** を選択します。
3.  [オンライン]、[すべて]、**[プレリリースを含める]** の順に選択し、"Microsoft.ApplicationInsights" を検索します。

    ![Get the prerelease version of the appropriate adapter][Get the prerelease version of the appropriate adapter]

4.  次のいずれかの適切なパッケージのプレリリース バージョンを選択します。
  + Microsoft.ApplicationInsights.TraceListener
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. 診断コレクションの構成

### System.Diagnostics.Trace の場合

Web.config を開き、次のコードを `<configuration>` セクションに挿入します。

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### NLog の場合

Nlog.config を開き、次のスニペットを `<extensions>`、`<targets>`、`<rules>` の各セクションにマージします。必要に応じてこれらのセクションを作成します。

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Log4Net の場合

Web.config を開き、次のスニペットを `<configsections>` セクションと `<log4net>` セクションにマージします。

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. ログ ステートメントの挿入、ビルド、デプロイ

選択したログ フレームワークを使用してイベント ログ呼び出しを挿入します。たとえば、Log4Net を使用する場合、次のような呼び出しを使用します。

    log.Warn("Slow response - database01");

開発時および運用時にログ記録されたイベントが Application Insights に送信されます。

## <a name="view"></a>4. ログ データの表示

Application Insights で、診断検索画面を開きます。

![Open diagnostic search][Open diagnostic search]

目的のログ イベントを選択して詳細を表示します。

![Open diagnostic search][1]

使用できるフィールドは、ログ フレームワークと、呼び出しで使用したパラメーターに依存します。

(ワイルドカードを含まない) プレーン文字列を使用して、項目内のフィールド データをフィルター処理できます。

## <a name="search"></a>5. データの検索

時間範囲を設定し、語句を検索します。範囲を狭くすると、検索に要する時間が短くなります。

![Open diagnostic search][2]

部分文字列ではなく語句を検索することに注意してください。語句とは、"."、"\_" などのいくつかの句読文字を含む、英数字の文字列です。次に例を示します。
<table>
<tr><th>語句</th><th>一致しない</th><th>一致する</th></tr>
<tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
<tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
<tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

使用できる検索表現を次に示します。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>サンプル クエリ</p></th>
<th align="left"><p>効果</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">slow</span></p></td>
<td align="left"><p>日付範囲内でフィールドに &quot;slow&quot; という語句が含まれるすべてのイベントを検索します</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">database??</span></p></td>
<td align="left"><p>database01、databaseAB などに一致します</p>
<p>? は、検索語句の先頭に使用できません</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">database*</span></p></td>
<td align="left"><p>database、database01、databaseNNNN に一致します</p>
<p>* は、検索語句の先頭に使用できません</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple AND banana</span></p></td>
<td align="left"><p>両方の語句を含むイベントを検索します。&quot;and&quot; ではなく大文字の &quot;AND&quot; を使用します。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">apple OR banana</span></p>
<p><span class="code">apple banana</span></p></td>
<td align="left"><p>どちらかの語句を含むイベントを検索します。&quot;or&quot; ではなく &quot;OR&quot; を使用します。</p>
<p>短縮形。</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple NOT banana</span></p>
<p><span class="code">apple -banana</span></p></td>
<td align="left"><p>ある用語を含む一方で他方の用語を含まないイベントを検索します。</p>
<p>短縮形。</p></td>
</tr>
<tr class="odd">
<td align="left"><p>app* AND banana NOT (grape OR pear)</p>
<p><span class="code">app* AND banana -(grape pear)</span></p></td>
<td align="left"><p>論理演算子とかっこの使用。</p>
<p>短縮形。</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>指定されたフィールドに一致します。既定では、すべてのフィールドが検索されます。使用できるフィールドを調べるには、イベントを選択して詳細を表示します。</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>次のステップ

-   [プロジェクトへの Application Insights の追加][Application Insights を Web サービス プロジェクトに追加]
-   [可用性と応答性のテストの設定][可用性と応答性のテストの設定]
-   [トラブルシューティング][トラブルシューティング]

## 詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][Application Insights を Web サービス プロジェクトに追加]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サーバーの監視を今すぐ始める]
-   [メトリックの探索][メトリックの探索]
-   [Application Insights での診断ログの検索][Application Insights での診断ログの検索]
-   [Web アプリケーションの可用性と応答性の監視][可用性と応答性のテストの設定]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティング]

<!--Link references-->

  [ログ アダプターの追加]: #add
  [診断コレクションの構成]: #configure
  [ログ ステートメントの挿入、ビルド、デプロイ]: #deploy
  [ログ データの表示]: #view
  [データの検索]: #search
  [次のステップ]: #next
  [Application Insights を Web サービス プロジェクトに追加]: ../app-insights-monitor-application-health-usage/
  [Get the prerelease version of the appropriate adapter]: ./media/appinsights/appinsights-36nuget.png
  [Open diagnostic search]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [可用性と応答性のテストの設定]: ../app-insights-monitor-web-app-availability/
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Application Insights - 概要]: ../app-insights-get-started/
  [ライブ Web サーバーの監視を今すぐ始める]: ../app-insights-monitor-performance-live-website-now/
  [メトリックの探索]: ../app-insights-explore-metrics/
  [Application Insights での診断ログの検索]: ../app-insights-search-diagnostic-logs/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
