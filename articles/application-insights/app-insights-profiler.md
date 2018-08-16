---
title: Application Insights Profiler を使用して Azure でライブ Web アプリをプロファイルする | Microsoft Docs
description: フットプリントの小さいプロファイラーを使用して Web サーバー コードのホット パスを特定できます。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 6048a17bf50ecac691c7cf687f87e454c54ee9d9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521885"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights を使用してライブ Azure Web アプリをプロファイルする

Azure Application Insights のこの機能は Azure App Service の Web Apps 機能のために一般公開されており、Azure コンピューティング リソースに対してはプレビューの段階です。 [プロファイラーのオンプレミスの使用に関してはこちらをご覧ください](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)。

この記事では、[Application Insights](app-insights-overview.md) を使用したときにライブ Web アプリケーションの各メソッドで費やされる時間について説明します。 Application Insights Profiler ツールは、アプリによって処理されたライブ要求の詳細なプロファイルを表示します。 Profiler は、ほとんどの時間を使用する*ホット パス*を強調します。 さまざまな応答時間を持つ要求がサンプリングごとにプロファイルされます。 さまざまな手法を使用することによって、アプリケーションに関連付けられたオーバーヘッドを最小限に抑えることができます。

Profiler は現在、Web Apps 上で実行されている ASP.NET および ASP.NET Core Web アプリに対して機能します。 Profiler を使用するには、Basic 以上のサービス レベルが必要です。

## <a id="installation"></a> Web Apps で Profiler を有効にする

Web アプリをデプロイ後、ソース コードに App Insights SDK が含まれているかどうかに関係なく、次を実行します。

1. Azure Portal の **[App Services]** ウィンドウに移動します。
1. **[設定] > [監視]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/app-insights-profiler/AppInsights-AppServices.png)

1. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択して Web アプリを監視します。 すべての既定のオプションをそのまま使用します。 **[コード レベルの診断]** は既定でオンになっており、Profiler を有効にします。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

1. Profiler は App Insights のサイト拡張機能を使用してインストールされ、App Services のアプリ設定を使用して有効になります。

    ![Profiler 用のアプリ設定][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Azure コンピューティング リソースに対して Profiler を有効にする (プレビュー)

詳細については、[Azure コンピューティング リソースのためのプレビュー バージョンの Profiler](https://go.microsoft.com/fwlink/?linkid=848155) に関するページを参照してください。

## <a name="view-profiler-data"></a>プロファイラー データを表示する

アプリケーションがトラフィックを受信していることを確認します。 実験を行っている場合は、[Application Insights パフォーマンス テスト](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)を使用して Web アプリへの要求を生成できます。 新しく Profiler を有効にした場合は、短いロード テストを約 15 分間実行できます。これにより、プロファイラー トレースが生成されます。 既にしばらくの間 Profiler を有効にしている場合は、Profiler が 1 時間に 2 回ランダムに、また毎回 2 分間実行されることに注意してください。 確実にサンプル プロファイラー トレースを取得できるように、最初に 1 時間のロード テストを実行することをお勧めします。

アプリケーションがトラフィックをいくつか受信したら、**[パフォーマンス]** ウィンドウに移動し、**[Take Actions] \(アクションの実行)** を選択してプロファイラー トレースを表示してから、**[Profiler Traces] \(プロファイラー トレース)** ボタンを選択します。

![Application Insights の [パフォーマンス] ウィンドウの Profiler トレースのプレビュー][performance-blade-v2-examples]

要求の実行に費やされた時間のコード レベルの内訳を表示するためのサンプルを選択します。

![Application Insights トレース エクスプローラー][trace-explorer]

トレース エクスプローラーは、次の情報を表示します。

* **ホット パスの表示**: 最大リーフ ノード、またはそれに近いノードが表示されます。 ほとんどの場合、このようなノードはパフォーマンスのボトルネックのすぐ近くにあります。
* **ラベル**: 関数またはイベントの名前。 このツリーには、コードと発生したイベント (SQL イベントや HTTP イベントなど) が混在して表示されます。 最上位のイベントは要求時間全体に相当します。
* **経過時間**: 操作の開始から終了までの期間。
* **時間**: 関数またはイベントが他の関数と関連して実行されていた時間。

## <a name="how-to-read-performance-data"></a>パフォーマンス データの解釈方法

Microsoft サービス プロファイラーでは、アプリのパフォーマンス分析に、サンプリング メソッドとインストルメンテーションを組み合わせて使用します。 詳細な収集が進行中のとき、サービス プロファイラーは 1 ミリ秒ごとに各マシン CPU の命令ポインターをサンプリングします。 各サンプルでは、現在実行中のスレッドの呼び出し履歴全体がキャプチャされます。 これにより、そのスレッドが実行した内容に関する詳細情報が、高い抽象化レベルと低い抽象化レベルの両方で取得されます。 また、サービス プロファイラーでは、アクティビティの相関関係と因果関係を追跡するために、コンテキスト切り替えイベント、タスク並列ライブラリ (TPL) イベント、スレッドプール イベントなど、他のイベントも収集されます。

タイムライン ビューに表示される呼び出し履歴は、サンプリングとインストルメンテーションの結果です。 各サンプルはスレッドの完全な呼び出し履歴をキャプチャするため、それには Microsoft .NET Framework や、参照しているその他のフレームワークのコードが含まれています。

### <a id="jitnewobj"></a>オブジェクトの割り当て (clr!JIT\_New または clr!JIT\_Newarr1)


  **clr!JIT\_New** と **clr!JIT\_Newarr1** は、マネージド ヒープからメモリを割り当てる .NET Framework のヘルパー関数です。 **clr!JIT\_New** は、オブジェクトが割り当てられるときに呼び出されます。 **clr!JIT\_Newarr1** は、オブジェクト配列が割り当てられるときに呼び出されます。 通常、これらの 2 つの関数は高速であり、比較的短時間で完了します。 タイムラインで **clr!JIT\_New** または **clr!JIT\_Newarr1** がかなり長時間かかっていることが確認された場合は、そのコードが多数のオブジェクトを割り当て、大量のメモリを消費している可能性があることを示しています。

### <a id="theprestub"></a>コードの読み込み (clr!ThePreStub)

**clr!ThePreStub** は、初めて実行するコードを準備する .NET Framework のヘルパー関数です。 これには通常、Just-In-Time (JIT) コンパイルが含まれます (ただし、これに限定されるわけではありません)。 C# の各メソッドでは、プロセスの有効期間中に **clr!ThePreStub** が最大 1 回呼び出されます。

要求に対して **clr!ThePreStub** がかなりの時間を要している場合は、その要求が、このメソッドを実行する最初の要求であることを示します。 .NET Framework ランタイムが最初のメソッドを読み込むには、かなりの時間がかかります。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用するか、アセンブリで Native Image Generator (ngen.exe) を実行することを検討してください。

### <a id="lockcontention"></a>ロックの競合 (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** または **clr!JITutil\_MonEnterWorker** は、現在のスレッドが、ロックが解放されるのを待っていることを示します。 このテキストは多くの場合、C# の **LOCK** ステートメントを実行するとき、**Monitor.Enter** メソッドを呼び出すとき、または **MethodImplOptions.Synchronized** 属性を持つメソッドを呼び出すときに表示されます。 ロックの競合は通常、スレッド _A_ がロックを取得し、スレッド _A_ がそれを解放する前にスレッド _B_ が同じロックを取得しようとしたときに発生します。

### <a id="ngencold"></a>コードの読み込み ([COLD])

**mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined** のように、メソッド名に **[COLD]** が含まれている場合は、.NET Framework ランタイムが、<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ガイド付き最適化のプロファイル</a>によって最適化されていないコードを初めて実行していることを示します。 これは、メソッドごとに、プロセスの有効期間中に最大 1 回表示されます。

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

**[When]\(実行期間\)** 列は、ノードについて収集された包括的なサンプルが時間の経過と共にどのように変化するかを視覚化したものです。 要求の範囲全体は 32 個のタイム バケットに分割されます。 そのノードの包括的なサンプルは、その 32 個のバケットに蓄積されます。 各バケットはバーとして表されます。 バーの長さは、調整された値を表します。 **CPU_TIME** または **BLOCKED_TIME** とマークされているか、またはリソース (CPU、ディスク、スレッドなど) の消費との明らかな関係が存在するノードの場合、このバーは、そのバケットの期間中のいずれかのリソースの消費を表します。 これらのメトリックでは、複数のリソースを消費することによって 100 % を超える値になることがあります。 たとえば、ある間隔の間に平均して 2 つの CPU を使用した場合は、200 % になります。

## <a name="limitations"></a>制限事項

既定のデータ保有期間は 5 日です。 1 日あたりに取り込まれる最大データは 10 GB です。

Profiler サービスの使用に料金は発生しません。 Profiler サービスを使用するには、Web アプリが少なくとも Basic レベルの Web Apps でホストされている必要があります。

## <a name="overhead-and-sampling-algorithm"></a>オーバーヘッドとサンプリング アルゴリズム

Profiler は、トレースをキャプチャするために Profiler が有効になっているアプリケーションをホストする各仮想マシン上で 1 時間に 2 分間ランダムに実行されます。 Profiler が実行されているとき、サーバーには 5 ～ 15 % の CPU オーバーヘッドが追加されます。

アプリケーションのホスティングに使用できるサーバーの数が多いほど、Profiler が全体的なアプリケーション パフォーマンスに与える影響は少なくなります。 これは、サンプリング アルゴリズムによって、Profiler が常にサーバーの 5 % でしか実行されなくなるためです。 Web 要求の処理に使用できるサーバーの数が増え、Profiler の実行によって発生するサーバー オーバーヘッドが相殺されます。

## <a name="disable-profiler"></a>Profiler を無効にする

個々の Web アプリ インスタンスで Profiler を停止または再起動するには、**[Web ジョブ]** で Web Apps リソースに移動します。 Profiler を削除するには、**[拡張機能]** に移動します。

![Web ジョブで Profiler を無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべての Web アプリで Profiler を有効にすることをお勧めします。

Web アプリに対する変更を WebDeploy を使用してデプロイする場合は、デプロイ中の削除対象から App_Data フォルダーを除外してください。 そうしないと、次に Azure に Web アプリケーションをデプロイするときに Profiler 拡張機能のファイルが削除されます。


## <a id="troubleshooting"></a>トラブルシューティング

### <a name="too-many-active-profiling-sessions"></a>アクティブなプロファイリング セッションが多すぎる

現在、同じサービス プランで実行されている最大 4 つの Azure Web アプリとデプロイ スロットで Profiler を有効にできます。 Profiler Web ジョブが報告しているアクティブなプロファイリング セッションが多すぎる場合は、一部の Web アプリを別のサービス プランに移動します。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler が実行されているかどうかを確認するにはどうすればよいですか。

Profiler は、Web アプリ内の継続的な Web ジョブとして実行されます。 Web アプリのリソースは、[Azure Portal](https://portal.azure.com) で開くことができます。 **[WebJobs]** ウィンドウで、**ApplicationInsightsProfiler** の状態を確認してください。 実行されていない場合は、**[ログ]** を開いて詳細情報を入手します。

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Profiler が実行されているのに、呼び出し履歴の例が何も見つからないのはなぜですか。

次の点を確認してください。

* Web アプリ サービス プランが Basic レベル以上であることを確認します。
* Web アプリで Application Insights SDK 2.2 Beta 以降が有効になっていることを確認します。
* Web アプリの **APPINSIGHTS_INSTRUMENTATIONKEY** 設定が、Application Insights SDK で使用されているインストルメンテーション キーと同じキーで構成されていることを確認します。
* Web アプリが .NET Framework 4.6 で動作していることを確認します。
* Web アプリが ASP.NET Core アプリケーションの場合は、ASP.NET Core 2.0 以降が実行されている必要があります。

Profiler が起動された後、Profiler が複数のパフォーマンス トレースをアクティブに収集する短いウォームアップ期間が存在します。 その後、Profiler は 1 時間に 2 分間パフォーマンス トレースを収集します。

> [!NOTE]
> プロファイラー エージェントには、ASP.NET Core 2.1 上で実行されているアプリケーションから取得されたトレースをアップロードできないというバグがあります。 現在、修正プログラムを作成中です。間もなく準備できます。

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>私は Azure Service Profiler を使用していました。 それはどうなったのでしょうか。

Application Insights Profiler を有効にすると、Azure Service Profiler エージェントは無効になります。

### <a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが、要求期間よりも長くなる場合があります。

この状況は、ある要求に 2 つ以上のスレッドが関連付けられ、それらが並行して動作している場合に発生することがあります。 この場合、スレッド時間全体は経過時間よりも長くなります。 あるスレッドが、他のスレッドが完了するのを待機している可能性があります。 ビューアーはこれを検出して不要な待機を省略しようとしますが、重大になる可能性がある情報を省略するのではなく、誤って多すぎる情報を表示します。

トレースに並列スレッドがある場合は、要求のクリティカル パスを特定できるように、どのスレッドが待機しているかを確認してください。 通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドに集中し、待機中のスレッドの時間は無視してください。

### <a id="issue-loading-trace-in-viewer"></a>プロファイリング データがない

次の点を確認してください。

* 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。
* プロキシまたはファイアウォールが https://gateway.azureserviceprofiler.net へのアクセスをブロックしていないことを確認します。
* アプリで使用している Application Insights インストルメンテーション キーが、プロファイリングを有効にするために使用した Application Insights リソースと同じであることを確認します。 このキーは通常 ApplicationInsights.config ファイル内にありますが、web.config または app.config ファイル内にもある可能性があります。

### <a name="error-report-in-the-profiling-viewer"></a>プロファイリング ビューアーのエラーの報告

ポータルでサポート チケットを送信してください。 エラー メッセージの関連付け ID を必ず含めてください。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>配置エラー: ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler が有効になった Web Apps リソースに Web アプリを再デプロイしている場合は、次のようなメッセージが表示されることがあります。

*ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

このエラーは、スクリプトまたは Visual Studio Team Services デプロイ パイプラインから Web 配置を実行した場合に発生します。 解決するには、次の追加デプロイ パラメーターを、Web 配置タスクに追加します。

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

こうしたパラメーターにより、Application Insights Profiler で使用されたフォルダーが削除され、再デプロイ プロセスのブロックが解除されます。 これらは、現在実行されている Profiler インスタンスに影響を与えません。

## <a name="manual-installation"></a>手動のインストール

Profiler を構成すると、Web アプリの設定に対して更新が行われます。 環境に応じて、更新を手動で適用することもできます。 1 つの例として、アプリケーションが PowerApps 用の Web Apps 環境で実行されている場合があります。

1. **[Web App Control] \(Web アプリ コントロール)** ウィンドウで、**[設定]** を開きます。
1. **.Net Framework バージョン**を **v4.6** に設定します。
1. **[Always On]** を**オン**に設定します。
1. **APPINSIGHTS_INSTRUMENTATIONKEY** アプリ設定を追加し、その値を SDK によって使用されているものと同じインストルメンテーション キーに設定します。
1. **[高度なツール]** を開きます。
1. **[移動]** を選択して、Kudu の Web サイトを開きます。
1. Kudu の Web サイトで、**[Site extensions]\(サイトの拡張機能\)** を選択します。
1. Azure Web Apps ギャラリーから **Application Insights** をインストールします。
1. Web アプリを再起動します。

## <a id="profileondemand"></a> Profiler を手動でトリガーする

Profiler は、ボタンを 1 回クリックすることで手動でトリガーできます。 Web パフォーマンス テストを実行しているとします。 Web アプリに負荷がかかった状態のパフォーマンスを把握するには、トレースが必要です。 ロード テストが実行されるタイミングはわかっているので、トレースがキャプチャされるタイミングをコントロールすることが重要ですが、ランダム サンプリングの間隔により欠落する場合があります。
次の手順では、このシナリオがどのように機能するかについて説明します。

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(省略可能) 手順 1: Web パフォーマンス テストを開始して Web アプリへのトラフィックを生成する

Web アプリが既にトラフィックを受信している、または手動でトラフィックを生成する場合は、このセクションをスキップして手順 2 に進みます。

Application Insights ポータルで、**[構成] > [パフォーマンス テスト]** に移動します。 [新規] ボタンをクリックして新しいパフォーマンス テストを開始します。
![新しいパフォーマンス テストを作成する][create-performance-test]

**[新しいパフォーマンス テスト]** ウィンドウで、テスト対象の URL を構成します。 すべての既定の設定をそのまま使用し、ロード テストの実行を開始します。

![ロード テストを構成する][configure-performance-test]

新しいテストはまずキューに登録済みと表示され、次に状態が進行中になります。

![ロード テストが送信されキューに登録済み][load-test-queued]

![ロード テストが進行中][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>手順 2: オンデマンドでプロファイラーを開始する

ロード テストの実行中、プロファイラーを開始して Web アプリが負荷を受けている間にトレースをキャプチャできます。
[Configure Profiler]\(Profiler の構成\) ウィンドウに移動します。

![[Configure Profiler]\(Profiler の構成\) ウィンドウの入力][configure-profiler-entry]

**[Configure Profiler]\(Profiler の構成\) ウィンドウ**には、リンクされた Web アプリのすべてのインスタンス上のプロファイラーをトリガーする **[Profile Now]\(今すぐプロファイル\)** ボタンが用意されています。 さらに、過去にプロファイラーが実行されていたタイミングを視覚的に確認できます。

![オンデマンドでのプロファイラー][profiler-on-demand]

プロファイラーの実行状態に関する通知や状態の変更が表示されます。

### <a name="step-3-view-traces"></a>手順 3: トレースを表示する

プロファイラーの実行が完了したら、通知の指示に従って [パフォーマンス] ブレードに移動し、トレースを表示します。

### <a name="troubleshooting-on-demand-profiler"></a>オンデマンドでのプロファイラーのトラブルシューティング

オンデマンド セッションの後に、次のような Profiler のタイムアウト エラー メッセージが表示されることがあります。

![Profiler のタイムアウト エラー][profiler-timeout]

このエラーが発生する理由として、次の 2 つが考えられます。

1. オンデマンドでのプロファイラー セッションは成功したものの、Application Insights が収集したデータを処理するのに時間がかかった。 データの処理が 15 分以内に完了しなかった場合、ポータルにタイムアウト メッセージが表示されます。 ただし、しばらくしてから Profiler のトレースが表示されます。 これが発生した場合、現時点ではエラー メッセージを無視してください。 現在、この問題の修正に積極的に取り組んでいます。

1. Web アプリでオンデマンド機能がない古いバージョンの Profiler エージェントが使用されている。 以前に Application Insights Profiler を有効にしたことがある場合、オンデマンド機能の使用を開始するには Profiler エージェントの更新が必要になることがあります。
  
次の手順に従って Profiler を確認し、最新の Profiler をインストールしてください。

1. App Services のアプリ設定に移動し、次の設定を確認します。
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights 用の適切なインストルメンテーション キーに置き換えます。
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 これらの設定のいずれかが設定されていない場合は、Application Insights の有効化ウィンドウに移動して、最新のサイト拡張機能をインストールしてください。

1. App Services ポータルの [Application Insights] ウィンドウに移動します。

    ![App Services ポータルで Application Insights を有効にします。][enable-app-insights]

1. 次のページで [更新] ボタンが表示されている場合は、それをクリックして Application Insights サイト拡張機能を更新します。これにより、最新の Profiler エージェントがインストールされます。
![サイト拡張機能の更新][update-site-extension]

1. 次に **[変更]** をクリックして Profiler がオンになっていることを確認し、**[OK]** を選択して変更を保存します。

    ![App Insights を変更して保存する][change-and-save-appinsights]

1. App Service の **[アプリ設定]** タブに戻り、次のアプリ設定項目が設定されていることをもう一度確認します。
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights 用の適切なインストルメンテーション キーに置き換えます。
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Profiler 用のアプリ設定][app-settings-for-profiler]

1. 必要に応じて、拡張機能のバージョンを確認し、利用可能な更新プログラムがないことを確認します。

    ![拡張機能の更新プログラムを確認する][check-for-extension-update]

## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
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
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
