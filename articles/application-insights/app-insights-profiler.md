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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c65ef9141898369b8fcadd4c52972b767aca7cfe
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights を使用してライブ Azure Web アプリをプロファイルする

*Azure Application Insights のこの機能は Azure App Service の Web Apps 機能のために一般公開されており、Azure コンピューティング リソースに対してはプレビューの段階です。*

この記事では、[Application Insights](app-insights-overview.md) を使用したときにライブ Web アプリケーションの各メソッドで費やされる時間について説明します。 Application Insights Profiler ツールは、アプリによって処理されたライブ要求の詳細なプロファイルを表示します。 Profiler は、ほとんどの時間を使用する*ホット パス*を強調します。 さまざまな応答時間を持つ要求がサンプリングごとにプロファイルされます。 さまざまな手法を使用することによって、アプリケーションに関連付けられたオーバーヘッドを最小限に抑えることができます。

Profiler は現在、Web Apps 上で実行されている ASP.NET および ASP.NET Core Web アプリに対して機能します。 Profiler を使用するには、Basic 以上のサービス レベルが必要です。

## <a id="installation"></a> Web Apps Web アプリで Profiler を有効にする
アプリケーションが既に Web アプリに発行されているが、Application Insights を使用するためにソース コードでまだ何も実行していない場合は、次を実行します。
1. Azure Portal の **[App Services]** ウィンドウに移動します。
2. **[監視]** で、**[Application Insights]** を選択した後、ウィンドウの指示に従って新しいリソースを作成するか、または既存の Application Insights リソースを選択して Web アプリを監視します。

   ![App Service ポータルで App Insights を有効にする][appinsights-in-appservices]

3. プロジェクト ソース コードにアクセスできる場合は、[Application Insights をインストール](app-insights-asp-net.md)します。  
   既にインストールされている場合は、バージョンが最新であることを確認します  最新バージョンであることを確認するには、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** > **[更新]** > **[すべてのパッケージを更新する]** を選択します。 次に、アプリをデプロイします。

ASP.NET Core アプリケーションで Profiler を使用するには、Microsoft.ApplicationInsights.AspNetCore NuGet パッケージ 2.1.0-beta6 以降のインストールが必要です。 2017 年 6 月 27 日より、これより前のバージョンはサポートされません。

1. [Azure Portal](https://portal.azure.com) で、Web アプリの Application Insights リソースを開きます。 
2. **[パフォーマンス]** > **[Application Insights Profiler を有効にする]** を選択します。

   ![プロファイラーの有効化バナーを選択する][enable-profiler-banner]

3. あるいは、**[プロファイラー]** 構成を選択して状態を表示したり、Profiler を有効または無効にしたりできます。

   ![Profiler 構成を選択する][performance-blade]

   Application Insights で構成されている Web アプリの一覧が **[プロファイラー]** 構成ウィンドウに表示されます。 前の手順に従っている場合は、Profiler エージェントがインストールされています。 

4. **[プロファイラー]** 構成ウィンドウで、**[Profiler の有効化]** を選択します。

5. 必要な場合は、手順に従って Profiler エージェントをインストールします。 Application Insights で Web アプリが構成されていない場合は、**[リンクされたアプリの追加]** を選択します。

   ![[構成] ウィンドウのオプション][linked app services]

Web Apps プランによってホストされる Web アプリとは異なり、Azure コンピューティング リソースでホストされるアプリケーション (Azure Virtual Machines、仮想マシン スケール セット、Azure Service Fabric、Azure Cloud Services など) は Azure によって直接管理されません。 この場合、リンク先 Web アプリはありません。 アプリにリンクするのではなく、**[Profiler の有効化]** を選択します。

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Azure コンピューティング リソースに対して Profiler を有効にする (プレビュー)

詳細については、[Azure コンピューティング リソースのためのプレビュー バージョンの Profiler](https://go.microsoft.com/fwlink/?linkid=848155) に関するページを参照してください。

## <a name="view-profiler-data"></a>プロファイラー データを表示する

アプリケーションがトラフィックを受信していることを確認します。 実験を行っている場合は、[Application Insights パフォーマンス テスト](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test)を使用して Web アプリへの要求を生成できます。 新しく Profiler を有効にした場合は、短いロード テストを約 15 分間実行できます。これにより、プロファイラー トレースが生成されます。 既にしばらくの間 Profiler を有効にしている場合は、Profiler が 1 時間に 2 回ランダムに、また毎回 2 分間実行されることに注意してください。 確実にサンプル プロファイラー トレースを取得できるように、最初に 1 時間のロード テストを実行することをお勧めします。

アプリケーションがトラフィックをいくつか受信したら、**[パフォーマンス]** ウィンドウに移動し、**[Take Actions] (アクションの実行)** を選択してプロファイラー トレースを表示してから、**[Profiler Traces] (プロファイラー トレース)** ボタンを選択します。

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
**clr!JIT\_New** と **clr!JIT\_Newarr1** は、マネージ ヒープからメモリを割り当てる .NET Framework のヘルパー関数です。 **clr!JIT\_New** は、オブジェクトが割り当てられるときに呼び出されます。 **clr!JIT\_Newarr1** は、オブジェクト配列が割り当てられるときに呼び出されます。 通常、これらの 2 つの関数は高速であり、比較的短時間で完了します。 タイムラインで **clr!JIT\_New** または **clr!JIT\_Newarr1** がかなり長時間かかっていることが確認された場合は、そのコードが多数のオブジェクトを割り当て、大量のメモリを消費している可能性があることを示しています。

### <a id="theprestub"></a>コードの読み込み (clr!ThePreStub)
**clr!ThePreStub** は、初めて実行するコードを準備する .NET Framework のヘルパー関数です。 これには通常、Just-In-Time (JIT) コンパイルが含まれます (ただし、これに限定されるわけではありません)。 C# の各メソッドでは、プロセスの有効期間中に **clr!ThePreStub** が最大 1 回呼び出されます。

要求に対して **clr!ThePreStub** がかなりの時間を要している場合は、その要求が、このメソッドを実行する最初の要求であることを示します。 .NET Framework ランタイムが最初のメソッドを読み込むには、かなりの時間がかかります。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用するか、アセンブリで Native Image Generator (ngen.exe) を実行することを検討してください。

### <a id="lockcontention"></a>ロックの競合 (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** または **clr!JITutil\_MonEnterWorker** は、現在のスレッドが、ロックが解放されるのを待っていることを示します。 このテキストは通常、C# の **LOCK** ステートメントを実行するとき、**Monitor.Enter** メソッドを呼び出すとき、または **MethodImplOptions.Synchronized** 属性を持つメソッドを呼び出すときに表示されます。 ロックの競合は通常、スレッド _A_ がロックを取得し、スレッド _A_ がそれを解放する前にスレッド _B_ が同じロックを取得しようとしたときに発生します。

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
* Web アプリが ASP.NET Core アプリケーションである場合は、[必須の依存関係](#aspnetcore)を確認してください。

Profiler が起動された後、Profiler が複数のパフォーマンス トレースをアクティブに収集する短いウォームアップ期間が存在します。 その後、Profiler は 1 時間に 2 分間パフォーマンス トレースを収集します。

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

1. **[Web App Control] (Web アプリ コントロール)** ウィンドウで、**[設定]** を開きます。
2. **.Net Framework バージョン**を **v4.6** に設定します。
3. **[Always On]** を**オン**に設定します。
4. **APPINSIGHTS_INSTRUMENTATIONKEY** アプリ設定を追加し、その値を SDK によって使用されているものと同じインストルメンテーション キーに設定します。
5. **[高度なツール]** を開きます。
6. **[移動]** を選択して、Kudu の Web サイトを開きます。
7. Kudu の Web サイトで、**[Site extensions]\(サイトの拡張機能\)** を選択します。
8. Azure Web Apps ギャラリーから **Application Insights** をインストールします。
9. Web アプリを再起動します。

## <a id="profileondemand"></a> Profiler を手動でトリガーする
Profiler を開発したときに、アプリ サービスで Profiler をテストできるようにコマンド ライン インターフェイスを追加しました。 この同じインターフェイスを使用すると、ユーザーも Profiler の起動方法をカスタマイズできます。 高いレベルでは、Profiler は Web Apps Kudu System を使用して、バックグラウンドでプロファイリングを管理します。 Application Insights 拡張機能をインストールすると、Profiler をホストする継続的な Web ジョブが作成されます。 この同じテクノロジを使用して、ニーズに合わせてカスタマイズできる新しい Web ジョブが作成されます。

このセクションでは、次の方法について説明します。

* ボタンの押下によって Profiler を 2 分間起動できる Web ジョブを作成します。
* Profiler の実行をスケジュールできる Web ジョブを作成します。
* Profiler の引数を設定します。


### <a name="set-up"></a>セットアップ
最初に、Web ジョブのダッシュボードに慣れておいてください。 **[設定]** で、**[Web ジョブ]** タブを選択します。

![[Web ジョブ] ブレード](./media/app-insights-profiler/webjobs-blade.png)

見てわかるように、このダッシュボードには、現在サイトにインストールされているすべての Web ジョブが表示されます。 Profiler ジョブを実行している ApplicationInsightsProfiler2 Web ジョブが確認できます。 ここで、手動およびスケジュールされたプロファイリングのための新しい Web ジョブを作成します。

必要なバイナリを入手するには、次を実行します。

1.  Kudu サイトの **[開発ツール]** タブで、Kudu ロゴの入った **[高度なツール]** タブを選択してから、**[Go] (実行)** を選択します。  
   新しいサイトが開き、自動的にサインインされます。
2.  Profiler バイナリをダウンロードするには、ページの上部にある **[デバッグ コンソール]** > **[CMD]** からファイル エクスプローラーに移動します。
3.  **[サイト]** > **[wwwroot]** > **[App_Data]** > **[ジョブ]** > **[継続的]** を選択します。  
   *ApplicationInsightsProfiler2* という名前のフォルダーが表示されます。 
4. フォルダーの左側にある **[ダウンロード]** アイコンを選択します。  
   このアクションによって *ApplicationInsightsProfiler2.zip* ファイルがダウンロードされます。 この zip アーカイブの移動先のクリーンなディレクトリを作成することをお勧めします。

### <a name="setting-up-the-web-job-archive"></a>Web ジョブ アーカイブの設定
Azure Web サイトに新しい Web ジョブを追加する場合、基本的には、内部に *run.cmd* ファイルを含む zip アーカイブを作成します。 この *run.cmd* ファイルは、Web ジョブ システムに、その Web ジョブが実行されたときに行う処理を指示します。

1.  新しいフォルダー (*RunProfiler2Minutes* など) を作成します。
2.  抽出された *ApplicationInsightProfiler2* フォルダーからこの新しいフォルダーにファイルをコピーします。
3.  新しい *run.cmd* ファイルを作成します。  
    便宜のために、開始する前に Visual Studio Code で作業フォルダーを開くことができます。
4.  このファイル内にコマンド `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` を追加します。 これらのコマンドは、次のように記述されます。

    * `start`: Profiler に起動するよう指示します。  
    * `--engine-mode immediate`: Profiler に直ちにプロファイリングを開始するよう指示します。  
    * `--single`: Profiler に実行した後、自動的に停止するよう指示します。  
    * `--immediate-profiling-duration 120`: Profiler に 120 秒間 (つまり 2 分間) 実行するよう指示します。

5.  変更を保存します。
6.  右クリックし、**[Send to] (送信先)** > **[Compressed (zipped) folder] (圧縮 (zip 形式) フォルダー)** を選択することによって、このフォルダーをアーカイブします。  
   このアクションによって、フォルダーの名前を使用する .zip ファイルが作成されます。

![Profiler の起動コマンド](./media/app-insights-profiler/start-profiler-command.png)

Web ジョブの .zip ファイルが作成されたので、これを使用してサイト内の Web ジョブを設定できます。

### <a name="add-a-new-web-job"></a>新しい Web ジョブの追加
このセクションでは、サイト上で新しい Web ジョブを追加します。 次の例は、手動でトリガーされる Web ジョブを追加する方法を示しています。 手動でトリガーされる Web ジョブを追加した後のプロセスは、スケジュールされた Web ジョブの場合とほぼ同じです。

1.  **[Web ジョブ]** ダッシュボードに移動します。
2.  ツールバーの **[追加]** を選択します。
3.  Web ジョブに名前を付けます。  
    明確にするために、アーカイブの名前を一致させ、それを *run.cmd* ファイルのさまざまなバージョンに対して開くようにすると有効な場合があります。
4.  フォームの **[ファイルのアップロード]** 領域で、**[ファイルを開く]** アイコンを選択し、前のセクションで作成した .zip ファイルを検索します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。  **[種類]** ボックスで、**[トリガー]** を選択します。  
    b.  **[トリガー]** ボックスで、**[手動]** を選択します。

5.  **[OK]**を選択します。

![Profiler の起動コマンド](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Profiler を実行する

これで、手動でトリガーできる新しい Web ジョブが作成されたので、このセクションの手順に従ってその実行を試みることができます。

設計により、マシン上でいつでも 1 つの *ApplicationInsightsProfiler.exe* プロセスだけが実行されるようにできます。 そのため、開始する前に、このダッシュボードから *[継続的]* Web ジョブを無効にします。 
1. 新しい Web ジョブを含む行を選択してから、**[停止]** を選択します。 
2. ツールバーの **[更新]** を選択し、ジョブが停止された状態が示されていることを確認します。
3. 新しい Web ジョブを含む行を選択してから、**[実行]** を選択します。
4. その行が選択されたままにして、ツールバーの **[ログ]** コマンドを選択します。  
    このアクションによって、新しい Web ジョブのための Web ジョブ ダッシュボードが開かれ、最新の実行とその結果が一覧表示されます。
5. 今起動した実行のインスタンスを選択します。  
    新しい Web ジョブを正常にトリガーした場合は、プロファイリングが開始されたことを確認する、Profiler から来たいくつかの診断ログを表示できます。

### <a name="things-to-consider"></a>考慮事項

この方法は比較的単純ではありますが、次の点を考慮してください。

* Web ジョブは Microsoft のサービスによって管理されないため、Web ジョブのエージェント バイナリを更新する方法がありません。 現在は Microsoft のバイナリの安定したダウンロード ページが存在しないため、最新のバイナリを入手するには、前の手順で行ったように、拡張機能を更新し、それを *[継続的]* フォルダーから取得する方法しかありません。

* このプロセスでは、もともとはエンド ユーザーではなく、開発者向けに設計されたコマンド ライン引数を利用しているため、これらの引数は将来変更される可能性があります。 アップグレードすると変更される可能性があることに注意してください。 Web ジョブを追加、実行、およびテストして動作することを確認できるため、これはそれほど大きな問題ではありません。 最終的には、手動プロセスなしでこれを処理するための UI を構築する予定です。

* Web Apps の Web ジョブ機能は独特です。 Web ジョブを実行するときは、そのプロセスの環境変数とアプリ設定が Web サイトのものと同じであることが保証されます。 つまり、コマンド ラインから Profiler にインストルメンテーション キーを渡す必要はありません。 Profiler は、環境からインストルメンテーション キーを選択します。 ただし、Web Apps の外部にある開発ボックスまたはマシンで Profiler を実行する場合は、インストルメンテーション キーを指定する必要があります。 これは、引数 `--ikey <instrumentation-key>` を渡すことによって実行できます。 この値は、アプリケーションが使用しているインストルメンテーション キーに一致している必要があります。 Profiler からのログ出力により、Profiler がどの ikey で起動されたかや、プロファイリング中にそのインストルメンテーション キーからのアクティビティが検出されたかどうかが示されます。

* 手動でトリガーされる Web ジョブは、Web フックでトリガーできます。 この URL は、ダッシュボードで Web ジョブを右クリックし、プロパティを表示することによって取得できます。 または、ツールバーで、テーブル内の Web ジョブを選択した後に **[プロパティ]** を選択できます。 このアプローチにより、CI/CD パイプライン (VSTS など) や Microsoft Flow (https://flow.microsoft.com/en-us/) のようなものから Profiler をトリガーするなど、無限の可能性が開かれます。 最終的に、ユーザーの選択肢は *run.cmd* ファイル (*run.ps1* ファイルでもかまいません) をどれだけ複雑にするかによって異なりますが、そこには柔軟性があります。

## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
