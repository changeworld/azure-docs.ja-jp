<properties 
	pageTitle="Visual Studio での Azure Web サイトのトラブルシューティング" 
	description="Visual Studio 2013 に組み込まれているリモート デバッグ、トレース、ログ記録のツールを使用して、Azure の Web サイトのトラブルシューティングを行う方法について説明します。" 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/13/2014" 
	ms.author="tdykstra"/>

# Visual Studio での Azure Web サイトのトラブルシューティング

このチュートリアルでは、Visual Studio のツールを活用し、Azure Web サイトで実行されているアプリケーションを [デバッグ モード](http://www.visualstudio.com/ja-jp/get-started/debug-your-app-vs.aspx) でリモートから実行するか、アプリケーションのログと Web サーバーのログを参照することによってデバッグする方法を説明します。

学習内容: 

* Visual Studio から利用できる Azure のサイト管理機能。
* Visual Studio のリモート ビューを使用して、リモート Web サイトにすぐに変更を加える方法。
* Azure でプロジェクトが実行されているときに、リモートからデバッグ モードを実行する方法。
* アプリケーションのトレース ログを作成する方法と、ログが作成されている最中にそれらを確認する方法。
* Web サーバーのログ (詳細なエラー メッセージ、失敗した要求トレースを含む) を確認する方法。
* Azure のストレージ アカウントに診断ログを送り、そこでログを確認する方法。

Visual Studio Ultimate がある場合は、デバッグに [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) を使用することもできます。IntelliTrace については、このチュートリアルでは説明しません。

### チュートリアル セグメント

- [前提条件](#prerequisites)
- [サイトの構成と管理](#sitemanagement)
- [サーバー エクスプローラーでの Web サイト ファイルへのアクセス](#remoteview)
- [リモート デバッグ](#remotedebug)
	- Web サイトのリモート デバッグ
	- Web ジョブのリモート デバッグ
	- リモート デバッグに関する注意 
- [診断ログの概要](#logsoverview)
- [アプリケーションのトレース ログの作成と表示](#apptracelogs)
- [Web サーバーのログの表示](#webserverlogs)
- [詳細なエラー メッセージ ログの表示](#detailederrorlogs)
- [ファイル システムのログのダウンロード](#downloadlogs)
- [ストレージ ログの表示](#storagelogs)
- [失敗した要求ログの表示](#failedrequestlogs)
- [次のステップ](#nextsteps)

<h2><a name="prerequisites"></a>前提条件</h2>

このチュートリアルでは、[Azure Websites と ASP.NET を使用する][GetStarted] で設定した開発環境、Web プロジェクト、および Azure Web サイトを使用します。Web ジョブのセクションでは、[Azure Web ジョブ SDK の使用][GetStartedWJ] で作成したアプリケーションが必要です。

このチュートリアルで示すコード サンプルは、C# MVC Web アプリケーションに対応していますが、トラブルシューティング手順は Visual Basic および Web フォームの各アプリケーションでも同じです。

リモート デバッグには、Visual Studio 2013 または Visual Studio 2012 Update 4 が必要です。Web ジョブ用のリモート デバッグ機能と**サーバー エクスプローラー**機能の使用には、[Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) またはそれ以降が必要です。このチュートリアルで紹介するその他の機能については、Visual Studio 2013 Express for Web や Visual Studio 2012 Express for Web でも動作します。 

ストリーミング ログ機能は、.NET Framework 4 以降を対象とするアプリケーションでのみ動作します。

<h2><a name="sitemanagement"></a>サイトの構成と管理</h2>

Visual Studio は、管理ポータルで利用できるサイト管理機能や構成設定に一部アクセスできるようになっています。このセクションでは、その対象となる機能や設定について取り上げます。

1. まだ Visual Studio から Azure にサインインしていない場合は、**サーバー エクスプローラー** の **[Azure に接続]** をクリックします。

	または、アカウントへのアクセスを可能にする管理証明書をインストールします。管理証明書により、追加の Azure サービス (SQL Database サービスとモバイル サービス) に対するアクセス許可が**サーバー エクスプローラー**に付与されます。証明書をインストールする方針を選択した場合は、**サーバー エクスプローラー**で、**Azure** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理]** をクリックします。**[Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブをクリックし、**[インポート]** をクリックします。操作手順に従い、Azure アカウント用のサブスクリプション ファイル (*.publishsettings* ファイル) をダウンロードしてインポートします。

	> [AZURE.NOTE]
	> サブスクリプション ファイルをダウンロードする場合は、ソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) に保存し、インポートが完了したらそのファイルを削除します。悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

	Visual Studio から Azure リソースへの接続の詳細については、「[アカウント、サブスクリプション、管理ロールの管理](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)」をご覧ください。

2. **サーバー エクスプローラー**で **[Azure]** を展開し、**[Web サイト]** を展開します。

3. [Azure Websites と ASP.NET を使用する][GetStarted] で作成した Web サイトのノードを右クリックし、**[設定の表示]** をクリックします。

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	**[Azure の Web サイト]** タブが表示され、Visual Studio から利用できるサイト管理タスクや構成タスクが確認できます。

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	このチュートリアルでは、ログとトレースのドロップダウンを使用します。	また、リモート デバッグも使用し、さまざまな方法でこの機能を有効にします。
   
	このウィンドウの [アプリケーション設定] ボックスと [接続文字列] ボックスについては、[Azure の Web サイト:アプリケーション文字列と接続文字列の動作に関するページ](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) をご覧ください。

	このウィンドウでは実行できないサイト管理タスクを行う場合は、**[すべての Web サイト設定]** をクリックすると、ブラウザー ウィンドウが開いて管理ポータルにアクセスできます。詳細については、[Web サイトの構成方法](/ja-jp/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). をご覧ください。

<h2><a name="remoteview"></a>サーバー エクスプローラーでの Web サイト ファイルへのアクセス</h2>

通常は、Web.config ファイルで  `customErrors` フラグを  `On` または  `RemoteOnly` に設定してサイトをデプロイします。これは、何か問題が発生したときに、役に立つエラー メッセージを表示しないことを意味します。表示されるエラーの多くは、次のようなページになります。

**'/' アプリケーションのサーバー エラー:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**エラーが発生しました:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web サイト側でページを表示できません**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

多くの場合、エラーの原因を最も簡単に見つける方法は、詳細なエラー メッセージを有効にすることです。その結果、既に示した最初のスクリーンショットのように、対応方法が表示されます。ここでは、デプロイした Web.config ファイルの変更が必要です。プロジェクト内の  *Web.config* ファイルを編集し、プロジェクトを再デプロイすること、または [Web.config の変換](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) を作成し、デバッグ ビルドをデプロイすることもできますが、より簡単な方法があります。 *remote view* 機能を使用して、リモート サイトから **[ソリューション エクスプローラー]** でファイルを直接表示し、編集できます。

1. **サーバー エクスプローラー**で **[Azure]**、**[Web サイト]**、デプロイしようとする Web サイトに対応するノードの順に展開します。

	Web サイトのコンテンツ ファイルとログ ファイルへのアクセス許可を付与するノードが表示されます。

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. **[ファイル]** ノードを展開し、 *Web.config* ファイルをダブルクリックします。

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio は、リモート サイトから Web.config ファイルを開き、タイトル バーではファイル名の横に "[リモート]" と表示されています。

3. 次の行を  `system.web` 要素に追加します。

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. あまり役に立たないエラー メッセージを示しているブラウザーを最新の情報に更新すると、次の例のような詳細なエラー メッセージが表示されます。

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(ここで示されているエラーは、赤く表示されている行を  *Views\Home\Index.cshtml* に追加することによって作成したものです。)

Web.config ファイルを編集することは、Azure Web サイトにあるファイルの読み取りと編集を可能にして、トラブルシューティングを容易にする 1 つのシナリオの例にすぎません。

<h2><a name="remotedebug"></a>リモート デバッグ</h2>

詳細なエラー メッセージで十分な情報が表示されておらず、エラーをローカルで再現できない場合は、トラブルシューティングの別の方法として、リモートでデバッグ モードを実行できます。ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。 

リモート デバッグは、Visual Studio の各 Express Edition では機能しません。

### Web サイトのリモート デバッグ

このセクションでは、[Azure Websites と ASP.NET を使用する][GetStarted] で作成したプロジェクトを使用してリモート デバッグを実行する方法を示します。

1. [Azure Websites と ASP.NET を使用する][GetStarted] で作成した Web プロジェクトを開きます。

1.  *Controllers\HomeController.cs* を開きます。

2.  `About()` メソッドを削除し、その位置に次のコードを挿入します。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2.  `ViewBag.Message` の行に [ブレークポイント](http://www.visualstudio.com/ja-jp/get-started/debug-your-app-vs.aspx) を設定します。

1. **[ソリューション エクスプローラー]** で、 Web プロジェクトを右クリックし、**[発行]** をクリックします。

2. **[プロファイル]** ボックスの一覧から、[Azure Websites と ASP.NET を使用する][GetStarted] で使用したのと同じプロファイルを選択します。

3. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. デプロイが完了し、ブラウザーが起動してサイトの Azure URL が表示されたら、ブラウザーを閉じます。

5. Visual Studio 2013 の場合: **サーバー エクスプローラー** で **[Azure]**、**[Web サイト]** の順に展開し、Web サイトを右クリックして **[デバッガーの接続]** をクリックします。 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

6. Visual Studio 2012 Update 4 の場合: <a id="vs2012"></a>

	* Azure の管理ポータルで、Web サイトの **[構成]** タブに移動し、下へスクロールして **[サイト診断]** セクションを表示します。

	* **[リモート デバッグ]** を **[オン]** に設定し、**リモート デバッグに使用する Visual Studio のバージョン**を **[2012]** に設定します。

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Visual Studio の **[デバッグ]** メニューの **[プロセスにアタッチ]** をクリックします。

	* **[修飾子]** ボックスに Web サイトの URL を入力します。プレフィックス `http://` は付けずに入力してください。 

	* **[すべてのユーザーからのプロセスを表示する]** を選択します。

	* 資格情報を求められたら、Web サイトを発行する権限のあるユーザー名とパスワードを入力します。資格情報を入手するには、管理ポータルで Web サイトの [ダッシュボード] タブに移動し、**[発行プロファイルのダウンロード]** をクリックします。そのファイルをテキスト エディターで開くと、1 つ目の **userName=** と **userPWD=** の後に、ユーザー名とパスワードが表示されます。 

	* **[選択可能なプロセス]** テーブルにプロセスが表示されたら、**[w3wp.exe]** を選択し、**[アタッチ]** をクリックします。

	* ブラウザーを起動して、目的のサイトの URL に移動します。

	デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

6. メニューの **[About]** をクリックします。

	Visual Studio がブレークポイントで停止します。コードが実行されている場所は、ローカル コンピューターではなく Azure 上です。

7.  `currentTime` 変数にマウスを合わせて、時刻値を表示します。

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	表示される時刻は、Azure サーバーの時刻です。ローカル コンピューターとはタイム ゾーンが異なります。

8.  `currentTime` 変数に新しい値 (「Now running in Azure」など) を入力します。

5. F5 キーを押して実行を継続します。

	Azure で実行中の [About] ページに、先ほど currentTime 変数に対して入力した新しい値が表示されます。

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Web ジョブのリモート デバッグ

このセクションでは、[Azure Web ジョブ SDK の使用] (../websites-dotnet-webjobs-sdk) で作成するプロジェクトと Web サイトを使用してリモートでデバッグする方法を示します。このセクションで紹介する機能は、Visual Studio 2013 Update 4 でのみ使用できます。

1. [Azure Web ジョブ SDK の使用][GetStartedWJ] で作成した Web プロジェクトを開きます。

1. ContosoAdsWebJob プロジェクトで、 *Functions.cs* を開きます。

2.  `GnerateThumbnail` メソッドで最初のステートメントに [ブレークポイントを設定](http://www.visualstudio.com/ja-jp/get-started/debug-your-app-vs.aspx) します。

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. **ソリューション エクスプローラー**で Web プロジェクト (Web ジョブ プロジェクトではない) を右クリックし、**[発行]** をクリックします。

2. **[プロファイル]** ボックスの一覧から、[Azure Web ジョブ SDK とは] (../websites-dotnet-webjobs-sdk) で使用したものと同じプロファイルを選択します。

3. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

	Visual Studio によって Web プロジェクトと Web ジョブ プロジェクトが配置され、ブラウザーでサイトの Azure URL が表示されます。

5. **[サーバー エクスプローラー]** で、**[Azure]**、**[Web サイト]**、[使用中の Web サイト]、**[Web ジョブ]**、**[継続]** の順に展開し、**[ContosoAdsWebJob]** を右クリックします。

7. **[デバッガーの接続]** をクリックします。 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。この待ち時間が生じるのは、Web サイトでのデバッグ モードの初回実行時に限られます。次回のデバッガーの接続では、初回の実行から 48 時間以内の場合、待ち時間は発生しません。

6. Contoso Ads ホーム ページを表示している Web ブラウザーで、新しい広告を作成します。 

	広告を作成すると、キュー メッセージが作成されます。キュー メッセージは Web ジョブによって取得され、処理されます。Web ジョブ SDK がキュー メッセージを処理する関数を呼び出すと、コードがブレークポイントにヒットします。

7. デバッガーがブレークポイントで停止すると、プログラムがクラウドを実行している間に、変数の値を確認、変更できます。次の図では、デバッガーは、GenerateThumbnail メソッドに渡された blobInfo オブジェクトの内容を示しています。

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. F5 キーを押して実行を継続します。

	GenerateThumbnail メソッドが、サムネイルの作成を完了します。

6. ブラウザーでインデックス ページを更新すると、サムネイルが表示されます。

6. Visual Studio で、Shift キーを押しながら F5 キーを押すと、デバッグは停止します。

7. **サーバー エクスプローラー**で、ContosoAdsWebJob ノードを右クリックし、**[ダッシュボードの表示]** をクリックします。

8. Azure の資格情報を使用してサインインし、Web ジョブ名をクリックして、Web ジョブのページに移動します。 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	GenerateThumbnail 関数が最近実行されたことが、ダッシュボードに示されます。

	(次回、**[ダッシュボードの表示]** をクリックするときには、サインインする必要はありません。ブラウザーが Web ジョブのページに直接移動します。)

9. 関数の実行に関する詳細を表示するには、関数名をクリックします。

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

関数が [ログを書き込んだ](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs) 場合は、**ToggleOutput** をクリックするとログを表示できます。

### リモート デバッグに関する注意

* 運用環境におけるデバッグ モードの実行はお勧めできません。運用環境のサイトが複数のサーバー インスタンスにスケール アウトされていない場合、デバッグを行うと、Web サーバーが他の要求に応答できなくなります。しかし、Web サーバーのインスタンスが複数存在する場合、デバッガーのアタッチ先となるインスタンスは無作為に決定されるため、そのインスタンスにブラウザーのその後の要求を確実に渡すことができません。また、運用環境にデバッグ ビルドを展開することも一般的ではありません。リリース ビルドに対してはコンパイラが最適化を行うため、ソース コードの状況を行レベルで把握することは不可能です。運用環境の問題をトラブルシューティングするのに最も適しているリソースは、アプリケーション トレースと Web サーバーのログです。

* リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。数分以上停止しているプロセスは、応答していないプロセスと見なされ、Azure によりシャットダウンされます。

* デバッグ中は、サーバーから Visual Studio にデータが送信されるため、帯域幅の使用料に影響が及ぶ可能性があります。帯域幅使用料については、[Azure 料金計算ツール](/ja-jp/pricing/calculator/) をご覧ください。

*  *Web.config* ファイルの  `compilation` 要素の  `debug` 属性が true に設定されていることを確認します。デバッグ ビルド構成で発行するときは、true が既定値です。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* デバッグ対象となるコードにデバッガーがステップ インしない場合、[マイ コードのみ] の設定を変更してみてください。詳細については、「[ステップ実行をマイ コードのみに制限する](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)」をご覧ください。

* リモート デバッグ機能を有効にしたときに、サーバー上でタイマーが開始され、48 時間後にこの機能が自動的に無効になります。この 48 時間の上限はセキュリティとパフォーマンス上の理由で設定されています。必要に応じて、この機能を何回でも簡単に有効に戻すことができます。積極的にデバッグを実行している場合以外は、この機能を無効にしたままにすることをお勧めします。

* 手動でデバッガーを Web サイト プロセス (w3wp.exe) だけでなく、任意のプロセスに接続できます。Visual Studio のデバッグ モードの使い方の詳細については、MSDN のトピック「[Visual Studio でのデバッグ](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx)」をご覧ください。

<h2><a name="logsoverview"></a>診断ログの概要</h2>

Azure Web サイトで動作する ASP.NET アプリケーションでは、次の種類のログが作成されます。

* **アプリケーション トレース ログ**<br/>
  アプリケーションが [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) クラスのメソッドを呼び出すことによって作成されます。
* **Web サーバーのログ**<br/>
  Web サーバーは、サイトに届くすべての HTTP 要求について、それぞれログ エントリを作成します。
* **詳細なエラー メッセージ ログ**<br/>
  失敗した HTTP 要求 (ステータス コード 400 以上の要求) について、より詳しい情報を記した HTML ページが Web サーバーによって作成されます。 
* **失敗した要求トレース ログ**<br/>
  失敗した HTTP 要求についての詳しいトレース情報を記録した XML ファイルが Web サーバーによって作成されます。また、ブラウザーで XML の体裁を設定するための XSL ファイルも作成されます。
  
ログ出力はサイトのパフォーマンスに影響を及ぼすため、Azure では、ログの種類ごとにその有効と無効を切り替えることができるようになっています。アプリケーション ログについては、特定の重大度レベルを超えるログだけを記録するように指定できます。新しい Web サイトを作成した時点ではすべてのログが既定で無効になります。

ログは、Web サイトのファイル システムにある  *LogFiles* フォルダー内のファイルに出力され、FTP 経由でアクセスできます。Web サーバーのログとアプリケーションのログは、Azure のストレージ アカウントに出力することもできます。ストレージ アカウントには、ファイル システムよりも大量のログを保持できます。ファイル システムを使用した場合、保存できるログの上限は 100 MB です。(ファイル システムのログは、短期間のみ保持されます。上限に達すると、古いログ ファイルは削除され、新しいログ ファイルのための領域が確保されます。)  

<h2><a name="apptracelogs"></a>アプリケーションのトレース ログの作成と表示</h2>

このセクションでは、次のタスクを実行します。

* [Azure Websites と ASP.NET を使用する][GetStarted] で作成した Web プロジェクトにトレース ステートメントを追加します。
* プロジェクトをローカル実行したときのログを確認します。
* Azure で実行中のアプリケーションによって生成されたログを確認します。 

Web ジョブでアプリケーション ログを作成する方法については、[Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法 - ログの作成方法] (../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs) をご覧ください。次の手順は、ログの表示や、Azure での格納方法を制御するためのものですが、Web ジョブによって作成されたアプリケーションのログにも適用されます。 

### アプリケーションへのトレース ステートメントの追加

1.  `Trace` ステートメントと  `System.Diagnostics` の  `using` ステートメントを追加するために  *Controllers\HomeController.cs* ファイルを開いて、その内容を次のコードで置き換えます。

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

3. F5 キーを押してデバッグ モードでアプリケーションを実行します。

	既定のトレース リスナーは、すべてのトレース出力を他のデバッグ出力と一緒に **[出力]** ウィンドウに書き込みます。次の図は、 `Index` メソッドに追加したトレース ステートメントからの出力結果を示したものです。

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	以降の手順では、コンパイルせずにデバッグ モードで、トレース出力を Web ページに表示する方法を紹介します。

2. プロジェクト フォルダーにあるアプリケーションの Web.config ファイルを開き、ファイル末尾の終了 `</configuration>` 要素直前に、`<system.diagnostics>` 要素を追加します。

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

	 `WebPageTraceListener` を使用すると、ブラウザーから  `/trace.axd` にアクセスすることでトレース出力を表示できます。

3. 次のように、Web.config ファイルの `<system.web>` の下に、<a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trace 要素</a> を追加します。

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Ctrl キーを押しながら F5 キーを押して、アプリケーションを実行します。

4. ブラウザー ウィンドウのアドレス バーで、URL に続けて  *trace.axd* を入力し、Enter キーを押します (例: http://localhost:53370/trace.axd)。

5. **[アプリケーション トレース]** ページの最初の行 (BrowserLink の行とは異なる) で、**[詳細の表示]** をクリックします。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	 **[要求の詳細]** ページが表示され、 `Index` メソッドに追加したトレース ステートメントからの出力が **[トレース情報]** セクションに表示されます。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	既定では、 `trace.axd` の使用はローカルに限られます。 *Web.config* ファイルで  `trace` 要素に `localOnly="false"` を追加すると、リモート サイトからも利用できるようになります。その例を次に示します。

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	ただしセキュリティ上の理由から、運用サイトで  `trace.axd` を有効にすることは、通常お勧めできません。もっと簡単に Azure Web サイトのトレース ログを確認する方法を以降のセクションで説明します。

### Azure でのトレース出力の確認

1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[発行]** をクリックします。

2. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

	更新したプロジェクトを発行すると、ブラウザー ウィンドウが起動して自分のホーム ページが表示されます (ただし、**[接続]** タブの **[宛先 URL]** をクリアした場合を除く)。

3. **サーバー エクスプローラー**で Web サイトを右クリックし、**[ストリーミング ログを表示]** を選択します。 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	ログ ストリーミング サービスに接続されたことを示すメッセージが **[出力]** ウィンドウに表示されます。ログが出力されないまま 1 分が経過すると、その都度、1 行の通知が追加されます。

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. アプリケーションのホーム ページが表示されているブラウザー ウィンドウで、**[Contact]** をクリックします。

	数秒すると、 `Contact` メソッドに追加したエラー レベルのトレースの出力が **[出力]** ウィンドウに表示されます。

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio にエラー レベルのトレースしか表示されていないのは、それが、ログ監視サービスを有効にしたときの既定の設定であるためです。先ほどサイトの設定ページを開いたときに目にしたように、新しい Azure Web サイトを作成したときは、すべてのログが既定で無効となります。

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	ただし、**[ストリーミング ログを表示]** を選択すると、**[アプリケーション ログ記録 (ファイル システム)]** が自動的に **[エラー]** に変更されます。これは、エラー レベルのログが報告されることを意味します。すべてのトレース ログを表示するためには、この設定を **[詳細]** に変更する必要があります。"エラー" よりも低い重大度レベルを選択すると、その重大度を超えるログがすべて報告されます。つまり [詳細] を選択した場合は、"情報"、"警告"、"エラー" に該当するログが表示されます。  

4. 先ほどと同じように、**サーバー エクスプローラー**で Web サイトを右クリックし、**[設定の表示]** をクリックします。

5. **[アプリケーション ログ記録 (ファイル システム)]** を **[詳細]** に変更し、**[保存]** をクリックします。
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. **[Contact]** ページが表示されているブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	数秒すると、**出力**ウィンドウにすべてのトレース出力が表示されます。

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	このセクションでは、ログの有効と無効の切り替えを Azure Web サイトの設定で行いました。トレース リスナーの有効と無効は、Web.config ファイルで設定することもできます。ただし、Web.config ファイルに変更を加えると、アプリケーション ドメインの再利用処理が実行されます。一方、Web サイトを介してログを有効にした場合は、そのようにはなりません。問題の再現に時間がかかる場合や、発生のタイミングが散発的である場合、アプリケーション ドメインの再利用処理で問題が "解消" し、再発までしばらく待たなければならなくなります。Azure で診断を有効にすると、そのようなことにはならず、すぐにエラー情報を収集できます。

### 出力ウィンドウの機能

**[出力]** ウィンドウの **[Azure ログ]** タブには、いくつかのボタンと 1 つのテキスト ボックスが表示されます。

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

それぞれの機能を次に示します。

* **[出力]** ウィンドウをクリアする。
* 右端での折り返しを有効または無効にする。
* ログの監視を開始または停止する。
* 監視するログを指定する。
* ログをダウンロードする。
* 検索文字列または正規表現によるフィルターをログに適用する。
* **出力**ウィンドウを閉じる。

検索文字列または正規表現を入力した場合、ログ情報は、Visual Studio によってクライアント側でフィルタリングされます。つまり、フィルターの条件は、**出力**ウィンドウにログが表示された後に入力できます。フィルターの条件を変更するためにログを生成し直す必要はありません。

<h2><a name="webserverlogs"></a>Web サーバーのログの表示</h2>

Web サーバーのログには、サイト上の HTTP アクティビティがすべて記録されます。それらの HTTP アクティビティを **[出力]** ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。 

1. **サーバー エクスプローラー**から開いた **Azure Web サイトの [構成]** タブで [Web サーバーのログ記録] を **[オン]** に変更し、**[保存]** をクリックします。

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. **[出力]** ウィンドウの **[監視する Azure ログの指定]** をクリックします。
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. **[Azure ログ オプション]** ダイアログ ボックスの  **[Web サーバーのログ]** を選択し、**[OK]** をクリックします。

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	通常はアプリケーションのログが先に表示され、続けて Web サーバーのログが表示されます。ログが表示されるまでにしばらく時間がかかる場合があります。 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Visual Studio で Web サーバーのログを初めて有効にしたとき、既定では、Azure によってファイル システムにログが書き込まれます。代わりに管理ポータルを使用して、Web サーバーのログの書き込み先として、ストレージ アカウントの BLOB コンテナーを指定することもできます。詳細については、 [How to Configure Web Sites (Web サイトの構成方法)] (/ja-jp/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig) の**サイトの診断**に関するセクションをご覧ください。 

Web サーバーのログを Azure のストレージ アカウントに記録するように管理ポータルで指定した後、Visual Studio でログ記録を無効にした場合、Visual Studio で再度ログ記録を有効にすると、ストレージ アカウントの設定が復元されます。 

<h2><a name="detailederrorlogs"></a>詳細なエラー メッセージ ログの表示</h2>

詳細なエラー ログでは、エラー応答コード (400 以上) が返された HTTP 要求について、いくつかの詳しい情報が確認できます。それらの HTTP アクティビティを**[出力]** ウィンドウに表示するためには、サイトに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた **Azure Web サイトの [構成]** タブで **[詳細なエラー メッセージ]** を **[オン]** に変更し、**[保存]** をクリックします。

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. **[出力]** ウィンドウの **[監視する Azure ログの指定]** をクリックします。

3. **[Azure ログ オプション]** ダイアログ ボックスの **[すべてのログ]** をクリックし、 **[OK]** をクリックします。

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. ブラウザー ウィンドウのアドレス バーで、404 エラーの原因となるような余分な文字を URL に追加し (例: `http://localhost:53370/Home/Contactx`)、Enter キーを押します。

	数秒後、Visual Studio の**[出力]** ウィンドウに詳細なエラー ログが表示されます。

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Ctrl キーを押しながらリンクをクリックすると、次のような書式化されたログ出力がブラウザーに表示されます。

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>ファイル システムのログのダウンロード</h2>

**[出力]** ウィンドウで監視できるすべてのログは *.zip* ファイルとしてダウンロードすることもできます。 

1. **[出力]** ウィンドウの **[ストリーミング ログのダウンロード]** をクリックします。

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	エクスプローラーが起動して  *Downloads* フォルダーが開き、ダウンロード済みのファイルが選択状態で表示されます。

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. この *.zip* ファイルを展開すると、次のフォルダー構造が確認できます。

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* アプリケーション トレース ログは、 *LogFiles\Application* フォルダーの *.txt* ファイルに記録されます。
	* Web サーバーのログは、 *LogFiles\http\RawLogs* フォルダーの *.log* ファイルに記録されます。これらのファイルの閲覧と操作は、[Log Parser](http://www.microsoft.com/ja-jp/download/details.aspx?displaylang=en&id=24659) などのツールを使って行うことができます。
	* 詳細なエラー メッセージのログは、 *LogFiles\DetailedErrors* フォルダーの *.html* ファイルに記録されます。

	( *deployments* フォルダーは、ソース管理の発行によって作成されたファイルに使用されます。Visual Studio の発行に関連したファイルは保存されません。 *Git* フォルダーは、ログ ファイル ストリーミング サービスやソース管理の発行に関連したトレースに使用されます。)  

<h2><a name="storagelogs"></a>ストレージ ログの表示</h2>

アプリケーション トレース ログを Azure のストレージ アカウントに送って、Visual Studio で表示することもできます。ストレージ アカウントを作成して管理ポータルでストレージ ログを有効にし、**[Azure の Web サイト]** ウィンドウの **[ログ]** タブでそれらを表示します。

次の 3 個の送信先のいずれかまたはすべてにログを送信できます。

* ファイル システム。
* ストレージ アカウント テーブル。
* ストレージ アカウント BLOB。

送信先ごとに異なる重要度レベルを指定することもできます。 

テーブル形式を使用すると、ログの詳細をオンラインで簡単に表示でき、またストリーミングもサポートされています。テーブル内にあるログを照会することや、作成されている新しいログを表示することもできます。BLOB 形式を使用すると、ログをファイルにダウンロードし、HDInsight を使ってそれらのログを簡単に解析できるようになります。HDInsight は、BLOB ストレージを処理する方法を把握しているためです。詳細については、[データ ストレージ オプション (Azure を使用した実際のクラウド アプリケーションの作成) に関するページ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options) の「**Hadoop and MapReduce**」をご覧ください。

現在、ファイル システムのログは詳細レベルに設定されています。次の手順では、ストレージ アカウント テーブルに切り替える目的で情報レベルのログを設定する方法を説明します。情報レベルを指定した場合、 `Trace.TraceInformation`、 `Trace.TraceWarning`、 `Trace.TraceError` の呼び出しで作成されたすべてのログが表示され、  `Trace.WriteLine` の呼び出しで作成されたログは除外されます。

多くの場合、ストレージ アカウント形式はファイル システム形式と比較して、ログに対してより多くのストレージと、より長い期間にわたる保持を実現します。アプリケーション トレース ログをストレージに送る別の利点は、ファイル システムのログでは得ることのできない詳しい情報をそれぞれのログと共に入手できることです。

5. Azure ノードの **[ストレージ]** を右クリックし、**[ストレージ アカウントの作成]** をクリックします。

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. **[ストレージ アカウントの作成]** ダイアログ ボックスで、ストレージ アカウントの名前を入力します。 

	名前は一意である必要があります (Azure ストレージ アカウントで同じ名前は使用できません)。入力した名前が既に使用されている場合、変更を求められます。

	ストレージ アカウントにアクセスする URL は、*{name}*.c-ore.windows.net という形式になります。 

5. **[リージョン/アフィニティ グループ]** ドロップダウン リストで最も近いリージョンを設定します。

	この設定は、お使いのストレージ アカウントをホストしている Azure データセンターを指定します。このチュートリアルでは、選択肢によって顕著な相違は生じませんが、運用サイトの場合は、待ち時間とデータ送信料金を最小限に抑えるために、Web サーバーとストレージ アカウントを同じリージョンに設定することが望まれます。待ち時間を最小限にするには、Web サイト (後で作成する) をサイトにアクセスするブラウザーのできるだけ近くに配置します。

6. **[レプリケーション]** ボックスを **[ローカル冗長]** に設定します。 

	Geo レプリケーションをストレージ アカウントに対して有効にすると、1 次拠点で重大な障害が発生した場合に備えて、保存したコンテンツは 2 次データセンターに複製されて、フェイルオーバーが可能になります。Geo レプリケーションには追加費用が発生する場合があります。また、テストおよび開発アカウントの場合は、一般的に Geo レプリケーションに対する課金は避けたいと考えるでしょう。詳細については、[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account/#replication-options) をご覧ください。

5. **[作成]** をクリックします。 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Visual Studio の **[Azure の Web サイト]** ウィンドウで、**[ログ]** タブをクリックし、**[管理ポータルでのログの構成]** をクリックします。

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	自分の Web サイトの管理ポータルの **[構成]** タブが開きます。この画面にアクセスするために、**[Web サイト]** タブをクリックして該当する Web サイトをクリックし、**[構成]** タブをクリックすることもできます。

2. 管理ポータルの **[構成]** タブで、下へスクロールしてアプリケーションの診断セクションを表示し、**[アプリケーション ログ記録 (ストレージ)]** を **[オン]** に変更します。

3. **[ログ レベル]** を **[情報]** に変更します。

4. **[テーブル ストレージの管理]** をクリックします。

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	ストレージ アカウントが複数ある場合は、**[アプリケーション診断用のテーブル ストレージの管理]** ボックスで目的のストレージ アカウントを選択できます。新しいテーブルを作成するか、既存のテーブルを使用できます。

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. **[アプリケーション診断用のテーブル ストレージの管理]** ボックスのチェック マークをクリックしてこのボックスを閉じます。

6. 管理ポータルの **[構成]** タブで、**[保存]** をクリックします。

7. アプリケーションの Web サイトを表示するブラウザー ウィンドウで、**[Home]**、**[About]**、**[Contact]** を順にクリックします。

	これらの Web ページを閲覧することによって生成されたログ情報がストレージ アカウントに書き込まれます。

8. Visual Studio の **[Azure の Web サイト]** ウィンドウの **[ログ]** タブで、**[診断の概要]** の下に表示される **[最新の情報に更新]** をクリックします。

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	既定では、直近 15 分間のログが **[診断の概要]** セクションに表示されます。この時間を変更することで、表示するログの量を増やすことができます。 

	("テーブルが見つからない" という内容のエラーが表示された場合は、 **[アプリケーション ログ記録 (ストレージ)]** を有効にした後と、**[保存]** をクリックした後に、トレース対象のページを参照したことをご確認ください。)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	この画面に **[プロセス ID]** と **[スレッド ID]** がログごとに表示されていることに注目してください。ファイル システムのログでは確認できない情報です。Azure のストレージ テーブルを直接参照することで、さらに別のフィールドを確認できます。

8. **[すべてのアプリケーション ログの表示]** をクリックします。

	Azure のストレージ テーブル ビューアーにトレース ログ テーブルが表示されます。
   
	("シーケンスに要素が含まれていません" というエラーが表示された場合は、**サーバー エクスプローラー**を開き、**Azure** ノードで、自分のストレージ アカウントに該当するノードを展開し、**[テーブル]** を右クリックして **[最新の情報に更新]** をクリックします。)

	![サーバー エクスプ ローラーでトレース テーブル] (./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	この画面には、他の画面では確認できないフィールドが表示されます。専用のクエリ ビルダー UI を使用してクエリを作成し、ログにフィルターを適用することもできます。詳細については、[サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/windowsazure/ff683677.aspx) の「テーブル リソースの操作」にある「エンティティのフィルター処理」をご覧ください。

7. 行の詳細を見るには、行の 1 つをダブルクリックします。

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>失敗した要求トレース ログの表示</h2>

失敗した要求トレース ログは、URL の書き換えや認証の問題など、IIS による HTTP 要求の処理を詳しく把握する必要がある状況で活用できます。 

Azure Web サイトでは、同じ失敗した要求トレース機能が使用されています。この機能は、IIS 7.0 以降で利用できます。ただし、ログに記録するエラーを指定するための IIS 設定にアクセスする必要はありません。失敗した要求トレースを有効にすると、すべてのエラーがキャプチャされます。 

失敗した要求トレースは Visual Studio を使用して有効にできますが、それらを Visual Studio で表示することはできません。これらのログは XML ファイル形式になっています。ストリーミング ログ サービスで監視されるのは、プレーンテキスト モードでの読み取りが可能と判断された次のファイルのみです:*.txt*、*.html*、*.log* の各ファイル

失敗した要求トレース ログは、ブラウザーから FTP で直接表示できるほか、FTP ツールを使ってローカル コンピューターにダウンロードした後、ローカルで表示することもできます。このセクションでは、ブラウザーで直接閲覧する方法を説明します。

1. **サーバー エクスプローラー**から開いた **[Azure の Web サイト]** ウィンドウの **[構成]** タブで、**[失敗した要求トレース]** を **[オン]** に変更し、**[保存]** をクリックします。

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Web サイトが表示されているブラウザー ウィンドウのアドレス バーで、余分な文字を URL に追加し、Enter キーを押して 404 エラーを発生させます。

	これにより、失敗した要求トレース ログが作成されます。そのログを表示またはダウンロードする方法については、以降の手順で説明します。

2. Visual Studio で、**[Azure の Web サイト]** ウィンドウの **[構成]** タブにある **[管理ポータルで開く]** をクリックします。

3. 管理ポータルの **[ダッシュボード]** をクリックし、**[概要]** セクションにある **[展開資格情報のリセット]** をクリックします。

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. 新しいユーザー名とパスワードを入力します。

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. 管理ポータルの **[ダッシュボード]** タブで F5 キーを押してページを最新の情報に更新し、下へスクロールして **[展開 / FTP ユーザー]** を表示します。ユーザー名に、プレフィックスとしてサイト名が付加されていることに注目してください。**ログインする際は、ここに表示されている完全なユーザー名とそのサイト名プレフィックスを使用する必要があります。**

5. 新しいブラウザー ウィンドウで、**[FTP ホスト名]** (Web サイトの管理ポータル ページの **[ダッシュボード]** タブ) に表示されている URL に移動します。**[FTP ホスト名]** は、**[概要]** セクションの **[デプロイ / FTP ユーザー]** の近くに表示されます。

6. 先ほど作成した FTP 資格情報を使用してログインします (ユーザー名のサイト名プレフィックスを含めること)。

	サイトのルート フォルダーがブラウザーに表示されます。

6.  *LogFiles* フォルダーを開きます。

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. W3SVC に数値の付いた名前のフォルダーを開きます。

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	このフォルダーには、失敗した要求トレースを有効にした後で記録されたすべてのエラーの XML ファイルに加え、ブラウザーで XML の体裁を設定するための XSL ファイルが格納されています。

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. トレース情報を確認する失敗した要求の XML ファイルをクリックします。

	次の図は、サンプル エラーのトレース情報を部分的に示したものです。

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>次のステップ</h2>

Azure Web サイトで作成されたログは Visual Studio を使って簡単に参照できることが確認できました。次のセクションでは、関連トピックに関する他のリソースへのリンクを紹介します。

* Azure Web サイトのトラブルシューティング
* Visual Studio でのデバッグ 
* Azure でのリモート デバッグ
* ASP.NET アプリケーションでのトレース
* Web サーバーのログの分析
* 失敗した要求トレース ログの分析
* クラウド サービスのデバッグ

### Azure Web サイトのトラブルシューティング

Azure Web サイト (WAWS) のトラブルシューティングの詳細については、以下のリソースをご覧ください。

* [Web サイトの監視方法](/ja-jp/manage/services/web-sites/how-to-monitor-websites/)
* [Investigating Memory Leaks in Azure Web Sites with Visual Studio 2013 (Visual Studio 2013 を使用した Azure の Web サイトでのメモリ リークの調査)](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx)。マネージされるメモリの問題の分析に役立つ Visual Studio の機能に関する Microsoft ALM のブログ記事
* [Windows Azure Websites online tools you should know about (知っておくべき Windows Azure Web サイトのオンライン ツール)](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/)。Amit Apple によるブログの投稿です。

具体的なトラブルシューティングについての質問は、次のいずれかのフォーラムで投稿してください。

* [ASP.NET サイト上の Azure フォーラム](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [MSDN 上の Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/)。
* [StackOverflow.com](http://www.stackoverflow.com)。

### Visual Studio でのデバッグ 

Visual Studio のデバッグ モードの使い方については、MSDN のトピック [Visual Studio でのデバッグ](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) と [Visual Studio 2010 でのデバッグのヒントに関するページ](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx) をご覧ください。

### Azure でのリモート デバッグ

Azure Web サイトと Web ジョブのリモート デバッグの詳細については、以下のリソースをご覧ください。

* [Azure の Web サイトにリモート デバッグの概要] (/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)。
* [Introduction to Remote Debugging Azure Web Sites part 2 - Inside Remote debugging (Azure の Web サイトにおけるリモート デバッグの概要 2 - リモート デバッグの内部処理)](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduction to Remote Debugging on Azure Web Sites part 3 - Multi-Instance environment and GIT (Azure の Web サイトにおけるリモート デバッグの概要 3 - マルチインスタンス環境と GIT)](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs Debugging (Web ジョブのデバッグ) (ビデオ)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Web サイトで Azure Web API またはモバイル サービス バックエンドを使用し、デバッグを実行する必要がある場合は [Visual Studio での .NET バックエンドのデバッグに関するページ](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx) をご覧ください。

### ASP.NET アプリケーションでのトレース

ASP.NET トレースに関しては、最新かつ必要な情報をすべて網羅した解説がインターネットには存在しません。そのため、過去に作成された入門者向けの資料を参考にするのが最善の方法となります。MVC がまだ存在していなかったために Web フォームを想定して書かれていますが、具体的な問題については、最新のブログで情報を補うことができます。たとえば、以下のリソースが参考になります。

* [監視と利用統計情報 (Azure での実際のクラウド アプリケーションのビルド) に関するページ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。<br> 
  Azure クラウド アプリケーションをトレースするためのベスト プラクティスを掲載した E-Book の章。
* [ASP.NET トレース](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  最新とは言えませんが、基本的な事柄がわかりやすくまとめられています。
* [トレース リスナー](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  トレース リスナーについて書かれていますが、[WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx) には触れていません。
* [チュートリアル:ASP.NET トレースと System.Diagnostics トレースの統合](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  古い情報ですが、入門記事では扱っていないような詳しい情報が記載されています。
* [ASP.NET MVC Razor ビューでのトレース](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Razor ビューでのトレースに加え、MVC アプリケーションでハンドルされない例外をすべてログに記録するためのエラー フィルターの作成方法についても説明されています。Web フォーム アプリケーションで、ハンドルされない例外をすべてログに記録する方法については、MSDN の「[エラー ハンドラーの完全なコード例](http://msdn.microsoft.com/library/bb397417.aspx)」で紹介されている Global.asax サンプルをご覧ください。MVC または Web フォームで、特定の例外をログに記録すると共に、既定のフレームワークの処理はそのまま活かしておく必要がある場合、例外を捕捉してから再スローする方法を利用できます。その例を次に示します。

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  このチュートリアルで Visual Studio を使って行ったことをコマンド ラインで行う方法が解説されています。[Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) は、ASP.NET アプリケーションをデバッグするためのツールです。 
* [David Ebbo による Azure の Web サイトのログ記録と診断の使用](http://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) および [David Ebbo による Azure の Web サイトからストリーミング ログ](http://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman と David Ebbo によるビデオ。

エラーをログに記録する方法としては、独自のトレース コードを記述する以外にも、[ELMAH](http://nuget.org/packages/elmah/) のようなオープン ソースのログ記録フレームワークを使う方法があります。詳細については、[Scott Hanselman が ELMAH についてまとめたブログ記事](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)をご覧ください。

Azure からストリーミング ログを取得する必要がある場合、必ずしも ASP.NET や System.Diagnostics トレースを使う必要はありません。Azure Web サイトのストリーミング ログ サービスは、 *LogFiles* フォルダーに見つかったすべての *.txt* ファイル、*.html* ファイル、*.log* ファイルをストリーミングします。したがって、Web サイトのファイル システムに書き込む独自のログ記録システムを作成することもできます。必要なファイルが自動的にストリーミングされ、ダウンロードされます。必要な作業は、 *d:\home\logfiles* フォルダーにファイルを作成するアプリケーション コードを記述するだけです。 

### Web サーバーのログの分析

Web サーバーのログの分析の詳細については、次のリソースをご覧ください。

* [Log Parser](http://www.microsoft.com/ja-jp/download/details.aspx?id=24659)<br/>
  Web サーバーのログ (*.log* ファイル) に記録されているデータを表示するためのツールです。
* [Troubleshooting IIS Performance Issues or Application Errors using LogParser (IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする)](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Web サーバーのログを分析する際に活用できる Log Parser ツールについて基本的な事柄が説明されています。
* [LogParser の使用に関して Robert McMurray が執筆したブログ記事](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP ステータス コード](http://support.microsoft.com/kb/943891)

### 失敗した要求トレース ログの分析

失敗した要求トレース ログの活用方法については、Microsoft TechNet Web サイトの「[Using Failed Request Tracing (失敗した要求トレースの使用)](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing)」セクションなどが参考になります。ただし、このドキュメントで扱う内容は、失敗した要求トレースを IIS で構成する作業が主体です。この作業を Azure Web サイトで行うことはできません。

### クラウド サービスのデバッグ

Web サイトではなく Azure クラウド サービスをデバッグする場合、[クラウド サービスのデバッグに関するページ](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx)をご覧ください。




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/



<!--HONumber=42-->
