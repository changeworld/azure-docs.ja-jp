<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Troubleshooting Azure Websites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Visual Studio での Azure Web サイトのトラブルシューティング

Web アプリケーションの開発やテストにおけるトラブルシューティングは、[デバッグ モード][デバッグ モード]または [IntelliTrace][IntelliTrace] を使用して実行できます。デバッグ モードは、IIS Express を使ってローカルで実行するか、Azure Web サイトを使ってリモートで実行することができます。ただし、運用環境でのみ発生するエラーについては、アプリケーション コードまたは Web サーバーによって作成されるログを参照してデバッグするのが最善の方法となります。このチュートリアルでは、Visual Studio のツールを活用し、Azure Web サイトで実行されているアプリケーションをデバッグ モードでリモートから実行するか、アプリケーションと Web サーバーのログを参照することによってデバッグする方法を説明します。

学習内容:

-   Visual Studio から利用できる Azure のサイト管理機能。
-   Visual Studio のリモート ビューを使用して、リモート Web サイトにすぐに変更を加える方法。
-   Azure Web サイトでプロジェクトが実行されているときに、リモートからデバッグ モードを実行する方法。
-   アプリケーションのトレース ログを作成する方法と、ログが作成されている最中にそれらを確認する方法。
-   Web サーバーのログ (詳細なエラー メッセージ、失敗した要求トレースを含む) を確認する方法。
-   Azure のストレージ アカウントに診断ログを送り、そこでログを確認する方法。

### チュートリアル セグメント

1.  [前提条件][前提条件]
2.  [サイトの構成と管理][サイトの構成と管理]
3.  [リモート ビュー][リモート ビュー]
4.  [リモート デバッグ][リモート デバッグ]
5.  [診断ログの概要][診断ログの概要]
6.  [アプリケーションのトレース ログの作成と表示][アプリケーションのトレース ログの作成と表示]
7.  [Web サーバーのログの表示][Web サーバーのログの表示]
8.  [詳細なエラー メッセージ ログの表示][詳細なエラー メッセージ ログの表示]
9.  [ファイル システムのログのダウンロード][ファイル システムのログのダウンロード]
10. [ストレージ ログの表示][ストレージ ログの表示]
11. [失敗した要求ログの表示][失敗した要求ログの表示]
12. [次のステップ][次のステップ]

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、「[ASP.NET Web アプリケーションを Azure Web サイトに展開する][ASP.NET Web アプリケーションを Azure Web サイトに展開する]」で設定した開発環境、Web プロジェクト、および Azure Web サイトを使用します。このチュートリアルで示すコード サンプルは、C# MVC Web アプリケーションに対応していますが、トラブルシューティング手順は Visual Basic および Web フォームの各アプリケーションでも同じです。

リモート デバッグを実行するには、Visual Studio 2013 または Visual Studio 2012 Update 4 が必要です。このチュートリアルで紹介するその他の機能については、Visual Studio 2013 Express for Web や Visual Studio 2012 Express for Web でも動作します。

ストリーミング ログ機能は、.NET Framework 4 以降を対象とするアプリケーションでのみ動作します。

## <a name="sitemanagement"></a>サイトの構成と管理

Visual Studio は、管理ポータルで利用できるサイト管理機能や構成設定に一部アクセスできるようになっています。このセクションでは、その対象となる機能や設定について取り上げます。

1.  まだ Visual Studio から Azure にサインインしていない場合は、**サーバー エクスプローラー** の **[Azure に接続]** をクリックします。

    または、アカウントへのアクセスを可能にする管理証明書をインストールします。管理証明書により、追加の Azure サービス (SQL Database サービスとモバイル サービス) に対するアクセス許可がサーバー エクスプローラーに付与されます。証明書をインストールする方針を選択した場合は、**サーバー エクスプローラー**で、**Azure** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理]** をクリックします。**[Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブをクリックし、**[インポート]** をクリックします。操作手順に従い、Azure アカウント用のサブスクリプション ファイル (*.publishsettings* ファイル) をダウンロードしてインポートします。

    > [WACOM.NOTE]
    > サブスクリプション ファイルをダウンロードする場合は、ソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) に保存し、インポートが完了したらそのファイルを削除します。悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

    Visual Studio から Azure リソースへの接続の詳細については、「[アカウント、サブスクリプション、管理ロールの管理][アカウント、サブスクリプション、管理ロールの管理]」を参照してください。

2.  **サーバー エクスプローラー**で **[Azure]** を展開し、**[Web サイト]** を展開します。

3.  「[ASP.NET Web アプリケーションを Azure Web サイトに展開する][ASP.NET Web アプリケーションを Azure Web サイトに展開する]」で作成した Web サイトのノードを右クリックし、**[設定の表示]** をクリックします。

    ![サーバー エクスプローラーの 設定の表示][サーバー エクスプローラーの 設定の表示]

    **[Azure の Web サイト]** タブが表示され、Visual Studio から利用できるサイト管理タスクや構成タスクが確認できます。

    ![Azure Web サイト ウィンドウ][Azure Web サイト ウィンドウ]

    このチュートリアルでは、ログとトレースのドロップダウンを使用します。また、リモート デバッグも使用し、さまざまな方法でこの機能を有効にします。

    このウィンドウの [アプリケーション設定] ボックスと [接続文字列] ボックスについては、[Azure の Web サイト: アプリケーション文字列と接続文字列の動作に関するページ][Azure の Web サイト: アプリケーション文字列と接続文字列の動作に関するページ]を参照してください。

    このウィンドウでは実行できないサイト管理タスクを行う場合は、**[すべての Web サイト設定]** をクリックすると、ブラウザー ウィンドウが開いて管理ポータルにアクセスできます。詳細については、「[Web サイトの構成方法][Web サイトの構成方法]」を参照してください。

## <a name="remoteview"></a>リモート ビュー

通常は、Web.config ファイルで `customErrors` フラグを `On` または `RemoteOnly` に設定してサイトをデプロイします。これは、何か問題が発生したときに、役に立つエラー メッセージを表示しないことを意味します。表示されるエラーの多くは、次のようなページになります。

**'/' アプリケーションのサーバー エラー:**
![あまり役に立たないエラー ページ][あまり役に立たないエラー ページ]

**エラーが発生しました:**
![あまり役に立たないエラー ページ][1]

**Web サイト側でページを表示できません**
![あまり役に立たないエラー ページ][2]

多くの場合、エラーの原因を最も簡単に見つける方法は、詳細なエラー メッセージを有効にすることです。その結果、既に示した最初のスクリーンショットのように、対応方法が表示されます。ここでは、デプロイした Web.config ファイルの変更が必要です。プロジェクト内の *Web.config* ファイルを編集し、プロジェクトを再デプロイすること、または [Web.config の変換][Web.config の変換]を作成し、デバッグ ビルドをデプロイすることもできますが、より簡単な方法があります。*remote view* 機能を使用して、リモート サイトから**ソリューション エクスプローラー**でファイルを直接表示し、編集することができます。

1.  **サーバー エクスプローラー**で **[Azure]**、**[Web サイト]**、デプロイしようとする Web サイトに対応するノードの順に展開します。

    Web サイトのコンテンツ ファイルとログ ファイルへのアクセス許可を付与するノードが表示されます。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png" alt="[ファイル] と [ログ ファイル]" /></p>

2.  **[ファイル]** ノードを展開し、*[Web.config]* ファイルをダブルクリックします。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png" alt="[Web.config] を開く" /></p>


    Visual Studio は、リモート サイトから Web.config ファイルを開き、タイトル バーではファイル名 の横に "リモート" と表示されています。

3.  次の行を `system.web` 要素に追加します。

    `<customErrors mode="off"></customErrors>`

    ![Web.config の編集][Web.config の編集]

4.  あまり役に立たないエラー メッセージを示しているブラウザーを最新の情報に更新すると、次の例のような詳細なエラー メッセージが表示されます。

    ![詳細なエラー メッセージ][詳細なエラー メッセージ]

    (ここで示されているエラーは、赤く表示されている行を *Views\\Home\\Index.cshtml* に追加することによって作成したものです。)

Web.config ファイルを編集することは、Azure Web サイトにあるファイルの読み取りと編集を可能にして、トラブルシューティングを容易にする 1 つのシナリオの例にすぎません。

## <a name="remotedebug"></a>リモート デバッグ

詳細なエラー メッセージで十分な情報が表示されておらず、エラーをローカルで再現できない場合は、トラブルシューティングの別の方法として、リモートでデバッグ モードを実行することができます。ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。

リモート デバッグは、Visual Studio の各 Express Edition では機能しません。

このセクションでは、「[ASP.NET Web アプリケーションを Azure の Web サイトに展開する][ASP.NET Web アプリケーションを Azure Web サイトに展開する]」で作成したプロジェクトを使用してリモート デバッグを実行する方法を示します。

1.  「[ASP.NET Web アプリケーションを Azure Web サイトに展開する][ASP.NET Web アプリケーションを Azure Web サイトに展開する]」で作成した Web プロジェクトを開きます。

2.  *Controllers\\HomeController.cs* を開きます。

3.  `About()` メソッドを削除し、その位置に次のコードを挿入します。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

4.  `ViewBag.Message` の行に[ブレークポイントを設定][デバッグ モード]します。

5.  **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、**[発行]** をクリックします。

6.  **[プロファイル]** ボックスの一覧から、「[ASP.NET Web アプリケーションを Azure Web サイトに展開する][ASP.NET Web アプリケーションを Azure Web サイトに展開する]」で使用したものと同じプロファイルを選択します。

7.  **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

    ![デバッグ モードでの発行][デバッグ モードでの発行]

8.  デプロイが完了し、ブラウザーが起動してサイトの Azure URL が表示されたら、ブラウザーを閉じます。

9.  Visual Studio 2013 の場合:**サーバー エクスプローラー** で **[Azure]**、**[Web サイト]** の順に展開し、Web サイトを右クリックして **デバッガーの接続** をクリックします。

    ![デバッガーの接続][デバッガーの接続]

    ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

10. Visual Studio 2012 Update 4 の場合:<span id="vs2012"></span></a>

    -   Azure の管理ポータルで、Web サイトの **[構成]** タブに移動し、下へスクロールして **[サイト診断]** セクションを表示します。

    -   **リモート デバッグ** を **[オン]** に設定し、**[リモート デバッグに使用する Visual Studio のバージョン]**を **[2012]** に設定します。

    ![管理ポータルでのリモート デバッグの設定][管理ポータルでのリモート デバッグの設定]

    -   Visual Studio の **[デバッグ]** メニューで **[プロセスにアタッチ]** をクリックします。

    -   **[修飾子]** ボックスに Web サイトの URL を入力します。プレフィックス「`http://`」は付けずに入力してください。

    -   **[すべてのユーザーからのプロセスを表示する]** を選択します。

    -   資格情報を求められたら、Web サイトを発行する権限のあるユーザー名とパスワードを入力します。資格情報を入手するには、管理ポータルで Web サイトの [ダッシュボード] タブに移動し、**[発行プロファイルのダウンロード]** をクリックします。そのファイルをテキスト エディターで開くと、1 つ目の **userName=** と **userPWD=** の後に、ユーザー名とパスワードが表示されます。

    -   **[選択可能なプロセス]** テーブルにプロセスが表示されたら、**[w3wp.exe]** を選択し、**[アタッチ]** をクリックします。

    -   ブラウザーを起動して、目的のサイトの URL に移動します。

    デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

11. メニューの **[About]** をクリックします。

    Visual Studio がブレークポイントで停止します。コードが実行されている場所は、ローカル コンピューターではなく Azure 上です。

12. `currentTime` 変数にマウスを合わせて、時刻値を表示します。

    ![Azure で実行されているコード内の変数をデバッグ モードで表示][Azure で実行されているコード内の変数をデバッグ モードで表示]

    表示される時刻は、Azure サーバーの時刻です。ローカル コンピューターとはタイム ゾーンが異なります。

13. `currentTime` 変数に新しい値 (「Now running in Azure」など) を入力します。

14. F5 キーを押して実行を継続します。

    Azure で実行中の [About] ページに、先ほど currentTime 変数に対して入力した新しい値が表示されます。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png" alt="[About] ページ (値の変更後)" /></p>

### リモート デバッグに関する注意

-   運用環境におけるデバッグ モードの実行はお勧めできません。運用環境のサイトが複数のサーバー インスタンスにスケール アウトされていなければ、デバッグのトラフィックは自分の Web サーバー インスタンスにのみ関連付けられます。しかし、Web サーバーのインスタンスが複数存在する場合、デバッガーのアタッチ先となるインスタンスは無作為に決定されるため、そのインスタンスにブラウザーの要求を確実に渡すことができません。また、運用環境にデバッグ ビルドを展開することも一般的ではありません。リリース ビルドに対してはコンパイラが最適化を行うため、ソース コードの状況を行レベルで把握することは不可能です。運用環境の問題をトラブルシューティングするのに最も適しているリソースは、アプリケーション トレースと Web サーバーのログです。

-   リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。数分以上停止しているプロセスは、応答していないプロセスと見なされ、Azure によりシャットダウンされます。

-   デバッグ中は、サーバーから Visual Studio にデータが送信されるため、帯域幅の使用料に影響が及ぶ可能性があります。帯域幅使用料については、[Azure 料金計算ツール][Azure 料金計算ツール]を参照してください。

-   *Web.config* ファイルの `compilation` 要素の `debug` 属性が true に設定されていることを確認します。デバッグ ビルド構成で発行するときは、true が既定値です。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

-   デバッグ対象となるコードにデバッガーがステップ インしない場合、[マイ コードのみ] の設定を変更してみてください。詳細については、「[ステップ実行をマイ コードのみに制限する][ステップ実行をマイ コードのみに制限する]」を参照してください。

-   リモート デバッグ機能を有効にしたときに、サーバー上でタイマーが開始され、48 時間後にこの機能が自動的に無効になります。この 48 時間の上限はセキュリティとパフォーマンス上の理由で設定されています。必要に応じて、この機能を何回でも簡単に有効に戻すことができます。積極的にデバッグを実行している場合以外は、この機能を無効にしたままにすることをお勧めします。

-   すべてのプロセスに手動でデバッガーを接続できます。Web サイト プロセス (w3wp.exe) をデバッグする以外に、[WebJobs][WebJobs] など、他のプロセスをデバッグすることもできます。Visual Studio のデバッグ モードの使い方の詳細については、MSDN のトピック「[Visual Studio でのデバッグ][Visual Studio でのデバッグ]」を参照してください。

## <a name="logsoverview"></a><span class="short-header">ログの概要</span>診断ログの概要

Azure Web サイトで動作する ASP.NET アプリケーションでは、次の種類のログが作成されます。

-   **アプリケーション トレース ログ**    
    アプリケーションが [System.Diagnostics.Trace][System.Diagnostics.Trace] クラスのメソッドを呼び出すことによって作成されます。
-   **Web サーバーのログ**    
    Web サーバーは、サイトに届くすべての HTTP 要求について、それぞれログ エントリを作成します。
-   **詳細なエラー メッセージ ログ**    
    失敗した HTTP 要求 (ステータス コード 400 以上の要求) について、より詳しい情報を記した HTML ページが Web サーバーによって作成されます。
-   **失敗した要求トレース ログ**    
    失敗した HTTP 要求についての詳しいトレース情報を記録した XML ファイルが Web サーバーによって作成されます。また、ブラウザーで XML の体裁を設定するための XSL ファイルも作成されます。

ログ出力はサイトのパフォーマンスに影響を及ぼすため、Azure では、ログの種類ごとにその有効と無効を切り替えることができるようになっています。アプリケーション ログについては、特定の重大度レベルを超えるログだけを記録するように指定できます。新しい Web サイトを作成した時点ではすべてのログが既定で無効になります。

ログは、Web サイトのファイル システムにある *LogFiles* フォルダー内のファイルに出力され、FTP 経由でアクセスできます。Web サーバーのログとアプリケーションのログは、Azure のストレージ アカウントに出力することもできます。ストレージ アカウントには、ファイル システムよりも大量のログを保持することができます。ファイル システムを使用した場合、保存できるログの上限は 100 MB です。(ファイル システムのログは、短期間のみ保持されます。上限に達すると、古いログ ファイルは削除され、新しいログ ファイルのための領域が確保されます。)

## <a name="apptracelogs"></a><span class="short-header">アプリケーション ログ</span>アプリケーション トレース ログの作成と表示

このセクションでは、次のタスクを実行します。

-   [][ASP.NET Web アプリケーションを Azure Web サイトに展開する]以前のチュートリアルで作成した Web プロジェクトにトレース ステートメントを追加します。
-   プロジェクトをローカル実行したときのログを確認します。
-   Azure で実行中のアプリケーションによって生成されたログを確認します。

### アプリケーションへのトレース ステートメントの追加

1.  `System.Diagnostics` の `Trace` ステートメントと `using` ステートメントを追加するために、*Controllers\\HomeController.cs* ファイルを開いて、その内容を次のコードで置き換えます。

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Diagnostics;
        using System.Linq;
        using System.Web;
        using System.Web.Configuration;
        using System.Web.Mvc;
        namespace MyExample.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    Trace.WriteLine("Entering Index method");
                    ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
                    Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Index method");
                    return View();
                }

                public ActionResult About()
                {
                    Trace.WriteLine("Entering About method");
                    ViewBag.Message = "Your app description page.";
                    Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
                    Trace.WriteLine("Leaving About method");
                    return View();
                }

                public ActionResult Contact()
                {
                    Trace.WriteLine("Entering Contact method");
                    ViewBag.Message = "Your contact page.";
                    Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Contact method");
                    return View();
                }
            }
        }

### ローカルでのトレース出力の表示

1.  F5 キーを押してデバッグ モードでアプリケーションを実行します。

    既定のトレース リスナーは、すべてのトレース出力を他のデバッグ出力と一緒に**出力**ウィンドウに書き込みます。次の図は、`Index` メソッドに追加したトレース ステートメントからの出力結果を示したものです。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png" alt="[デバッグ] ウィンドウでのトレース" /></p>


    以降の手順では、コンパイルせずにデバッグ モードで、トレース出力を Web ページに表示する方法を紹介します。

2.  プロジェクト フォルダーにあるアプリケーションの Web.config ファイルを開き、ファイル末尾の終了 `<system.diagnostics>` 要素の直前に `</configuration>` 要素を追加します。

        <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    `WebPageTraceListener` を使用すると、ブラウザーから `/trace.axd` にアクセスすることでトレース出力を表示できます。

3.  Web.config ファイルの `<system.web>` に、次のような [trace 要素][trace 要素]を追加します。

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

4.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

5.  ブラウザー ウィンドウのアドレス バーで、URL に続けて「*trace.axd*」を入力し、Enter キーを押します (例: http://localhost:53370/trace.axd)。

6.  **[アプリケーション トレース]** ページの **[詳細の表示]** をクリックします。

    ![trace.axd][trace.axd]

    **[要求の詳細]** ページが表示され、`Index` メソッドに追加したトレース ステートメントからの出力が **[トレース情報]** セクションに表示されます。

    ![trace.axd][3]

    既定では、`trace.axd` の使用はローカルに限られます。*Web.config* ファイルで `trace` 要素に `localOnly="false"` を追加すると、リモート サイトからも利用できるようになります。その例を次に示します。

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    ただしセキュリティ上の理由から、運用サイトで `trace.axd` を有効にすることは、通常お勧めできません。もっと簡単に Azure Web サイトのトレース ログを確認する方法を以降のセクションで説明します。

### Azure でのトレース出力の確認

1.  **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[発行]** をクリックします。

2.  **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

    更新したプロジェクトを発行すると、ブラウザー ウィンドウが起動して自分のホーム ページが表示されます (ただし、**[接続]** タブの **[宛先 URL]** をクリアした場合を除く)。

3.  **サーバー エクスプローラー**で Web サイトを右クリックし、**[ストリーミング ログを出力ウィンドウで表示]** を選択します。

    ![コンテキスト メニューからストリーミング ログを表示][コンテキスト メニューからストリーミング ログを表示]

    ログ ストリーミング サービスに接続されたことを示すメッセージが**出力**ウィンドウに表示されます。ログが出力されないまま 1 分が経過すると、その都度、1 行の通知が追加されます。

    ![コンテキスト メニューからストリーミング ログを表示][4]

4.  アプリケーションのホーム ページが表示されているブラウザー ウィンドウで、**[Contact]** をクリックします。

    数秒すると、`Contact` メソッドに追加したエラー レベルのトレースの出力が**出力**ウィンドウに表示されます。

    ![出力ウィンドウのエラー トレース][出力ウィンドウのエラー トレース]

    Visual Studio にエラー レベルのトレースしか表示されていないのは、それが、ログ監視サービスを有効にしたときの既定の設定であるためです。先ほどサイトの設定ページを開いたときに目にしたように、新しい Azure Web サイトを作成したときは、すべてのログが既定で無効となります。

    ![アプリケーション ログ記録がオフ][アプリケーション ログ記録がオフ]

    ただし、**[ストリーミング ログを出力ウィンドウで表示]** を選択すると、**[アプリケーション ログ記録 (ファイル システム)]** が自動的に **[エラー]** に変更されます。これは、エラー レベルのログが報告されることを意味します。すべてのトレース ログを表示するためには、この設定を **[詳細]** に変更する必要があります。"エラー" よりも低い重大度レベルを選択すると、その重大度を超えるログがすべて報告されます。つまり [詳細] を選択した場合は、"情報"、"警告"、"エラー" に該当するログが表示されます。

5.  先ほどと同じように、**サーバー エクスプローラー**で Web サイトを右クリックし、**[設定の表示]** をクリックします。

6.  **[アプリケーション ログ記録 (ファイル システム)]** を **[詳細]** に変更し、**[保存]** をクリックします。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png" alt="トレース レベルを [詳細] に設定" /></p>

7.  **[Contact]** ページが表示されているブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

    数秒すると、**出力**ウィンドウにすべてのトレース出力が表示されます。

    ![詳細レベルのトレース出力][詳細レベルのトレース出力]

    このセクションでは、ログの有効と無効の切り替えを Azure Web サイトの設定で行いました。トレース リスナーの有効と無効は、Web.config ファイルで設定することもできます。ただし、Web.config ファイルに変更を加えると、アプリケーション ドメインの再利用処理が実行されます。一方、Web サイトを介してログを有効にした場合は、そのようにはなりません。問題の再現に時間がかかる場合や、発生のタイミングが散発的である場合、アプリケーション ドメインの再利用処理で問題が "解消" し、再発までしばらく待たなければならなくなります。Azure で診断を有効にすると、そのようなことにはならず、すぐにエラー情報を収集することができます。

### 出力ウィンドウの機能

**出力**ウィンドウの **[Azure ログ]** タブには、いくつかのボタンと 1 つのテキスト ボックスが表示されます。

![ログ タブのボタン][ログ タブのボタン]

それぞれの機能を次に示します。

-   **出力**ウィンドウをクリアする。
-   右端での折り返しを有効または無効にする。
-   ログの監視を開始または停止する。
-   監視するログを指定する。
-   ログをダウンロードする。
-   検索文字列または正規表現によるフィルターをログに適用する。
-   **出力**ウィンドウを閉じる。

検索文字列または正規表現を入力した場合、ログ情報は、Visual Studio によってクライアント側でフィルタリングされます。つまり、フィルターの条件は、**出力**ウィンドウにログが表示された後に入力できます。フィルターの条件を変更するためにログを生成し直す必要はありません。

## <a name="webserverlogs"></a><span class="short-header">Web サーバーのログ</span>Web サーバーのログの表示

Web サーバーのログには、サイト上の HTTP アクティビティがすべて記録されます。それらの HTTP アクティビティを**出力**ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1.  **サーバー エクスプローラー**から開いた Azure Web サイトの **[構成]** タブで [Web サーバーのログ記録] を **[オン]** に変更し、**[保存]** をクリックします。

    ![Web サーバーのログ記録を有効にする][Web サーバーのログ記録を有効にする]

2.  **出力**ウィンドウの <b>監視する Azure ログの指定</b> をクリックします。

    ![監視する Azure ログの指定][監視する Azure ログの指定]

3.  **[Azure ログ オプション]** ダイアログ ボックスの **[Web サーバーのログ]** を選択し、**[OK]** をクリックします。

    ![Web サーバーのログを監視する][Web サーバーのログを監視する]

4.  Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

    通常はアプリケーションのログが先に表示され、続けて Web サーバーのログが表示されます。ログが表示されるまでにしばらく時間がかかる場合があります。

    ![出力ウィンドウの Web サーバーのログ][出力ウィンドウの Web サーバーのログ]

Visual Studio で Web サーバーのログを初めて有効にしたとき、既定では、Azure によってファイル システムにログが書き込まれます。代わりに管理ポータルを使用して、Web サーバーのログの書き込み先として、ストレージ アカウントの BLOB コンテナーを指定することもできます。詳細については、「[How to Configure Web Sites (Web サイトの構成方法)][Web サイトの構成方法]」の**サイトの診断**に関するセクションを参照してください。

Web サーバーのログを Azure のストレージ アカウントに記録するように管理ポータルで指定した後、Visual Studio でログ記録を無効にした場合、Visual Studio で再度ログ記録を有効にすると、ストレージ アカウントの設定が復元されます。

## <a name="detailederrorlogs"></a><span class="short-header">エラー ログ</span>詳細なエラー メッセージ ログの表示

詳細なエラー ログでは、エラー応答コード (400 以上) が返された HTTP 要求について、いくつかの詳しい情報が確認できます。それらの HTTP アクティビティを**出力**ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1.  **サーバー エクスプローラー**から開いた Azure Web サイトの **[構成]** タブで <b>詳細なエラー メッセージ</b> を **[オン]** に変更し、**[保存]** をクリックします。

    ![詳細なエラー メッセージを有効にする][詳細なエラー メッセージを有効にする]

2.  **出力**ウィンドウの **監視する Azure ログの指定** をクリックします。

3.  **[Azure ログ オプション]** ダイアログ ボックスの **[すべてのログ]** をクリックし、**[OK]** をクリックします。

    ![すべてのログを監視する][すべてのログを監視する]

4.  ブラウザー ウィンドウのアドレス バーで、404 エラーの原因となるような余分な文字を URL に追加し (例: `http://localhost:53370/Home/Contactx`)、Enter キーを押します。

    数秒後、Visual Studio の**出力**ウィンドウに詳細なエラー ログが表示されます。

    ![出力ウィンドウの詳細なエラー ログ][出力ウィンドウの詳細なエラー ログ]

    Ctrl キーを押しながらリンクをクリックすると、次のような書式化されたログ出力がブラウザーに表示されます。

    ![ブラウザー ウィンドウの詳細なエラー ログ][ブラウザー ウィンドウの詳細なエラー ログ]

## <a name="downloadlogs"></a><span class="short-header">ログのダウンロード</span>ファイル システムのログのダウンロード

**出力**ウィンドウで監視できるすべてのログは *.zip* ファイルとしてダウンロードすることもできます。

1.  **出力**ウィンドウの **[ストリーミング ログのダウンロード]** をクリックします。

    ![ログ タブのボタン][5]

    エクスプローラーが起動して *[ダウンロード]* フォルダーが開き、ダウンロード済みのファイルが選択状態で表示されます。

    ![ダウンロードされたファイル][ダウンロードされたファイル]

2.  この *.zip* ファイルを展開すると、次のフォルダー構造が確認できます。

    ![ダウンロードされたファイル][6]

    -   アプリケーション トレース ログは、*LogFiles\\Application* フォルダーの *.txt* ファイルに記録されます。
    -   Web サーバーのログは、*LogFiles\\http\\RawLogs* フォルダーの *.log* ファイルに記録されます。これらのファイルの閲覧と操作は、[Log Parser][Log Parser] などのツールを使って行うことができます。
    -   詳細なエラー メッセージのログは、*LogFiles\\DetailedErrors* フォルダーの *.html* ファイルに記録されます。

    (*deployments* フォルダーは、ソース管理の発行によって作成されたファイルに使用されます。Visual Studio の発行に関連したファイルは保存されません。*Git* フォルダーは、ログ ファイル ストリーミング サービスやソース管理の発行に関連したトレースに使用されます。)

## <a name="storagelogs"></a><span class="short-header">ストレージ ログ</span>ストレージ ログの表示

アプリケーション トレース ログを Azure のストレージ アカウントに送って、Visual Studio で表示することもできます。ストレージ アカウントを作成して管理ポータルでストレージ ログを有効にし、**[Azure の Web サイト]** ウィンドウの **[ログ]** タブでそれらを表示します。

次の 3 個の送信先のいずれかまたはすべてにログを送信することができます。

-   ファイル システム。
-   ストレージ アカウント テーブル。
-   ストレージ アカウント BLOB。

送信先ごとに異なる重要度レベルを指定することもできます。

テーブル形式を使用すると、ログの詳細をオンラインで簡単に表示でき、またストリーミングもサポートされています。テーブル内にあるログを照会することや、作成されている新しいログを表示することもできます。BLOB 形式を使用すると、ログをファイルにダウンロードし、HDInsight を使ってそれらのログを簡単に解析できるようになります。HDInsight は、BLOB ストレージを処理する方法を把握しているためです。詳細については、[データ ストレージ オプション (Azure を使用した実際のクラウド アプリケーションの作成) に関するページ][データ ストレージ オプション (Azure を使用した実際のクラウド アプリケーションの作成) に関するページ]の「**Hadoop and MapReduce**」を参照してください。

現在、ファイル システムのログは詳細レベルに設定されています。次の手順では、ストレージ アカウント テーブルに切り替える目的で情報レベルのログを設定する方法を説明します。情報レベルを指定した場合、`Trace.TraceInformation`、`Trace.TraceWarning`、`Trace.TraceError` の呼び出しで作成されたすべてのログが表示され、`Trace.WriteLine` で作成されたログは除外されます。

多くの場合、ストレージ アカウント形式はファイル システム形式と比較して、ログに対してより多くのストレージと、より長い期間にわたる保持を実現します。アプリケーション トレース ログをストレージに送る別の利点は、ファイル システムのログでは得ることのできない詳しい情報をそれぞれのログと共に入手できることです。

1.  **サーバー エクスプローラー**で Web サイトを右クリックし、**[管理ポータルで開く]** をクリックします。

2.  管理ポータルで **[ストレージ]** タブをクリックし、**ストレージ アカウントの作成** をクリックします。

    ![ストレージ アカウントの作成][ストレージ アカウントの作成]

3.  ストレージ アカウントに使用する一意の URL を入力し、**ストレージ アカウントの作成** をクリックします。

    ![URL を入力する][URL を入力する]

4.  Visual Studio の **[Azure の Web サイト]** ウィンドウで、**[ログ]** タブをクリックし、**[ログの構成]** をクリックします。

    ![ダウンロードされたファイル][7]

    自分の Web サイトの管理ポータルの **[構成]** タブが開きます。この画面にアクセスするために、**[Web サイト]** タブをクリックして該当する Web サイトをクリックし、**[構成]** タブをクリックすることもできます。

5.  管理ポータルの **[構成]** タブで、下へスクロールしてアプリケーションの診断セクションを表示し、**[アプリケーション ログ記録 (ストレージ)]** を **[オン]** に変更します。

6.  **[ログ レベル]** を **[情報]** に変更します。

7.  **テーブル ストレージの管理** をクリックします。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png" alt="[テーブル ストレージの管理] をクリックします。" /></p>


    ストレージ アカウントが複数ある場合は、**[アプリケーション診断用のテーブル ストレージの管理]** ボックスで目的のストレージ アカウントを選択できます。新しいテーブルを作成するか、既存のテーブルを使用することができます。

    ![テーブル ストレージの管理][テーブル ストレージの管理]

8.  **[アプリケーション診断用のテーブル ストレージの管理]** ボックスのチェック マークをクリックしてこのボックスを閉じます。

9.  管理ポータルの **[構成]** タブで、**[保存]** をクリックします。

10. アプリケーションの Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

    これらの Web ページを閲覧することによって生成されたログ情報がストレージ アカウントに書き込まれます。

11. Visual Studio の **[Azure の Web サイト]** ウィンドウの **[ログ]** タブで、**[診断の概要]** の下に表示される **[最新の情報に更新]** をクリックします。

    <p><img src="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png" alt="[最新の情報に更新] をクリックする" /></p>


    既定では、直近 15 分間のログが **[診断の概要]** セクションに表示されます。この時間を変更することで、表示するログの量を増やすことができます。

    ("テーブルが見つからない" という内容のエラーが表示された場合は、**[アプリケーション ログ記録 (ストレージ)]** を有効にした後と、**[保存]** をクリックした後に、トレース対象のページを参照したことを確認してください。)

    ![ストレージ ログ][ストレージ ログ]

    この画面に **[プロセス ID]** と **[スレッド ID]** がログごとに表示されていることに注目してください。ファイル システムのログでは確認できない情報です。Azure のストレージ テーブルを直接参照することで、さらに別のフィールドを確認できます。

12. **[すべてのアプリケーション ログの表示]** をクリックします。

    Azure のストレージ テーブル ビューアーにトレース ログ テーブルが表示されます。

    ("シーケンスに要素が含まれていません" というエラーが表示された場合は、**サーバー エクスプローラー**を開き、**Azure** ノードで、自分のストレージ アカウントに該当するノードを展開し、**[テーブル]** を右クリックして **[最新の情報に更新]** をクリックします。)

    ![サーバー エクスプローラーのトレース テーブル][サーバー エクスプローラーのトレース テーブル]

    ![テーブル形式で表示されたストレージ ログ][テーブル形式で表示されたストレージ ログ]

    この画面には、他の画面では確認できないフィールドが表示されます。専用のクエリ ビルダー UI を使用してクエリを作成し、ログにフィルターを適用することもできます。詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照][サーバー エクスプローラーを使用したストレージ リソースの参照]」の「テーブル リソースの操作」にある「エンティティのフィルター処理」を参照してください。

13. 単一行の詳細情報を表示するには、いずれかの行を右クリックし、**[編集]** をクリックします。

    ![サーバー エクスプローラーのトレース テーブル][8]

## <a name="failedrequestlogs"></a><span class="short-header">失敗した要求ログ</span>失敗した要求トレース ログの表示

失敗した要求トレース ログは、URL の書き換えや認証の問題など、IIS による HTTP 要求の処理を詳しく把握する必要がある状況で活用できます。

Azure Web サイトでは、同じ失敗した要求トレース機能が使用されています。この機能は、IIS 7.0 以降で利用できます。ただし、ログに記録するエラーを指定するための IIS 設定にアクセスする必要はありません。失敗した要求トレースを有効にすると、すべてのエラーがキャプチャされます。

失敗した要求トレースは Visual Studio を使用して有効にできますが、それらを Visual Studio で表示することはできません。これらのログは XML ファイル形式になっています。ストリーミング ログ サービスで監視されるのは、プレーンテキスト モードでの読み取りが可能と判断されたファイル (*.txt*、*.html*、*.log* の各ファイル) だけです。

失敗した要求トレース ログは、ブラウザーから FTP で直接表示できるほか、FTP ツールを使ってローカル コンピューターにダウンロードした後、ローカルで表示することもできます。このセクションでは、ブラウザーで直接閲覧する方法を説明します。

1.  **サーバー エクスプローラー**から開いた **[Azure の Web サイト]** ウィンドウの **[構成]** タブで、**[失敗した要求トレース]** を **[オン]** に変更し、**[保存]** をクリックします。

    ![失敗した要求トレースを有効にする][失敗した要求トレースを有効にする]

2.  Web サイトが表示されているブラウザー ウィンドウのアドレス バーで、余分な文字を URL に追加し、Enter キーを押して 404 エラーを発生させます。

    これにより、失敗した要求トレース ログが作成されます。そのログを表示またはダウンロードする方法については、以降の手順で説明します。

3.  Visual Studio で、**[Azure の Web サイト]** ウィンドウの **[構成]** タブにある **[管理ポータルで開く]** をクリックします。

4.  管理ポータルの **[ダッシュボード]** をクリックし、**[概要]** セクションにある **[展開資格情報のリセット]** をクリックします。

    ![FTP の資格情報をリセットするためのリンク (ダッシュボード)][FTP の資格情報をリセットするためのリンク (ダッシュボード)]

5.  新しいユーザー名とパスワードを入力します。

    ![新しい FTP ユーザー名とパスワード][新しい FTP ユーザー名とパスワード]

6.  管理ポータルの **[ダッシュボード]** タブで F5 キーを押してページを最新の情報に更新し、下へスクロールして **[展開 / FTP ユーザー]** を表示します。ユーザー名に、プレフィックスとしてサイト名が付加されていることに注目してください。**ログインする際は、ここに表示されている完全なユーザー名とそのサイト名プレフィックスを使用する必要があります。**

7.  新しいブラウザー ウィンドウで、**[FTP ホスト名]** (Web サイトの管理ポータル ページの **[ダッシュボード]** タブ) に表示されている URL に移動します。**[FTP ホスト名]** は、**[概要]** セクションの **[展開 / FTP ユーザー]** の近くに表示されます。

8.  先ほど作成した FTP 資格情報を使用してログインします (ユーザー名のサイト名プレフィックスを含めること)。

    サイトのルート フォルダーがブラウザーに表示されます。

9.  *LogFiles* フォルダーを開きます。

    ![LogFiles フォルダーを開く][LogFiles フォルダーを開く]

10. W3SVC に数値の付いた名前のフォルダーを開きます。

    ![W3SVC フォルダーを開く][W3SVC フォルダーを開く]

    このフォルダーには、失敗した要求トレースを有効にした後で記録されたすべてのエラーの XML ファイルに加え、ブラウザーで XML の体裁を設定するための XSL ファイルが格納されています。

    ![W3SVC フォルダー][W3SVC フォルダー]

11. トレース情報を確認する失敗した要求の XML ファイルをクリックします。

    次の図は、サンプル エラーのトレース情報を部分的に示したものです。

    ![失敗した要求トレースをブラウザーで表示したところ][失敗した要求トレースをブラウザーで表示したところ]

## <a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ

Azure Web サイトで作成されたログは Visual Studio を使って簡単に参照できることが確認できました。Azure Web サイトのトラブルシューティングや、ASP.NET アプリケーションのトレース、Web サーバーのログの分析について、さらに理解を深めることができます。

### Azure Web サイトのトラブルシューティング

Azure Web サイト (WAWS) のトラブルシューティングの詳細については、以下のリソースを参照してください。

-   [Azure でのトラブルシューティング][Azure でのトラブルシューティング]<br/>
    WAWS について簡単に説明したセクションが含まれる入門者向けの基本的なホワイト ペーパーです。
-   [Web サイトのトラブルシューティング][Web サイトのトラブルシューティング]<br/>
    WAWS を重点的に説明しています。
-   [診断ログの有効化: Azure の Web サイト][診断ログの有効化: Azure の Web サイト]<br/>
    取り上げられている情報はこのチュートリアルとほぼ同じですが、Visual Studio を使わずに診断ログを入手する方法に重点が置かれています。
-   [Web サイトの監視方法][Web サイトの監視方法]<br/>
     トラブルシューティングのドキュメントには記載されていない有益な情報を、「[configure diagnostics and download logs (診断の構成とログのダウンロード)][configure diagnostics and download logs (診断の構成とログのダウンロード)]」セクションで取り上げています。
-   [Investigating Memory Leaks in Azure Web Sites with Visual Studio 2013 (Visual Studio 2013 を使用した Azure の Web サイトでのメモリ リークの調査)][Investigating Memory Leaks in Azure Web Sites with Visual Studio 2013 (Visual Studio 2013 を使用した Azure の Web サイトでのメモリ リークの調査)]。マネージされるメモリの問題の分析に役立つ Visual Studio の機能に関する Microsoft ALM のブログ記事
-   [Windows Azure Websites online tools you should know about (知っておくべき Windows Azure Web サイトのオンライン ツール)][Windows Azure Websites online tools you should know about (知っておくべき Windows Azure Web サイトのオンライン ツール)]。Amit Apple によるブログの投稿です。

具体的なトラブルシューティングについての質問は、次のいずれかのフォーラムで投稿してください。

-   [ASP.NET サイト上の Azure フォーラム][ASP.NET サイト上の Azure フォーラム]。
-   [MSDN 上の Azure フォーラム][MSDN 上の Azure フォーラム]。
-   [StackOverflow.com][StackOverflow.com]。

### Visual Studio でのデバッグ

Visual Studio のデバッグ モードの使い方については、MSDN のトピック「[Visual Studio でのデバッグ][Visual Studio でのデバッグ]」と [Visual Studio 2010 でのデバッグのヒントに関するページ][Visual Studio 2010 でのデバッグのヒントに関するページ]を参照してください。

Azure Web サイトのリモート デバッグの詳細については、以下のリソースを参照してください。

-   [Introduction to Remote Debugging on Azure Web Sites (Azure の Web サイトにおけるリモート デバッグの概要)][Introduction to Remote Debugging on Azure Web Sites (Azure の Web サイトにおけるリモート デバッグの概要)].
-   [Introduction to Remote Debugging Azure Web Sites part 2 - Inside Remote debugging (Azure の Web サイトにおけるリモート デバッグの概要 2 - リモート デバッグの内部処理)][Introduction to Remote Debugging Azure Web Sites part 2 - Inside Remote debugging (Azure の Web サイトにおけるリモート デバッグの概要 2 - リモート デバッグの内部処理)]
-   [Introduction to Remote Debugging on Azure Web Sites part 3 - Multi-Instance environment and GIT (Azure の Web サイトにおけるリモート デバッグの概要 3 - マルチインスタンス環境と GIT)][Introduction to Remote Debugging on Azure Web Sites part 3 - Multi-Instance environment and GIT (Azure の Web サイトにおけるリモート デバッグの概要 3 - マルチインスタンス環境と GIT)]

Web サイトで Azure Web API またはモバイル サービス バックエンドを使用し、デバッグを実行する必要がある場合は [Visual Studio での .NET バックエンドのデバッグに関するページ][Visual Studio での .NET バックエンドのデバッグに関するページ]を参照してください。

### ASP.NET アプリケーションでのトレース

ASP.NET トレースに関しては、最新かつ必要な情報をすべて網羅した解説がインターネットには存在しません。そのため、過去に作成された入門者向けの資料を参考にするのが最善の方法となります。MVC がまだ存在していなかったために Web フォームを想定して書かれていますが、具体的な問題については、最新のブログで情報を補うことができます。たとえば、以下のリソースが参考になります。

-   [監視と利用統計情報 (Azure での実際のクラウド アプリケーションのビルド) に関するページ][監視と利用統計情報 (Azure での実際のクラウド アプリケーションのビルド) に関するページ].<br/>
    Azure クラウド アプリケーションをトレースするためのベスト プラクティスを掲載した E-Book の章。
-   [ASP.NET トレース][ASP.NET トレース]<br/>
    最新とは言えませんが、基本的な事柄がわかりやすくまとめられています。
-   [トレース リスナー][トレース リスナー]<br/>
    トレース リスナーについて書かれていますが、[WebPageTraceListener][WebPageTraceListener] には触れていません。
-   [ASP.NET トレースと System.Diagnostics トレースの統合][ASP.NET トレースと System.Diagnostics トレースの統合]<br/>
    古い情報ですが、入門記事では扱っていないような詳しい情報が記載されています。
-   [ASP.NET MVC Razor ビューでのトレース][ASP.NET MVC Razor ビューでのトレース]<br/>
    Razor ビューでのトレースに加え、MVC アプリケーションでハンドルされない例外をすべてログに記録するためのエラー フィルターの作成方法についても説明されています。Web フォーム アプリケーションで、ハンドルされない例外をすべてログに記録する方法については、MSDN の「[エラー ハンドラーの完全なコード例][エラー ハンドラーの完全なコード例]」で紹介されている Global.asax サンプルを参照してください。MVC または Web フォームで、特定の例外をログに記録すると共に、既定のフレームワークの処理はそのまま活かしておく必要がある場合、例外を捕捉してから再スローする方法を利用できます。その例を次に示します。

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

-   [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報][Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報]
    このチュートリアルで Visual Studio を使って行ったことをコマンド ラインで行う方法が解説されています。[Glimpse][Glimpse] は、ASP.NET アプリケーションをデバッグするためのツールです。
-   [Azure の Web サイトのログと診断の使用に関するページ - David Ebbo 作成][Azure の Web サイトのログと診断の使用に関するページ - David Ebbo 作成]、および [Azure の Web サイトからのログのストリーミングに関するページ - David Ebbo 作成][Azure の Web サイトからのログのストリーミングに関するページ - David Ebbo 作成]
    Scott Hanselman と David Ebbo によるビデオ。

エラーをログに記録する方法としては、独自のトレース コードを記述する以外にも、[ELMAH][ELMAH] のようなオープン ソースのログ記録フレームワークを使う方法があります。詳細については、[Scott Hanselman が ELMAH についてまとめたブログ記事][Scott Hanselman が ELMAH についてまとめたブログ記事]を参照してください。

Azure からストリーミング ログを取得する必要がある場合、必ずしも ASP.NET や System.Diagnostics トレースを使う必要はありません。Azure Web サイトのストリーミング ログ サービスは、*LogFiles* フォルダーに見つかったすべての *.txt* ファイル、*.html* ファイル、*.log* ファイルをストリーミングします。したがって、Web サイトのファイル システムに書き込む独自のログ記録システムを作成することもできます。必要なファイルが自動的にストリーミングされ、ダウンロードされます。必要な作業は、*d:\\home\\logfiles* フォルダーにファイルを作成するアプリケーション コードを記述するだけです。

### Web サーバーのログの分析

Web サーバーのログの分析の詳細については、次のリソースを参照してください。

-   [LogParser][LogParser]<br/>
    Web サーバーのログ (*.log* ファイル) に記録されているデータを表示するためのツールです。
-   [Troubleshooting IIS Performance Issues or Application Errors using LogParser (IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする)][Troubleshooting IIS Performance Issues or Application Errors using LogParser (IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする)]<br/>
    Web サーバーのログを分析する際に活用できる Log Parser ツールについて基本的な事柄が説明されています。
-   [LogParser の使用に関して Robert McMurray が執筆したブログ記事][LogParser の使用に関して Robert McMurray が執筆したブログ記事]
-   [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP ステータス コード][IIS 7.0、IIS 7.5、IIS 8.0 における HTTP ステータス コード]

### 失敗した要求トレース ログの分析

失敗した要求トレース ログの活用方法については、Microsoft TechNet Web サイトの「[Using Failed Request Tracing (失敗した要求トレースの使用)][Using Failed Request Tracing (失敗した要求トレースの使用)]」セクションなどが参考になります。ただし、このドキュメントで扱う内容は、失敗した要求トレースを IIS で構成する作業が主体です。この作業を Azure Web サイトで行うことはできません。

### クラウド サービスのデバッグ

Web サイトではなく Azure クラウド サービスをデバッグする場合、[クラウド サービスのデバッグに関するページ][クラウド サービスのデバッグに関するページ]を参照してください。

  [デバッグ モード]: http://www.visualstudio.com/ja-jp/get-started/debug-your-app-vs.aspx
  [IntelliTrace]: http://msdn.microsoft.com/library/vstudio/dd264915.aspx
  [前提条件]: #prerequisites
  [サイトの構成と管理]: #sitemanagement
  [リモート ビュー]: #remoteview
  [リモート デバッグ]: #remotedebug
  [診断ログの概要]: #logsoverview
  [アプリケーションのトレース ログの作成と表示]: #apptracelogs
  [Web サーバーのログの表示]: #webserverlogs
  [詳細なエラー メッセージ ログの表示]: #detailederrorlogs
  [ファイル システムのログのダウンロード]: #downloadlogs
  [ストレージ ログの表示]: #storagelogs
  [失敗した要求ログの表示]: #failedrequestlogs
  [次のステップ]: #nextsteps
  [ASP.NET Web アプリケーションを Azure Web サイトに展開する]: /ja-jp/develop/net/tutorials/get-started/
  [アカウント、サブスクリプション、管理ロールの管理]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
  [サーバー エクスプローラーの 設定の表示]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png
  [Azure Web サイト ウィンドウ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png
  [Azure の Web サイト: アプリケーション文字列と接続文字列の動作に関するページ]: http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx
  [Web サイトの構成方法]: /ja-jp/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig
  [あまり役に立たないエラー ページ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png
  [1]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png
  [2]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png
  [Web.config の変換]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations
  [Web.config の編集]: ./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png
  [詳細なエラー メッセージ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png
  [デバッグ モードでの発行]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png
  [デバッガーの接続]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png
  [管理ポータルでのリモート デバッグの設定]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png
  [Azure で実行されているコード内の変数をデバッグ モードで表示]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png
  [Azure 料金計算ツール]: /ja-jp/pricing/calculator/
  [ステップ実行をマイ コードのみに制限する]: http://msdn.microsoft.com/ja-jp/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code
  [WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Visual Studio でのデバッグ]: http://msdn.microsoft.com/ja-jp/library/vstudio/sc65sadd.aspx
  [System.Diagnostics.Trace]: http://msdn.microsoft.com/ja-jp/library/system.diagnostics.trace.aspx
  [trace 要素]: http://msdn.microsoft.com/ja-jp/library/vstudio/6915t83k(v=vs.100).aspx
  [trace.axd]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png
  [3]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png
  [コンテキスト メニューからストリーミング ログを表示]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png
  [4]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png
  [出力ウィンドウのエラー トレース]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png
  [アプリケーション ログ記録がオフ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png
  [詳細レベルのトレース出力]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png
  [ログ タブのボタン]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png
  [Web サーバーのログ記録を有効にする]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png
  [監視する Azure ログの指定]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png
  [Web サーバーのログを監視する]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png
  [出力ウィンドウの Web サーバーのログ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png
  [詳細なエラー メッセージを有効にする]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png
  [すべてのログを監視する]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png
  [出力ウィンドウの詳細なエラー ログ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png
  [ブラウザー ウィンドウの詳細なエラー ログ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png
  [5]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png
  [ダウンロードされたファイル]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png
  [6]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png
  [Log Parser]: http://www.microsoft.com/ja-jp/download/details.aspx?displaylang=en&id=24659
  [ストレージ アカウントの作成]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png
  [URL を入力する]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png
  [7]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png
  [テーブル ストレージの管理]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png
  [ストレージ ログ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png
  [サーバー エクスプローラーのトレース テーブル]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png
  [テーブル形式で表示されたストレージ ログ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png
  [サーバー エクスプローラーを使用したストレージ リソースの参照]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff683677.aspx
  [8]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png
  [失敗した要求トレースを有効にする]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png
  [新しい FTP ユーザー名とパスワード]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png
  [LogFiles フォルダーを開く]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png
  [W3SVC フォルダーを開く]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png
  [W3SVC フォルダー]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png
  [失敗した要求トレースをブラウザーで表示したところ]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png
  [Azure でのトラブルシューティング]: /ja-jp/develop/net/best-practices/troubleshooting/
  [Web サイトのトラブルシューティング]: /ja-jp/develop/net/best-practices/troubleshooting-web-sites/
  [診断ログの有効化: Azure の Web サイト]: /ja-jp/develop/net/common-tasks/diagnostics-logging-and-instrumentation/
  [Web サイトの監視方法]: /ja-jp/manage/services/web-sites/how-to-monitor-websites/
  [ASP.NET サイト上の Azure フォーラム]: http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET
  [MSDN 上の Azure フォーラム]: http://social.msdn.microsoft.com/Forums/windowsazure/
  [StackOverflow.com]: http://www.stackoverflow.com
  [Visual Studio 2010 でのデバッグのヒントに関するページ]: http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx
  [Visual Studio での .NET バックエンドのデバッグに関するページ]: http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx
  [ASP.NET トレース]: http://msdn.microsoft.com/ja-jp/library/ms972204.aspx
  [トレース リスナー]: http://msdn.microsoft.com/ja-jp/library/4y5y10s7.aspx
  [WebPageTraceListener]: http://msdn.microsoft.com/ja-jp/library/system.web.webpagetracelistener.aspx
  [ASP.NET トレースと System.Diagnostics トレースの統合]: http://msdn.microsoft.com/ja-jp/library/b0ectfxd.aspx
  [ASP.NET MVC Razor ビューでのトレース]: http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx
  [エラー ハンドラーの完全なコード例]: http://msdn.microsoft.com/ja-jp/library/bb397417.aspx
  [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報]: http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx
  [Glimpse]: http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx
  [Azure の Web サイトのログと診断の使用に関するページ - David Ebbo 作成]: http://www.windowsazure.com/ja-jp/documentation/videos/azure-web-site-logging-and-diagnostics/
  [Azure の Web サイトからのログのストリーミングに関するページ - David Ebbo 作成]: http://www.windowsazure.com/ja-jp/documentation/videos/log-streaming-with-azure-web-sites/
  [ELMAH]: http://nuget.org/packages/elmah/
  [Scott Hanselman が ELMAH についてまとめたブログ記事]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx
  [LogParser]: http://www.microsoft.com/ja-jp/download/details.aspx?id=24659
  [LogParser の使用に関して Robert McMurray が執筆したブログ記事]: http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/
  [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP ステータス コード]: http://support.microsoft.com/kb/943891
  [クラウド サービスのデバッグに関するページ]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/windowsazure/ee405479.aspx
