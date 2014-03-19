<properties title="Visual Studio での Windows Azure の Web サイトのトラブルシューティング" pageTitle="Visual Studio での Windows Azure の Web サイトのトラブルシューティング" metaKeywords="トラブルシューティング デバッグ Azure Web サイト トレース ログ" description="Visual Studio 2013 に組み込まれているリモート デバッグ、トレース、ログの各ツールを使用した Windows Azure の Web サイトのトラブルシューティングの方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="Web Sites" authors="tdykstra"  solutions="" />


# Visual Studio での Windows Azure の Web サイトのトラブルシューティング

Web アプリケーションの開発やテストにおけるトラブルシューティングは、[デバッグ モード](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx)または [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) を使用して実行できます。デバッグ モードは、IIS Express を使ってローカルで実行するか、Windows Azure の Web サイトを使ってリモートで実行することができます。ただし、運用環境でのみ発生するエラーについては、アプリケーション コードまたは Web サーバーによって作成されるログを参照してデバッグするのが最善の方法となります。このチュートリアルでは、Visual Studio のツールを活用し、Windows Azure の Web サイトで実行されているアプリケーションをデバッグ モードでリモートから実行するか、アプリケーションまたは Web サーバーのログを参照することによってデバッグする方法を説明します。

学習内容: 

* Visual Studio から利用できる Windows Azure のサイト管理機能。
* Windows Azure の Web サイトでプロジェクトが実行されているときに、リモートからデバッグ モードを実行する方法。
* アプリケーションのトレース ログを作成する方法と、ログが作成されている最中にそれらを確認する方法。
* Web サーバーのログ (詳細なエラー メッセージ、失敗した要求トレースを含む) を確認する方法。
* Windows Azure のストレージ アカウントに診断ログを送り、そこでログを確認する方法。


### チュートリアル セグメント

1. [前提条件](#prerequisites)
2. [サイトの構成と管理](#sitemanagement)
5. [リモート デバッグ](#remotedebug)
3. [診断ログの概要](#logsoverview)
4. [アプリケーションのトレース ログの作成と表示](#apptracelogs)
5. [Web サーバーのログの表示](#webserverlogs)
5. [詳細なエラー メッセージ ログの表示](#detailederrorlogs)
5. [ファイル システムのログのダウンロード](#downloadlogs)
5. [ストレージ ログの表示](#storagelogs)
5. [失敗した要求ログの表示](#failedrequestlogs)
5. [次の手順](#nextsteps)

<h2><a name="prerequisites"></a><span class="short-header">前提条件</span></h2>

このチュートリアルでは、「[ASP.NET Web アプリケーションを Windows Azure Web サイトに展開する][GetStarted]」で設定した開発環境、Web プロジェクト、および Windows Azure の Web サイトを使用します。

リモート デバッグを実行するには、Visual Studio 2013 または Visual Studio 2012 が必要です。このチュートリアルで紹介するその他の機能については、Visual Studio 2013 Express for Web や Visual Studio 2012 Express for Web でも動作します。

ストリーミング ログ機能は、.NET Framework 4 以降を対象とするアプリケーションでのみ動作します。

<h2><a name="sitemanagement"></a><span class="short-header">サイト管理</span>サイトの構成と管理</h2>

Visual Studio は、管理ポータルで利用できるサイト管理機能や構成設定に一部アクセスできるようになっています。このセクションでは、その対象となる機能や設定について取り上げます。

1. 次のいずれかの方法で、Visual Studio から Windows Azure アカウントへの接続を有効にします。


	* 資格情報を入力して Windows Azure アカウントにサインインします。

		これはすばやく簡単に実行できる方法ですが、この方法を使用した場合、**[サーバー エクスプローラー]** ウィンドウで Windows Azure SQL データベースやモバイル サービスを確認することができません。

		**サーバー エクスプローラー**で、**[Windows Azure への接続]** をクリックします。または、**Windows Azure** ノードを右クリックし、コンテキスト メニューの **[Windows Azure への接続]** をクリックします。

	* アカウントへのアクセスを可能にする管理証明書をインストールします。

		**サーバー エクスプローラー**で、**Windows Azure** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理]** をクリックします。**[Windows Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブをクリックし、**[インポート]** をクリックします。操作手順に従い、Windows Azure アカウント用のサブスクリプション ファイル (*.publishsettings* ファイル) をダウンロードしてインポートします。

		> [WACOM.NOTE]
		> サブスクリプション ファイルをソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) にダウンロードし、インポートが完了したらそのファイルを削除します。これは、悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Windows Azure サービスを編集、作成、削除できるためです。

	詳細については、「[アカウント、サブスクリプション、管理者ロールを管理する](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)」を参照してください。

2. **サーバー エクスプローラー**で **[Windows Azure]** を展開し、**[Web サイト]** を展開します。

3. 「[ASP.NET Web アプリケーションを Windows Azure Web サイトに展開する][GetStarted]」で作成した Web サイトのノードを右クリックし、**[設定の表示]** をクリックします。

	![サーバー エクスプローラーの [設定の表示]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	**[Azure の Web サイト]** タブが表示され、Visual Studio から利用できるサイト管理タスクや構成タスクが確認できます。

	![[Azure の Web サイト] ウィンドウ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	このチュートリアルでは、ログとトレースのドロップダウンを使用します。
   
	このウィンドウの [アプリケーション設定] ボックスと [接続文字列] ボックスについては、「[Windows Azure Web Sites: How Application Strings and Connection Strings Work (Windows Azure の Web サイト: アプリケーション文字列と接続文字列の動作)](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx)」を参照してください。

	このウィンドウでは実行できないサイト管理タスクを行う場合は、**[すべての Web サイト設定]** をクリックすると、ブラウザー ウィンドウが開いて管理ポータルにアクセスできます。詳細については、「[How to Configure Web Sites (Web サイトの構成方法)](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)」を参照してください。

<h2><a name="remotedebug"></a><span class="short-header">リモート デバッグ</span>リモート デバッグ</h2>

アプリケーションを IIS Express 下でローカルに実行したときは正しく動作するにもかかわらず、Windows Azure の Web サイトに展開すると問題が生じる場合があります。この場合の対処としては、リモートからデバッグ モードで実行する方法が考えられます。

このセクションでは、まずローカルでデバッグを行った後、リモートからデバッグを行う方法について見ていきます。

<div class="dev-callout"><strong>注</strong><p>リモート デバッグはプレビュー機能です。</p></div>

### ローカル デバッグ

1. 「[ASP.NET Web アプリケーションを Windows Azure Web サイトに展開する][GetStarted]」で作成した Web プロジェクトを開きます。

1. *Controllers\HomeController.cs* を開きます。

2. 'About()' メソッドを削除し、その位置に次のコードを挿入します。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. 'About' メソッドで、'ViewBag.Message' を設定する行を右クリックし、コンテキスト メニューから **[ブレークポイント]**、**[ブレークポイントの挿入]** の順にクリックします。(カーソルをコード行に置いた状態で F9 キーを押すことでブレークポイントを設定することもできます。)

	![ブレークポイントの設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-setbreakpoint.png)

3. F5 キーを押してデバッグ モードで実行し、メニュー バーの **[About]** リンクをクリックします (表示されない場合は、ページの右上隅にある記号をクリックしてメニューを展開してください)。

	'ViewBag.Message' の設定を行うステートメントで Visual Studio が停止します。この状態で、変数の値の検査や変更、コードのステップ実行 (1 行ずつ実行)、ステートメントの実行順序の変更などを行うことができます。

4. 'currentTime' にマウス ポインターを合わせて、変数の値を表示します。

	![デバッグ モードでの変数の表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugview.png)

5. 表示された時刻値をクリックして値を ("Changed in debug mode" などに) 変更し、Enter キーを押します。

	![デバッグ モードでの変数の変更](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchange.png)

6. F5 キーを押してアプリケーションの実行を継続します。

	先ほど時刻値に対して入力した新しい値が [About] ページに表示されます。

	![[About] ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-afterdebugchange.png)

7. ブラウザーを閉じます。

### リモート デバッグ

1. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、**[発行]** をクリックします。

2. **[プロファイル]** ボックスの一覧から、「[ASP.NET Web アプリケーションを Windows Azure Web サイトに展開する][GetStarted]」で使用したものと同じプロファイルを選択します。

3. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

	![デバッグ モードでの発行](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. 展開が完了し、ブラウザーが起動してサイトの Windows Azure URL が表示されたら、ブラウザーを閉じます。

5. 先ほど設定したブレークポイントがそのままになっていることを確認します。

5. Visual Studio 2013 のみ: **サーバー エクスプローラー** で **[Windows Azure]**、**[Web サイト]** の順に展開し、Web サイトを右クリックして **[デバッガーの接続]** をクリックします。

	![デバッガーの接続](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	ブラウザーが自動的に起動し、Windows Azure で実行されたホーム ページが表示されます。デバッグに必要な設定を Windows Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

6. Visual Studio 2012 のみ: 

	* Windows Azure の管理ポータルで、Web サイトの **[構成]** タブに移動し、下へスクロールして **[サイト診断]** セクションを表示します。

	* **[リモート デバッグ]** を **[オン]** に設定し、**リモート デバッグに使用する Visual Studio のバージョン**を **[2012]** に設定します。

	![管理ポータルでのリモート デバッグの設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Visual Studio の **[デバッグ]** メニューの **[プロセスにアタッチ]** をクリックします。

	* **[修飾子]** ボックスに Web サイトの URL を入力します。プレフィックス 'http://' は付けずに入力してください。

	* **[すべてのユーザーからのプロセスを表示する]** を選択します。

	* 資格情報を求められたら、Web サイトを発行する権限のあるユーザー名とパスワードを入力します。資格情報を入手するには、管理ポータルで Web サイトの [ダッシュボード] タブに移動し、**[発行プロファイルのダウンロード]** をクリックします。そのファイルをテキスト エディターで開くと、1 つ目の **userName=** と **userPWD=** の後に、ユーザー名とパスワードが表示されます。

	* **[選択可能なプロセス]** テーブルにプロセスが表示されたら、**[w3wp.exe]** を選択し、**[アタッチ]** をクリックします。

	* ブラウザーを起動して、目的のサイトの URL に移動します。

	デバッグに必要な設定を Windows Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

6. メニューの **[About]** を再度クリックします。

	先ほどと同様、Visual Studio がブレークポイントで停止します。ただし、プロジェクト コードが実行されている場所は、ローカル コンピューターではなく Windows Azure 上です。

7. 'currentTime' 変数にマウスを合わせて、時刻値を表示します。

	![Windows Azure で実行されているコード内の変数をデバッグ モードで表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	今回表示される時刻は、Windows Azure サーバーの時刻です。ローカル コンピューターとはタイム ゾーンが異なります。

8. 'currentTime' 変数に新しい値 ("Now running in Windows Azure" など) を入力します。

5. F5 キーを押して実行を継続します。

	Windows Azure で実行中の [About] ページに、先ほど currentTime 変数に対して入力した新しい値が表示されます。

	![[About] ページ (値の変更後)](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### メモ 

* リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。数分以上停止しているプロセスは、応答していないプロセスと見なされ、Windows Azure によりシャットダウンされます。

* デバッグ中は、サーバーから Visual Studio にデータが送信されるため、帯域幅の使用料に影響が及ぶ可能性があります。帯域幅使用料については、[Windows Azure 料金計算ツール](/en-us/pricing/calculator/)を参照してください。

* *Web.config* ファイルの 'compilation' 要素の 'debug' 属性が true に設定されていることを確認します。デバッグ ビルド構成で発行するときは、true が既定値です。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* デバッグ対象となるコードにデバッガーがステップ インしない場合、[マイ コードのみ] の設定を変更してみてください。詳細については、「[ステップ実行をマイ コードのみに制限する](http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)」を参照してください。


### 運用環境でのデバッグ

運用環境におけるデバッグ モードの実行はお勧めできません。運用環境のサイトが複数のサーバー インスタンスにスケール アウトされていなければ、デバッグのトラフィックは自分の Web サーバー インスタンスにのみ関連付けられます。しかし、Web サーバーのインスタンスが複数存在する場合、デバッガーのアタッチ先となるインスタンスは無作為に決定されるため、そのインスタンスにブラウザーの要求を確実に渡すことができません。また、運用環境にデバッグ ビルドを展開することも一般的ではありません。リリース ビルドに対してはコンパイラが最適化を行うため、ソース コードの状況を行レベルで把握することは不可能です。運用環境の問題をトラブルシューティングするのに最も適しているリソースは、アプリケーション トレースと Web サーバーのログです。

<h2><a name="logsoverview"></a><span class="short-header">ログの概要</span>診断ログの概要</h2>

Windows Azure の Web サイトで動作する ASP.NET アプリケーションでは、次の種類のログが作成されます。

* **アプリケーション トレース ログ**<br/>
  アプリケーションが [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx) クラスのメソッドを呼び出すことによって作成されます。
* **Web サーバーのログ**<br/>
  Web サーバーは、サイトに届くすべての HTTP 要求について、それぞれログ エントリを作成します。
* **詳細なエラー メッセージ ログ**<br/>
  失敗した HTTP 要求 (ステータス コード 400 以上の要求) について、より詳しい情報を記した HTML ページが Web サーバーによって作成されます。
* **失敗した要求トレース ログ**<br/>
  失敗した HTTP 要求についての詳しいトレース情報を記録した XML ファイルが Web サーバーによって作成されます。また、ブラウザーで XML の体裁を設定するための XSL ファイルも作成されます。
  
ログ出力はサイトのパフォーマンスに影響を及ぼすため、Windows Azure では、ログの種類ごとにその有効と無効を切り替えることができるようになっています。アプリケーション ログについては、特定の重大度レベルを超えるログだけを記録するように指定できます。新しい Web サイトを作成した時点ではすべてのログが既定で無効になります。

ログは、Web サイトのファイル システムにある *LogFiles* フォルダー内のファイルに出力されます。Web サーバーのログとアプリケーションのログは、Windows Azure のストレージ アカウントに出力することもできます。ストレージ アカウントには、ファイル システムよりも大量のログを保持することができます。ファイル システムを使用した場合、保存できるログの上限は 100 MB です。(上限に達すると、古いログ ファイルは削除され、新しいログ ファイルのための領域が確保されます。)

<h2><a name="apptracelogs"></a><span class="short-header">アプリケーション ログ</span>アプリケーション トレース ログの作成と表示</h2>

このセクションでは、次のタスクを実行します。

* [以前のチュートリアル][GetStarted]で作成した Web プロジェクトにトレース ステートメントを追加します。
* プロジェクトをローカル実行したときのログを確認します。
* Windows Azure で実行中のアプリケーションによって生成されたログを確認します。

### アプリケーションへのトレース ステートメントの追加

1. 'System.Diagnostics' の 'Trace' ステートメントと 'using' ステートメントを追加するために、*Controllers\HomeController.cs* ファイルを開いて、その内容を次のコードで置き換えます。

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
		
				
### ローカルでのトレース出力の確認

3. F5 キーを押してデバッグ モードでアプリケーションを実行します。

	既定のトレース リスナーは、すべてのトレース出力を他のデバッグ出力と一緒に**出力**ウィンドウに書き込みます。次の図は、'Index' メソッドに追加したトレース ステートメントからの出力結果を示したものです。

	![[デバッグ] ウィンドウでのトレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	以降の手順では、コンパイルせずにデバッグ モードで、トレース出力を Web ページに表示する方法を紹介します。

2. プロジェクト フォルダーにあるアプリケーションの Web.config ファイルを開き、ファイル末尾の終了 '</configuration>' 要素の直前に '<system.diagnostics>' 要素を追加します。

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

	'WebPageTraceListener' を使用すると、ブラウザーから '/trace.axd' にアクセスすることでトレース出力を表示できます。

3. Web.config ファイルの '<system.web>' に、次のような <a href="http://msdn.microsoft.com/en-us/library/vstudio/6915t83k(v=vs.100).aspx">trace 要素</a>を追加します。

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

4. ブラウザー ウィンドウのアドレス バーで、URL に続けて「*trace.axd*」を入力し、Enter キーを押します (例: http://localhost:53370/trace.axd)。

5. **[アプリケーション トレース]** ページの **[詳細の表示]** をクリックします。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	**[要求の詳細]** ページが表示され、'Index' メソッドに追加されたトレース ステートメントからの出力が **[トレース情報]** セクションに表示されます。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	既定では、'trace.axd' の使用はローカルに限られます。*Web.config* ファイルで 'trace' 要素に 'localOnly="false"' を追加すると、リモート サイトからも利用できるようになります。その例を次に示します。

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	ただしセキュリティ上の理由から、運用サイトで 'trace.axd' を有効にすることは、通常お勧めできません。もっと簡単に Windows Azure の Web サイトのトレース ログを確認する方法を以降のセクションで見ていきます。

### Windows Azure でのトレース出力の確認

1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[発行]** をクリックします。

2. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

	更新したプロジェクトを発行すると、ブラウザー ウィンドウが起動して自分のホーム ページが表示されます (ただし、**[接続]** タブの **[宛先 URL]** をクリアした場合を除く)。

3. **サーバー エクスプローラー**で Web サイトを右クリックし、**[ストリーミング ログを出力ウィンドウで表示]** を選択します。

	![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	ログ ストリーミング サービスに接続されたことを示すメッセージが**出力**ウィンドウに表示されます。ログが出力されないまま 1 分が経過すると、その都度、1 行の通知が追加されます。

	![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. アプリケーションのホーム ページが表示されているブラウザー ウィンドウで、**[Contact]** をクリックします。

	数秒すると、'Contact' メソッドに追加したエラー レベルのトレースが**出力**ウィンドウに表示されます。

	![出力ウィンドウのエラー トレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio にエラー レベルのトレースしか表示されていないのは、それが、ログ監視サービスを有効にしたときの既定の設定であるためです。先ほどサイトの設定ページを開いたときに見たように、新しい Windows Azure の Web サイトを作成したときは、すべてのログが既定で無効となります。

	![アプリケーション ログ記録がオフ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	ただし、**[ストリーミング ログを出力ウィンドウで表示]** を選択すると、**[アプリケーション ログ記録 (ファイル システム)]** が自動的に **[エラー]** に変更されます。これは、エラー レベルのログが報告されることを意味します。すべてのトレース ログを表示するためには、この設定を **[詳細]** に変更する必要があります。"エラー" よりも低い重大度レベルを選択すると、その重大度を超えるログがすべて報告されます。つまり [詳細] を選択した場合は、"情報"、"警告"、"エラー" に該当するログが表示されます。

4. 先ほどと同じように、**サーバー エクスプローラー**で Web サイトを右クリックし、**[設定の表示]** をクリックします。

5. **[アプリケーション ログ記録 (ファイル システム)]** を **[詳細]** に変更し、**[保存]** をクリックします。
 
	![トレース レベルを [詳細] に設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. **[Contact]** ページが表示されているブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	数秒すると、**出力**ウィンドウにすべてのトレース出力が表示されます。

	![詳細レベルのトレース出力](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	このセクションでは、ログの有効と無効の切り替えを Windows Azure の Web サイトの設定で行いました。トレース リスナーの有効と無効は、Web.config ファイルで設定することもできます。ただし、Web.config ファイルに変更を加えると、アプリケーション ドメインの再利用処理が実行されます。一方、Web サイトを介してログを有効にした場合は、そのようにはなりません。問題の再現に時間がかかる場合や、発生のタイミングが散発的である場合、アプリケーション ドメインの再利用処理で問題が "解消" し、再発までしばらく待たなければならなくなります。Windows Azure で診断を有効にすると、そのようなことにはならず、すぐにエラー情報を収集することができます。

### 出力ウィンドウの機能

**出力**ウィンドウの **[Windows Azure ログ]** タブには、いくつかのボタンと 1 つのテキスト ボックスが表示されます。

![[ログ] タブのボタン](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

それぞれの機能を次に示します。

* **出力**ウィンドウをクリアする。
* 右端での折り返しを有効または無効にする。
* ログの監視を開始または停止する。
* 監視するログを指定する。
* ログをダウンロードする。
* 検索文字列または正規表現によるフィルターをログに適用する。
* **出力**ウィンドウを閉じる。

検索文字列または正規表現を入力した場合、ログ情報は、Visual Studio によってクライアント側でフィルタリングされます。つまり、フィルターの条件は、**出力**ウィンドウにログが表示された後に入力できます。フィルターの条件を変更するためにログを生成し直す必要はありません。

<h2><a name="webserverlogs"></a><span class="short-header">Web サーバーのログ</span>Web サーバーのログの表示</h2>

Web サーバーのログには、サイト上の HTTP アクティビティがすべて記録されます。それらの HTTP アクティビティを**出力**ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた Azure の Web サイトの **[構成]** タブで [Web サーバーのログ記録] を **[オン]** に変更し、**[保存]** をクリックします。

	![Web サーバーのログ記録を有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. **出力**ウィンドウの **[監視する Windows Azure ログの指定]** をクリックします。
	
	![監視する Windows Azure ログの指定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. **[Windows Azure ログ オプション]** ダイアログ ボックスの **[Web サーバーのログ]** を選択し、**[OK]** をクリックします。

	![Web サーバーのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	通常はアプリケーションのログが先に表示され、続けて Web サーバーのログが表示されます。ログが表示されるまでにしばらく時間がかかる場合があります。

	![出力ウィンドウの Web サーバーのログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Visual Studio で Web サーバーのログを初めて有効にしたとき、既定では、Windows Azure によってファイル システムにログが書き込まれます。代わりに管理ポータルを使用して、Web サーバーのログの書き込み先として、ストレージ アカウントの BLOB コンテナーを指定することもできます。詳細については、「[How to Configure Web Sites (Web サイトの構成方法)](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)」の**サイトの診断**に関するセクションを参照してください。

Web サーバーのログを Windows Azure のストレージ アカウントに記録するように管理ポータルで指定した後、Visual Studio でログ記録を無効にした場合、Visual Studio で再度ログ記録を有効にすると、ストレージ アカウントの設定が復元されます。

<h2><a name="detailederrorlogs"></a><span class="short-header">エラー ログ</span>詳細なエラー メッセージ ログの表示</h2>

詳細なエラー ログでは、エラー応答コード (400 以上) が返された HTTP 要求について、いくつかの詳しい情報が確認できます。それらの HTTP アクティビティを**出力**ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた Azure の Web サイトの **[構成]** タブで **[詳細なエラー メッセージ]** を **[オン]** に変更し、**[保存]** をクリックします。

	![詳細なエラー メッセージを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. **出力**ウィンドウの **[監視する Windows Azure ログの指定]** をクリックします。

3. **[Windows Azure ログ オプション]** ダイアログ ボックスの **[すべてのログ]** をクリックし、**[OK]** をクリックします。

	![すべてのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. ブラウザー ウィンドウのアドレス バーで、404 エラーの原因となるような余分な文字を URL に追加し (例: 'http://localhost:53370/Home/Contactx')、Enter キーを押します。

	数秒後、Visual Studio の**出力**ウィンドウに詳細なエラー ログが表示されます。ログの出力は長大であり、次の画像に表示されているのはその一部のみです。

	![出力ウィンドウの詳細なエラー ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	ログ出力の HTML をコピーしてブラウザーで開いた場合、次のページが表示されます。

	![ブラウザー ウィンドウの詳細なエラー ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a><span class="short-header">ログのダウンロード</span>ファイル システムのログのダウンロード</h2>

**出力**ウィンドウで監視できるすべてのログは *.zip* ファイルとしてダウンロードすることもできます。

1. **出力**ウィンドウの **[ストリーミング ログのダウンロード]** をクリックします。

	![[ログ] タブのボタン](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	エクスプローラーが起動して *[ダウンロード]* フォルダーが開き、ダウンロード済みのファイルが選択状態で表示されます。

	![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. この *.zip* ファイルを展開すると、次のフォルダー構造が確認できます。

	![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* アプリケーション トレース ログは、*LogFiles\Application* フォルダーの *.txt* ファイルに記録されます。
	* Web サーバーのログは *LogFiles\http\RawLogs* フォルダーの *.log* ファイルに記録されます。これらのファイルの閲覧と操作は、[Log Parser](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659) などのツールを使って行うことができます。
	* 詳細なエラー メッセージのログは、*LogFiles\DetailedErrors* フォルダーの *.html* ファイルに記録されます。

	(*deployments* フォルダーは、ソース管理の発行によって作成されたファイルに使用されます。Visual Studio の発行に関連したファイルは保存されません。*Git* フォルダーは、ログ ファイル ストリーミング サービスやソース管理の発行に関連したトレースに使用されます。)

<h2><a name="storagelogs"></a><span class="short-header">ストレージ ログ</span>ストレージ ログの表示</h2>

アプリケーション トレース ログを Windows Azure のストレージ アカウントに送って、Visual Studio で表示することもできます。ストレージ アカウントを作成して管理ポータルでストレージ ログを有効にし、**[Azure の Web サイト]** ウィンドウの **[ログ]** タブでそれらを表示します。

ファイル システムとストレージ アカウントの両方にログを送り、それぞれ異なる重大度レベルを指定することができます。現在ファイル システムのログは詳細レベルに設定されているので、これから、ストレージ ログを情報レベルに設定することにします。情報レベルを指定した場合、'Trace.TraceInformation'、'Trace.TraceWarning'、'Trace.TraceError' の呼び出しで作成されたすべてのログが対象となり、'Trace.WriteLine' で作成されたログは除外されます。

アプリケーション トレース ログをストレージに送る利点の 1 つは、ファイル システムのログでは得ることのできない詳しい情報をそれぞれのログと共に入手できることです。

1. **サーバー エクスプローラー**で Web サイトを右クリックし、**[管理ポータルで開く]** をクリックします。

2. 管理ポータルで **[ストレージ]** タブをクリックし、**[ストレージ アカウントの作成]** をクリックします。

	![ストレージ アカウントの作成](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png)

2. ストレージ アカウントに使用する一意の URL を入力し、**[ストレージ アカウントの作成]** をクリックします。

	![URL を入力する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png)

1. Visual Studio の **[Azure の Web サイト]** ウィンドウで、**[ログ]** タブをクリックし、**[ログの構成]** をクリックします。

	![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	自分の Web サイトの管理ポータルの **[構成]** タブが開きます。この画面には、**[Web サイト]** タブをクリックして該当する Web サイトをクリックし、**[構成]** タブをクリックすることでアクセスすることもできます。

2. 管理ポータルの **[構成]** タブで、下へスクロールしてアプリケーションの診断セクションを表示し、**[アプリケーション ログ記録 (ストレージ)]** を **[オン]** に変更します。

3. **[ログ レベル]** を **[情報]** に変更します。

4. **[接続の管理]** をクリックします。

	![[接続の管理] をクリックする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	ストレージ アカウントが複数ある場合は、**[診断ストレージの管理]** ボックスで目的のアカウントを選択できます。**[ストレージ アカウント キー]** フィールドには既定値として、選択されたストレージ アカウントのプライマリ キーの値が設定されます。

	![[接続の管理] をクリックする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. **[診断ストレージの管理]** ボックスのチェック マークをクリックしてこのボックスを閉じます。

6. 管理ポータルの **[構成]** タブで、**[保存]** をクリックします。

7. アプリケーションの Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	これらの Web ページを閲覧することによって生成されたログ情報がストレージ アカウントに書き込まれます。

8. Visual Studio の **[Azure の Web サイト]** ウィンドウの **[ログ]** タブで、**[診断の概要]** の下に表示される **[最新の情報に更新]** をクリックします。

	![[最新の情報に更新] をクリックする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	既定では、直近 15 分間のログが **[診断の概要]** セクションに表示されます。この時間を変更することで、表示するログの量を増やすことができます。

	(WAWSAppLogTable の "テーブルが見つからない" という内容のエラーが表示された場合は、**[アプリケーション ログ記録 (ストレージ)]** を有効にした後と、**[保存]** をクリックした後に、きちんとトレース対象のページをブラウズしたかどうかを確認してください。)

	![ストレージ ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	この画面に **[プロセス ID]** と **[スレッド ID]** がログごとに表示されていることに注目してください。ファイル システムのログでは確認できない情報です。Windows Azure のストレージ テーブルを直接参照することで、さらに別のフィールドを確認できます。

8. **[すべてのアプリケーション ログの表示]** をクリックします。

	Windows Azure のストレージ テーブル ビューアーにトレース ログ テーブルが表示されます。
   
	("シーケンスに要素が含まれていません" というエラーが表示された場合は、**サーバー エクスプローラー**を開き、**Windows Azure** ノードで、自分のストレージ アカウントに該当するノードを展開し、**[テーブル]** を右クリックして **[最新の情報に更新]** をクリックします。)

	![サーバー エクスプローラーのトレース テーブル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![テーブル形式で表示されたストレージ ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	この画面には、他の画面では確認できないフィールドが表示されます。専用のクエリ ビルダー UI を使用してクエリを作成し、ログにフィルターを適用することもできます。詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照
](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx)」の「テーブル リソースの操作」にある「エンティティのフィルター処理」を参照してください。

7. 単一行の詳細情報を表示するには、いずれかの行を右クリックし、**[編集]** をクリックします。

	![サーバー エクスプローラーのトレース テーブル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a><span class="short-header">失敗した要求ログ</span>失敗した要求トレース ログの表示</h2>

失敗した要求トレース ログは、URL の書き換えや認証の問題など、IIS による HTTP 要求の処理を詳しく把握する必要がある状況で活用できます。

Windows Azure の Web サイトでは、同じ失敗した要求トレース機能が使用されています。この機能は、IIS 7.0 以降で利用できます。ただし、ログに記録するエラーを指定するための IIS 設定にアクセスする必要はありません。失敗した要求トレースを有効にすると、すべてのエラーがキャプチャされます。

失敗した要求トレースは Visual Studio を使用して有効にできますが、それらを Visual Studio で表示することはできません。これらのログは XML ファイル形式になっています。ストリーミング ログ サービスで監視されるのは、プレーンテキスト モードでの読み取りが可能と判断されたファイル (*.txt*、*.html*、*.log* の各ファイル) だけです。

失敗した要求トレース ログは、ブラウザーから FTP で直接表示できるほか、FTP ツールを使ってローカル コンピューターにダウンロードした後、ローカルで表示することもできます。このセクションでは、ブラウザーで直接閲覧する方法を説明します。

1. **サーバー エクスプローラー**から開いた **[Azure の Web サイト]** ウィンドウの **[構成]** タブで、**[失敗した要求トレース]** を **[オン]** に変更し、**[保存]** をクリックします。

	![失敗した要求トレースを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Web サイトが表示されているブラウザー ウィンドウのアドレス バーで、余分な文字を URL に追加し、Enter キーを押して 404 エラーを発生させます。

	これにより、失敗した要求トレース ログが作成されます。そのログを表示またはダウンロードする方法については、以降の手順で説明します。

2. Visual Studio で、**[Azure の Web サイト]** ウィンドウの **[構成]** タブにある **[管理ポータルで開く]** をクリックします。

3. 管理ポータルの **[ダッシュボード]** をクリックし、**[概要]** セクションにある **[展開資格情報のリセット]** をクリックします。

	![FTP の資格情報をリセットするためのリンク (ダッシュボード)](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. 新しいユーザー名とパスワードを入力します。

	![新しい FTP ユーザー名とパスワード](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. 管理ポータルの **[ダッシュボード]** タブで F5 キーを押してページを最新の情報に更新し、下へスクロールして **[展開 / FTP ユーザー]** を表示します。ユーザー名に、プレフィックスとしてサイト名が付加されていることに注目してください。**ログインする際は、ここに表示されている完全なユーザー名とそのサイト名プレフィックスを使用する必要があります。**

5. 新しいブラウザー ウィンドウで、**[FTP ホスト名]** (Web サイトの管理ポータル ページの **[ダッシュボード]** タブ) に表示されている URL に移動します。**[FTP ホスト名]** は、**[概要]** セクションの **[展開 / FTP ユーザー]** の近くに表示されます。

6. 先ほど作成した FTP 資格情報を使用してログインします (ユーザー名のサイト名プレフィックスを含めること)。

	サイトのルート フォルダーがブラウザーに表示されます。

6. *LogFiles* フォルダーを開きます。

	![LogFiles フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. W3SVC に数値の付いた名前のフォルダーを開きます。

	![W3SVC フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	このフォルダーには、失敗した要求トレースを有効にした後で記録されたすべてのエラーの XML ファイルに加え、ブラウザーで XML の体裁を設定するための XSL ファイルが格納されています。

	![W3SVC フォルダー](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. トレース情報を確認する失敗した要求の XML ファイルをクリックします。

	次の図は、サンプル エラーのトレース情報を部分的に示したものです。

	![失敗した要求トレースをブラウザーで表示したところ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順</h2>

Windows Azure の Web サイトで作成されたログは Visual Studio を使って簡単に確認できることがおわかりいただけたと思います。Windows Azure の Web サイトのトラブルシューティングや、ASP.NET アプリケーションのトレース、Web サーバーのログの分析について、さらに理解を深めましょう。

### Windows Azure の Web サイトのトラブルシューティング

Windows Azure の Web サイト (WAWS) のトラブルシューティングの詳細については、以下のリソースを参照してください。

* [Windows Azure でのトラブルシューティング](/en-us/develop/net/best-practices/troubleshooting/)<br/>
  WAWS について簡単に説明したセクションが含まれる入門者向けの基本的なホワイト ペーパーです。
* [Web サイトのトラブルシューティング](/en-us/develop/net/best-practices/troubleshooting-web-sites/)<br/>
  WAWS を重点的に説明しています。
* [Windows Azure の Web サイトの診断ログを有効にする](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)<br/>
  取り上げられている情報はこのチュートリアルとほぼ同じですが、Visual Studio を使わずに診断ログを入手する方法に重点が置かれています。
* [Web サイトの監視方法](/en-us/manage/services/web-sites/how-to-monitor-websites/)<br/>
   トラブルシューティングのドキュメントには記載されていない有益な情報を、「[configure diagnostics and download logs (診断の構成とログのダウンロード)](/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics)」セクションで取り上げています。

具体的なトラブルシューティングについての質問は、次のいずれかのフォーラムで投稿してください。

* [ASP.NET サイト上の Windows Azure フォーラム](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [MSDN 上の Windows Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/)。
* [StackOverflow.com](http://www.stackoverflow.com)。

### Visual Studio でのデバッグ

Visual Studio のデバッグ モードの使い方については、MSDN のトピック「[Visual Studio でのデバッグ](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx)」を参照してください。

### ASP.NET アプリケーションでのトレース

ASP.NET トレースに関しては、最新かつ必要な情報をすべて網羅した解説がインターネットには存在しません。そのため、過去に作成された入門者向けの資料を参考にするのが最善の方法となります。MVC がまだ存在していなかったために Web フォームを想定して書かれていますが、具体的な問題については、最新のブログで情報を補うことができます。たとえば、以下のリソースが参考になります。

* [ASP.NET トレース](http://msdn.microsoft.com/en-us/library/ms972204.aspx)<br/>
  最新とは言えませんが、基本的な事柄がわかりやすくまとめられています。
* [トレース リスナー](http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx)<br/>
  トレース リスナーについて書かれていますが、[WebPageTraceListener](http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx) には触れていません。
* [チュートリアル: ASP.NET トレースと System.Diagnostics トレースの統合](http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx)<br/>
  古い情報ですが、入門記事では扱っていないような詳しい情報が記載されています。
* [ASP.NET MVC Razor ビューでのトレース](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Razor ビューでのトレースに加え、MVC アプリケーションでハンドルされない例外をすべてログに記録するためのエラー フィルターの作成方法についても説明されています。Web フォーム アプリケーションで、ハンドルされない例外をすべてログに記録する方法については、MSDN の「[エラー ハンドラーの完全なコード例](http://msdn.microsoft.com/en-us/library/bb397417.aspx)」で紹介されている Global.asax サンプルを参照してください。MVC または Web フォームで、特定の例外をログに記録すると共に、既定のフレームワークの処理はそのまま活かしておく必要がある場合、例外を捕捉してから再スローする方法を利用できます。その例を次に示します。

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Scott Guthrie: Windows Azure で構築する実用的なクラウド アプリケーション (パート 2)](http://vimeo.com/68215602)<br/>
  Windows Azure クラウド アプリケーションのトレースにおける最新の推奨事項については、このビデオの 47:00 ～ 55:36 をご覧ください。
* [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  このチュートリアルで Visual Studio を使って行ったことをコマンド ラインで行う方法が解説されています。[Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) は、ASP.NET アプリケーションをデバッグするためのツールです。

エラーをログに記録する方法としては、独自のトレース コードを記述する以外にも、[ELMAH](http://nuget.org/packages/elmah/) に代表されるオープン ソースのログ記録フレームワークを使う方法があります。詳細については、[Scott Hanselman が ELMAH についてまとめたブログ記事](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)を参照してください。

Windows Azure からストリーミング ログを取得する必要がある場合、必ずしも ASP.NET や System.Diagnostics トレースを使う必要はありません。Windows Azure の Web サイトのストリーミング ログ サービスは、*LogFiles* フォルダーに見つかったすべての *.txt* ファイル、*.html* ファイル、*.log* ファイルをストリーミングします。したがって、Web サイトのファイル システムに書き込む独自のログ記録システムを作成することもできます。必要なファイルが自動的にストリーミングされ、ダウンロードされます。必要な作業は、*d:\home\logfiles* フォルダーにファイルを作成するアプリケーション コードを記述するだけです。

### Web サーバーのログの分析

Web サーバーのログの分析の詳細については、次のリソースを参照してください。

* [Log Parser](http://www.microsoft.com/en-us/download/details.aspx?id=24659)<br/>
  Web サーバーのログ (*.log* ファイル) に記録されているデータを表示するためのツールです。
* [Troubleshooting IIS Performance Issues or Application Errors using LogParser (IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする)](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Web サーバーのログを分析する際に活用できる Log Parser ツールについて基本的な事柄が説明されています。
* [LogParser の使用に関して Robert McMurray が執筆したブログ記事](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP ステータス コード](http://support.microsoft.com/kb/943891)

### 失敗した要求トレース ログの分析

失敗した要求トレース ログの活用方法については、Microsoft TechNet Web サイトの「[Using Failed Request Tracing (失敗した要求トレースの使用)](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing)」セクションなどが参考になります。ただし、このドキュメントで扱う内容は、失敗した要求トレースを IIS で構成する作業が主体です。この作業を Windows Azure の Web サイトで行うことはできません。




[GetStarted]: /en-us/develop/net/tutorials/get-started/




