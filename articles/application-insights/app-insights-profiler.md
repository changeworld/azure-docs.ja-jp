---
title: "Application Insights Profiler を使用して Azure でライブ Web アプリをプロファイルする | Microsoft Docs"
description: "フットプリントの小さいプロファイラーを使用して Web サーバー コードのホット パスを特定できます。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights を使用してライブ Azure Web アプリをプロファイルする

"*Application Insights のこの機能は Azure App Service で一般公開されており、Azure コンピューティング リソースではプレビュー段階です。*"

[Application Insights Profiler](app-insights-overview.md) を使用して、ライブ Web アプリの各メソッドにどれくらい時間がかかっているかを確認できます。 Application Insights プロファイル ツールにより、アプリで処理された要求の詳細なプロファイルがライブ表示され、最も多くの時間がかかっている "*ホット パス*" が強調表示されます。 プロファイラーにより、さまざまな応答時間を含むサンプルが自動的に選択され、さまざまな手法によりオーバーヘッドが最小化されます。

このプロファイラーは、現在、Azure App Service で Basic サービス階層以上で実行されている ASP.NET Web アプリに対して使用できます。

## <a id="installation"></a>プロファイラーを有効にする

コードに [Application Insights をインストール](app-insights-asp-net.md)します。 既にインストールされている場合は、バージョンが最新であることを確認します  最新バージョンであることを確認するには、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** > **[更新]** > **[すべてのパッケージを更新する]** を選択します。 次に、アプリを再デプロイします。

*ASP.NET Core を使用している場合は、[こちら](#aspnetcore)から詳細をご確認ください。*

[Azure Portal](https://portal.azure.com) で、Web アプリの Application Insights リソースを開きます。 **[パフォーマンス]** > **[Application Insights Profiler を有効にする]** を選択します。

![プロファイラーの有効化バナーを選択する][enable-profiler-banner]

また、**[構成]** を選択して状態を表示し、プロファイラーを有効または無効にすることもできます。

![[パフォーマンス] の [構成] を選択する][performance-blade]

Application Insights で構成されている Web アプリの一覧が **[構成]** の下に表示されます。 必要に応じて、手順に従ってプロファイラー エージェントをインストールします。 Application Insights で Web アプリが構成されていない場合は、**[リンクされたアプリの追加]** を選択します。

リンクされたすべての Web アプリでプロファイラーを制御するには、**[構成]** ウィンドウで **[プロファイラーの有効化]** または **[プロファイラーの無効化]** を選択します。

![[構成] ウィンドウのオプション][linked app services]

App Service プランでホストされる Web アプリとは異なり、Azure コンピューティング リソースでホストされるアプリケーション (Azure Virtual Machines、仮想マシン スケール セット、Azure Service Fabric、Azure Cloud Services など) は、Azure で直接管理されません。 この場合、リンク先 Web アプリはありません。 アプリにリンクするのではなく、**[Profiler の有効化]** を選択します。

## <a name="disable-the-profiler"></a>Profiler を無効にする
個々の App Service インスタンスのプロファイラーを停止または再起動するには、**[Web ジョブ]** で App Service リソースに移動します。 プロファイラーを削除するには **[拡張機能]** に移動します。

![Web ジョブのプロファイラーを無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべての Web アプリでプロファイラーを有効にすることをお勧めします。

Web アプリに対する変更を WebDeploy を使用してデプロイする場合は、デプロイ中の削除対象から App_Data フォルダーを除外してください。 そうしないと、次に Web アプリを Azure にデプロイするときに、プロファイラーの拡張機能のファイルが削除されます。

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Azure VM およびコンピューティング リソースでのプロファイラーの使用 (プレビュー)

[実行時の Azure App Service に対して Application Insights を有効](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights)にすると、Application Insights Profiler が自動的に使用できるようになります。 リソースに対して既に Application Insights を有効にしている場合は、構成ウィザードを使用した最新バージョンへの更新が必要になる可能性があります。

[Azure コンピューティング リソース用のプロファイラーのプレビュー バージョン](https://go.microsoft.com/fwlink/?linkid=848155)に関する情報をご確認ください。


## <a name="limitations"></a>制限事項

既定のデータ リテンション期間は 5 日です。 また、1 日あたり最大 10 GB のデータを取り込むことができます。

プロファイラー サービスの使用料金は発生しません。 プロファイラー サービスを使用するには、Web アプリは、少なくとも App Service の Basic レベルでホストする必要があります。

## <a name="overhead-and-sampling-algorithm"></a>オーバーヘッドとサンプリング アルゴリズム

プロファイラーは、プロファイラーのトレース キャプチャが有効なアプリをホストする各仮想マシンで、1 時間ごとに 2 分間ランダムに実行されます。 プロファイラーの実行中は、サーバーに 5 ～ 15% の CPU オーバーヘッドが加わります。
アプリをホスト可能なサーバーの数が多くなるほど、プロファイラーがアプリのパフォーマンス全体に与える影響は低下します。 これは、サンプリング アルゴリズムにより、プロファイラーが常にサーバーの実行時間の 5% しか実行されないためです。 Web 要求の処理に利用できるサーバーが増えると、プロファイラーを実行することにより発生するサーバー オーバーヘッドは低下します。


## <a name="view-profiler-data"></a>プロファイラー データを表示する

**[パフォーマンス]** ウィンドウに移動し、操作の一覧が表示されるまで下へスクロールします。

![Application Insights の [パフォーマンス] ウィンドウの例: 列][performance-blade-examples]

操作テーブルには次の列が表示されます。

* **カウント**: **[カウント]** ウィンドウの時間の範囲における要求の数。
* **中央値**: アプリが要求に応答するのに要する標準的な時間。 全応答の半数が、この値より短い時間で行われました。
* **95 パーセンタイル**: 応答の 95% が、この値より短い時間で行われました。 この値と中央値との差が非常に大きい場合は、アプリに断続的に問題が発生している可能性があります  (または、キャッシュなどの設計特性が原因である可能性があります)。
* **PROFILER TRACES\(Profiler トレース\)**: アイコンは、プロファイラーがこの操作のスタック トレースをキャプチャしたことを示します。

**[表示]** を選択すると、トレース エクスプローラーが開きます。 このエクスプローラーには、プロファイラーがキャプチャしたいくつかのサンプルが応答時間ごとに分類されて表示されます。

**[Preview Performance]\(パフォーマンスのプレビュー\)** ウィンドウを使用している場合は、プロファイラーのトレースを表示するために、ページの **[Take Actions]\(アクションの実行\)** セクションに移動します。 **[Profiler トレース]** を選択します。

![Application Insights の [パフォーマンス] ウィンドウの Profiler トレースのプレビュー][performance-blade-v2-examples]

いずれかのサンプルを選択すると、要求の実行に費やした時間の内訳がコード レベルで表示されます。

![Application Insights トレース エクスプローラー][trace-explorer]

トレース エクスプローラーには次の情報が表示されます。

* **ホット パスの表示**: 最大リーフ ノード、またはそれに近いノードが表示されます。 ほとんどの場合、このようなノードはパフォーマンスのボトルネックのすぐ近くにあります。
* **ラベル**: 関数またはイベントの名前。 このツリーには、コードと発生したイベント (SQL イベント、HTTP イベントなど) が混在して表示されます。 最上位のイベントは要求時間全体に相当します。
* **経過時間**: 操作の開始から終了までの期間。
* **When\(実行期間\)**: 関数またはイベントが他の関数に関連して実行された時間。

## <a name="how-to-read-performance-data"></a>パフォーマンス データの解釈方法

Microsoft サービス プロファイラーでは、アプリのパフォーマンス分析に、サンプリング メソッドとインストルメンテーションを組み合わせて使用します。 詳細情報の収集中、サービス プロファイラーは、各コンピューターの CPU の命令ポインターをミリ秒単位サンプリングします。 各サンプルでは、現在実行中のスレッドの呼び出し履歴全体がキャプチャされます。 これにより、そのスレッドが実行した内容に関する有用な詳細情報が、高い抽象化レベルと低い抽象化レベルの両方で取得されます。 また、サービス プロファイラーでは、アクティビティの相関関係と因果関係を追跡するために、コンテキスト切り替えイベント、タスク並列ライブラリ (TPL) イベント、スレッドプール イベントなど、他のイベントも収集されます。

タイムライン ビューに表示される呼び出し履歴は、サンプリングとインストルメンテーションの結果です。 スレッドの呼び出し履歴全体がキャプチャされるため、各サンプルには、Microsoft .NET Framework と、参照している他のフレームワークのコードが含まれます。

### <a id="jitnewobj"></a>オブジェクトの割り当て (clr!JIT\_New または clr!JIT\_Newarr1)
**clr!JIT\_New** と **clr!JIT\_Newarr1** は、マネージ ヒープからメモリを割り当てる .NET Framework のヘルパー関数です。 **clr!JIT\_New** は、オブジェクトが割り当てられるときに呼び出されます。 **clr!JIT\_Newarr1** は、オブジェクト配列が割り当てられるときに呼び出されます。 これら 2 つの関数は、通常は非常に高速で、比較的短時間で完了します。 タイムライン上で **clr!JIT\_New** または **clr!JIT\_Newarr1** に長い時間がかかっている場合は、コードが多くのオブジェクトを割り当て、大量のメモリを消費している可能性があることを示します。

### <a id="theprestub"></a>コードの読み込み (clr!ThePreStub)
**clr!ThePreStub** は、初めて実行するコードを準備する .NET Framework のヘルパー関数です。 これには一般的に、Just-In-Time (JIT) コンパイルなどが含まれます (ただし、これに限定されません)。 C# の各メソッドでは、プロセスの有効期間中に **clr!ThePreStub** が最大 1 回呼び出されます。

要求に対して **clr!ThePreStub** がかなりの時間を要している場合は、その要求が、このメソッドを実行する最初の要求であることを示します。 .NET Framework ランタイムがそのメソッドを読み込むには、かなりの時間がかかります。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用するか、アセンブリで Native Image Generator (ngen.exe) を実行することを検討してください。

### <a id="lockcontention"></a>ロックの競合 (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** または **clr!JITutil\_MonEnterWorker** は、現在のスレッドが、ロックが解放されるのを待っていることを示します。 これは、通常、C# の **LOCK** ステートメントを実行するとき、**Monitor.Enter** メソッドを呼び出すとき、または **MethodImplOptions.Synchronized** 属性を持つメソッドを呼び出すときに表示されます。 ロックの競合は一般的に、スレッド A がロックを取得し、それを解放する前にスレッド B が同じロックを取得しようとすると発生します。

### <a id="ngencold"></a>コードの読み込み ([COLD])
**mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined** のように、メソッド名に **[COLD]** が含まれている場合は、.NET Framework ランタイムが、<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ガイド付き最適化のプロファイル</a>によって最適化されていないコードを初めて実行していることを示します。 これが表示されるのは、プロセスの有効期間中、メソッドごとに最大 1 回です。

要求に対してコードの読み込みがかなりの時間を要している場合は、要求がこのメソッドの最適化されていない部分を実行する最初の要求であることを示します。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用することを検討してください。

### <a id="httpclientsend"></a>HTTP 要求の送信
**HttpClient.Send** などのメソッドは、コードが HTTP 要求の完了を待機していることを示します。

### <a id="sqlcommand"></a>データベース操作
**SqlCommand.Execute** などのメソッドは、データベース操作が終了するのをコードが待機していることを示します。

### <a id="await"></a>待機 (AWAIT\_TIME)
**AWAIT\_TIME** は、別のタスクが終了するのをコードが待機していることを示します。 これは通常、C# の **AWAIT** ステートメントで発生します。 コードで C# の **AWAIT** が実行されると、スレッドはアンワインドして、スレッド プールの制御を戻します。**AWAIT** が終了するのを待機している間にスレッドがブロックされることはありません。 ただし、論理的に見れば、**AWAIT** を実行したスレッドは、操作の終了を待機している間 "ブロック" されます。 **AWAIT\_TIME** ステートメントは、タスクが終了するのを待機しているブロック時間を示します。

### <a id="block"></a>ブロック時間
**BLOCKED_TIME** は、別のリソースが使用可能になるのをコードが待機していることを示します。 たとえば、同期オブジェクトやスレッドが使用可能になるのを待っているか、要求が終了するのを待機している可能性があります。

### <a id="cpu"></a>CPU 時間
命令の実行中のため、CPU がビジー状態です。

### <a id="disk"></a>ディスク時間
アプリケーションがディスク操作を実行しています。

### <a id="network"></a>ネットワーク時間
アプリケーションがネットワーク操作を実行しています。

### <a id="when"></a>[When (実行期間)] 列
**[When]\(実行期間\)** 列は、ノードについて収集された包括的なサンプルが時間の経過と共にどのように変化するかを視覚化したものです。 要求の範囲全体は 32 個のタイム バケットに分割されます。 そのノードの包括的なサンプルは、その 32 個のバケットに蓄積されます。 各バケットはバーとして表されます。 バーの長さは、調整された値を表します。 ノードに **CPU_TIME** または **BLOCKED_TIME** と表示されている場合、リソース (CPU、ディスク、スレッド) の消費という明確な関係があるため、バーはそのバケットの期間にリソースのいずれかを消費していることを表しています。 こうしたメトリックでは、複数のリソースを消費することによって値が 100% を超える可能性があります。 たとえば、特定の期間に平均して 2 つの CPU を使用する場合、CPU 使用率は 200% になります。


## <a id="troubleshooting"></a>トラブルシューティング

### <a name="too-many-active-profiling-sessions"></a>アクティブなプロファイリング セッションが多すぎる

現在、同じサービス プランで実行されている最大 4 つの Azure Web アプリとデプロイ スロットで、プロファイラーを有効にできます。 プロファイラー Web ジョブによってアクティブなプロファイル セッションが多すぎる旨が報告された場合は、一部の Web アプリを別のサービス プランに移動します。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler が実行されているかどうかを確認するにはどうすればよいですか。

プロファイラーは、Web アプリで継続的な Web ジョブとして実行されます。 [Azure Portal](https://portal.azure.com) で Web アプリ リソースを開くことができます。 **[WebJobs]** ウィンドウで、**ApplicationInsightsProfiler** の状態を確認してください。 実行されていない場合は、**[ログ]** を開いて詳細情報を入手します。

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>プロファイラーが実行されているのにスタック サンプルが見つからないのはなぜですか。

次の点を確認してください。

* Web アプリ サービス プランが Basic レベル以上であることを確認します。
* Web アプリで Application Insights SDK 2.2 Beta 以降が有効になっていることを確認します。
* Web アプリの **APPINSIGHTS_INSTRUMENTATIONKEY** 設定が、Application Insights SDK で使用されているインストルメンテーション キーと同じキーで構成されていることを確認します。
* Web アプリが .NET Framework 4.6 で動作していることを確認します。
* Web アプリが ASP.NET Core アプリケーションである場合は、[必須の依存関係](#aspnetcore)を確認してください。

プロファイラーが起動されると、短いウォーミングアップ時間中に、複数のパフォーマンス トレースが高頻度で収集されます。 その後は、1 時間ごとに 2 分間パフォーマンス トレースが収集されます。  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>これまで Azure サービス プロファイラーを使用していましたが、 それはどうなったのでしょうか。  

Application Insights Profiler を有効にすると、Azure Service Profiler エージェントは無効になります。

### <a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが、要求期間よりも長くなる場合があります。

これは、要求に関連するスレッドが 2 つ以上存在し、そのスレッドが並行して動作している場合に発生する可能性があります。 この場合、スレッド時間全体は経過時間よりも長くなります。 あるスレッドが、他のスレッドが完了するのを待機している可能性があります。 ビューアーではこれを検出して不要な待機を省略しようとしますが、重要かもしれない情報は省略しないようにするため、結果として情報が過剰になる傾向があります。  

トレースに並列スレッドがある場合は、要求のクリティカル パスを特定できるように、どのスレッドが待機しているかを確認してください。 通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドの方に注目し、待機しているスレッドの時間は無視してください。

### <a id="issue-loading-trace-in-viewer"></a>プロファイリング データがない

次の点を確認してください。

* 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。
* プロキシまたはファイアウォールが https://gateway.azureserviceprofiler.net へのアクセスをブロックしていないことを確認します。
* アプリで使用している Application Insights インストルメンテーション キーが、プロファイルを有効にしたときに使用した Application Insights リソースと同じであることを確認します。 キーは、通常、ApplicationInsights.config にありますが、web.config ファイルや app.config ファイルにある場合もあります。

### <a name="error-report-in-the-profiling-viewer"></a>プロファイリング ビューアーのエラーの報告

ポータルでサポート チケットを送信してください。 エラー メッセージの関連付け ID を必ず含めてください。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>配置エラー: ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Web アプリを、プロファイラーが有効な App Service リソースに再デプロイする場合、次のようなメッセージが表示される可能性があります。

ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

このエラーは、スクリプトまたは Visual Studio Team Services デプロイ パイプラインから Web 配置を実行した場合に発生します。 解決するには、次の追加デプロイ パラメーターを、Web 配置タスクに追加します。

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

こうしたパラメーターにより、Application Insights Profiler で使用されたフォルダーが削除され、再デプロイ プロセスのブロックが解除されます。 これは現在実行中のプロファイラー インスタンスには影響しません。


## <a name="manual-installation"></a>手動のインストール

プロファイラーを構成すると、Web アプリの設定が更新されます。 環境に応じて、更新を手動で適用することもできます。 たとえば、PowerApps 用の App Service 環境でアプリケーションが実行されている場合です。

1. [Web アプリ] コントロール ウィンドウで、**[設定]** を開きます。
2. **.Net Framework バージョン**を **v4.6** に設定します。
3. **[Always On]** を**オン**に設定します。
4. __APPINSIGHTS_INSTRUMENTATIONKEY__ アプリ設定を追加し、その値を SDK によって使用されているインストルメンテーション キーと同じものに設定します。
5. **[高度なツール]** を開きます。
6. **[移動]** を選択して、Kudu の Web サイトを開きます。
7. Kudu の Web サイトで、**[Site extensions]\(サイトの拡張機能\)** を選択します。
8. Azure Web Apps ギャラリーから __Application Insights__ をインストールします。
9. Web アプリを再起動します。

## <a id="aspnetcore"></a>ASP.NET Core のサポート

ASP.NET Core アプリケーションでは、Microsoft.ApplicationInsights.AspNetCore NuGet パッケージ 2.1.0-beta6 以降をインストールして、プロファイラーと共に使用する必要があります。 2017 年 6 月 27 日時点では、これより前のバージョンはサポートされていません。

## <a name="next-steps"></a>次のステップ

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
