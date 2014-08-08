<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="ステップ 2: ダウンロードと実行" pageTitle="Azure での ASP.NET 多層 Web アプリケーション - ステップ 2: ダウンロードと実行" metaKeywords="Azure チュートリアル, 電子メール サービス アプリケーションのデプロイ, 電子メール サービスの発行" description="Azure 向け開発用にコンピューターを構成し、電子メール サービス アプリケーションをデプロイする方法について説明する 2 番目のチュートリアル。" metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Email Service アプリケーションの構成とデプロイ - 2/5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Azure Email Service アプリケーションの構成とデプロイ - 2/5

この 2 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service サンプル アプリケーションをビルドしてデプロイする方法について説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][firsttutorial]を参照してください。

このチュートリアルでは、Microsoft Azure 向け開発用にコンピューターを構成する方法と、次のいずれかの製品を使用して Microsoft Azure Email Service アプリケーションを Microsoft Azure クラウド サービスに展開する方法について説明します。

* Visual Studio 2012
* Visual Studio 2012 Express for Web
* Visual Studio 2010
* Visual Web Developer Express 2010.

>[WACOM.NOTE] このチュートリアルを作成した後、Visual Studio 2013 がリリースされ、Azure 管理ポータルと SDK が更新されました。Visual Studio 2013 と最新の SDK を使用している場合に異なる操作を実行する必要がある状況では、このような注が追加されています。これらの注は 2014 年 3 年に加筆されたもので、改定後の手順は、SDK のバージョン 2.3 を使用してテスト済みです。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けの開発を完全に無料で始めることができます。

このチュートリアルでは、次のことについて説明します。

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを構成する方法
* ローカル コンピューター上で Azure Email Service アプリケーションを構成してテストする方法
* Azure にアプリケーションを発行する方法
* Visual Studio または Azure ストレージ エクスプローラーを使用して、Azure のテーブル、キュー、BLOB を表示および編集する方法
* トレース機能を構成し、トレース データを表示する方法
* ワーカー ロール インスタンスの数を増やすことによって、アプリケーションの規模を拡張する方法

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。<a href="/ja-jp/pricing/free-trial/" target="_blank">無料評価版アカウント</a>を作成するか、<a href="/ja-jp/pricing/member-offers/msdn-benefits/" target="_blank">MSDN サブスクライバーの特典を有効にする</a>ことができます。</p>
</div>
<br />

### チュートリアル セグメント

- [開発環境を設定する][]
- [無料 Azure アカウントを設定する][]
- [Azure のストレージ アカウントの作成][]
- [Azure ストレージ エクスプローラーをインストールする][]
- [クラウド サービスを作成する][]
- [完成したソリューションをダウンロードして実行する][]
- [Visual Studio で開発ストレージを表示する][]
- [Azure ストレージを使用するためにアプリケーションを構成する][]
- [Azure にアプリケーションを展開する][]
- [ステージング環境から運用環境にアプリケーションを昇格する][]
- [SendGrid を使用するようにアプリケーションを構成する][]
- [トレース データを構成および表示する][]
- [増加した負荷の処理用に別のワーカー ロール インスタンスを追加する][]


>[WACOM.NOTE] SDK のインストールに関する以下のセクションでは、Visual Studio 2013 を使用している場合の正しいリンクは次のとおりです。[http://go.microsoft.com/fwlink/?LinkID=324322](http://go.microsoft.com/fwlink/?LinkID=324322)。


[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]



<h2><a name="createWASA"></a><span class="short-header">ストレージ アカウントの作成</span>Azure のストレージ アカウントの作成</h2>

Visual Studio でサンプル アプリケーションを実行するとき、Azure 開発ストレージで、またはクラウド内の Azure のストレージ アカウントで、テーブル、キュー、BLOB にアクセスできます。開発ストレージでは、SQL Server Express の LocalDB データベースを使用して、クラウド内での Azure のストレージの機能をエミュレートします。このチュートリアルでは、開発ストレージの使用から始めます。その後、Visual Studio での実行時にクラウド ストレージ アカウントを使用するようにアプリケーションを構成する方法について説明します。チュートリアルのこのセクションでは、Azure のストレージ アカウントを作成します。後のセクションで、Visual Studio での実行時にこのアカウントを使用するようにアプリケーションを構成します。

1. ブラウザーで、[Azure 管理ポータル][NewPortal]を開きます。

2. [Azure 管理ポータル][NewPortal]で、**[ストレージ]** をクリックし、**[新規]** をクリックします。

   ![新しいストレージ][mtas-portal-new-storage]

3. **[簡易作成]** をクリックします。
   
   ![簡易作成][mtas-storage-quick]

4. [URL] 入力ボックスに、URL のプレフィックスを入力します。

	このプレフィックスに、このボックスの下に表示されているテキストを連結したものが、ストレージ アカウントに固有の URL になります。入力したプレフィックスが他のだれかによって既に使用されている場合は、"そのストレージ名は既に使用中です" がこのボックスの上に表示されます。その場合は、別のプレフィックスを選択する必要があります。

5. リージョンのボックスで、アプリケーションをデプロイする場所を設定します。

6. **[レプリケーション]** ボックスを **[ローカル冗長]** に設定します。

	Geo レプリケーションをストレージ アカウントに対して有効にすると、1 次拠点で重大な障害が発生した場合に備えて、保存したコンテンツは 2 次拠点に複製されて、フェイルオーバーが可能になります。Geo レプリケーションには追加費用が発生する場合があります。また、テストおよび開発アカウントの場合は、一般的に Geo レプリケーションに対する課金は避けたいと考えるでしょう。詳細については、[ストレージ アカウントの管理方法に関するページ][managestorage]を参照してください。

5. **[ストレージ アカウントの作成]** をクリックします。

	次の画像では、ストレージ アカウントは `aestest3.core.windows.net` という URL で作成されます。

	![URL プレフィックスでのストレージ アカウントの作成][mtas-create-storage-url-test]

	この手順は完了までに数分かかる場合があります。完了を待つ間にここまでの手順を繰り返して、運用ストレージ アカウントを作成できます。多くの場合に便利なのは、ローカル開発用にテスト ストレージ アカウントを、Azure 上でのテスト用に別のテスト ストレージ アカウントを作成し、さらに運用ストレージ アカウントを作成することです。

5. 前の手順で作成したテスト アカウントをクリックし、**[アクセス キーの管理]** アイコンをクリックします。

	![キーの管理][mtas-manage-keys]<br/>

	![GUID キー][mtas-guid-keys]<br/>

	このチュートリアルでは**プライマリ アクセス キー**または**セカンダリ アクセス キー**が必要になります。これらのキーのいずれかをストレージ接続文字列で使用できます。

	キーが 2 つあるのは、ライブ アプリケーションのサービスを中断することなく、使用中のキーを定期的に変更できるようにするためです。使用中でないキーを再生成した後、アプリケーションの接続文字列を、再生成したキーを使用するように変更できます。キーが 1 つのみの場合では、キーの再生成時にアプリケーションでストレージ アカウントへの接続が失われることになります。画像に示しているキーは既に有効ではありません。画像のキャプチャ後に再生成されたためです。

6. 次のセクションで使用するため、これらのキーのいずれかをクリップボードにコピーします。





<h2><a name="installASE"></a><span class="short-header">ASE のインストール</span>Azure ストレージ エクスプローラーをインストールする</h2>

**Azure ストレージ エクスプローラー** (ASE) は、Azure のストレージのテーブル、キュー、BLOB を照会および更新するために使用できるツールです。このチュートリアルではこのツールを使用して、データが正しく更新されていることを確認したり、テスト データを作成したりします。

1. [Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/ )をインストールします。

	スクリーンショットでは ASE 4 を示していますが、希望する場合は ASE 5 をインストールすることもできます。

2. **Azure ストレージ エクスプローラー**を起動し、**[アカウントの追加]** をクリックします。

	![ASE アカウントの追加][mtas-ase-add]

3. テスト ストレージ アカウントの名前を入力し、前の手順でコピーしたキーを貼り付けます。

4. **[ストレージ アカウントの追加]** をクリックします。

	![ASE アカウントの追加][mtas-ase-add2]

Azure のストレージと連携するその他のツールも入手可能です。詳細については、[Microsoft Azure ストレージ エクスプローラーに関するページ](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)を参照してください。




<h2><a name="createcloudsvc"></a><span class="short-header">クラウド サービスの作成</span>クラウド サービスを作成する</h2>

1. ブラウザーで、[Azure 管理ポータル][NewPortal]を開きます。

2. **[クラウド サービス]** をクリックし、**[新規]** アイコンをクリックします。

	![クラウドの簡易作成][mtas-new-cloud]

3. **[簡易作成]** をクリックします。

4. [URL] 入力ボックスに、URL のプレフィックスを入力します。

	ストレージの URL と同様、この URL は一意であることが必要です。選択したプレフィックスが他のだれかによって既に使用されている場合は、エラー メッセージが表示されます。

5. リージョンのボックスで、アプリケーションをデプロイする場所を設定します。

	ストレージ アカウントを作成した同じリージョンでクラウド サービスを作成する必要があります。クラウド サービスとストレージ アカウントが異なるデータ センター (異なるリージョン) にあると、遅延時間が長くなり、データ センター外の帯域幅に対して課金されることになります。データ センター内の帯域幅は無料です。

	Azure アフィニティ グループには、データ センター内のリソース間の距離を最短にする機能が備わっており、それにより遅延時間を短縮できます。このチュートリアルでは、アフィニティ グループを使用しません。詳細については、[Azure でアフィニティ グループを作成する方法に関するページ](http://msdn.microsoft.com/ja-jp/library/jj156209.aspx)を参照してください。

6. [**クラウド サービスを作成する**] をクリックします。

	次の画像では、クラウド サービスは aescloud.cloudapp.net という URL で作成されます。

	![URL プレフィックスでのストレージ アカウントの作成][mtas-create-cloud]

	この手順の完了を待たずに、次の手順に進むことができます。





<h2><a name="downloadcnfg"></a><span class="short-header">ダウンロードと実行</span>完成したソリューションをダウンロードして実行する</h2>

1. [完成したソリューション](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36)をダウンロードして解凍します。

2. 昇格されたアクセス許可を使用して Visual Studio を起動します。

	コンピューティング エミュレーターを使用して Visual Studio で Microsoft Azure プロジェクトを実行するには、昇格されたアクセス許可がローカルで必要です。

	>[WACOM.NOTE] SDK 2.3 以降を使用する場合は、既定の Azure コンピューティング エミュレーターでは昇格されたアクセス許可が必要ありませんが、このプロジェクトは引き続き、元のコンピューティング エミュレーターを使用するように構成されています。したがって、昇格されたアクセス許可が必要です。
	
3. NuGet パッケージを復元します。

	ダウンロード サイズが小さくなるように、完成したソリューションは、インストール済みの NuGet パッケージのアセンブリなどのコンテンツなしで提供されます。ソリューションを開いてビルドすると、NuGet によってパッケージのすべてのコンテンツが自動的に取得されます。この処理が実行されるように、Visual Studio で NuGet パッケージの復元オプションを有効にしておく必要があります。NuGet パッケージの復元オプションをまだ有効にしていない場合は、次のステップを実行します。

	>[WACOM.NOTE] Visual Studio 2013 では、既定の設定はパッケージの自動的な復元を有効にすることですが、Visual Studio 2012 のプロジェクトを開く場合は、復元が自動的に実行されることはありません。ソリューションを開き終わるまで待ち、その後、以下の説明に従って **[NuGet パッケージの管理]** ダイアログ ボックスを開きます。右上に **[復元]** ボタンが表示されます。そのボタンをクリックしてパッケージを復元します。

		1. [ツール] メニューの [ライブラリ パッケージ マネージャー] をクリックし、[ソリューションの NuGet パッケージの管理] をクリックします。

		2. [NuGet パッケージの管理] ダイアログ ボックスの左下隅で、[設定] をクリックします。

		3. [オプション] ダイアログ ボックスの左側のウィンドウで、[パッケージ マネージャー]*の下にある [全般] を選択します。

		4. [ビルド中に足りないパッケージをダウンロードすることを NuGet に許可] を選択します。

	![NuGet パッケージの復元の有効化](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

3. **[ファイル]** メニューの **[プロジェクトを開く]** を選択し、ソリューションをダウンロードした場所に移動して、ソリューション ファイルを開きます。

3. **ソリューション エクスプローラー**で、**AzureEmailService** がスタートアップ プロジェクトとして選択されていることを確認します。

1. Ctrl + F5 キーを押して、アプリケーションを実行します。

	>[WACOM.NOTE] ソリューションを作成できるようにするには、現在の SDK アセンブリへの参照を追加する必要があります。ソリューション エクスプローラーで、[MvcWebRole] プロジェクトを右クリックし、**[追加]** をクリックしてから **[参照]** をクリックします。**[アセンブリ]** の下にある **[拡張]** をクリックします。*Microsoft.WindowsAzure.Diagnostics* と *Microsoft.WindowsAzure.ServiceRuntime* を選択し、**[OK]**をクリックします。WorkerRoleA と WorkerRoleB の各プロジェクトでも、同じ作業を実行します。

	アプリケーションのホーム ページがブラウザーに表示されます。

	![アプリケーションの実行][mtas-mailinglist1]

2. **[Create New]** をクリックします。

2. 何かテスト データを入力し、**[Create]** をクリックします。

	![アプリケーションの実行][mtas-create1]

3. いくつかの追加のメーリング リスト エントリを作成します。

	![Mailing List の Index ページ][mtas-mailing-list-index-page]

4. **[Subscribers]** をクリックし、数人の登録者を追加します。**[Verified]** を `true` に設定します。

	![登録者の Index ページ][mtas-subscribers-index-page]

4. 送信する電子メールの本文を保存した *.txt* ファイルを作成して、メッセージを追加する準備をします。その後、同じテキストといくつかの HTML (メッセージのいずれかの単語を太字や斜体にするタグなど) を保存した *.htm* ファイルを作成します。これらのファイルはこの後の手順で使用します。

4. **[Messages]** をクリックし、何かメッセージを追加します。前の手順で作成したファイルを選択します。[Scheduled Date] の値を変更しないでください。既定で 1 週間後の日付に設定されています。SendGrid を構成するまで、このアプリケーションでメッセージを送信することはできません。

   ![メッセージ作成ページ][mtas-message-create-page]
	<br/><br/>
   ![メッセージのインデックス ページ][mtas-message-index-page]

入力および表示しているデータは Azure 開発ストレージに保存されています。開発ストレージでは、SQL Server Express の LocalDB データベースを使用して、クラウド内での Azure のストレージの機能をエミュレートします。このアプリケーションで開発ストレージが使用されているのは、ダウンロード時にそのストレージを使用するようにプロジェクトを構成したためです。この設定は **AzureEmailService** プロジェクトの *.cscfg* ファイルに保存されています。*ServiceConfiguration.Local.cscfg* ファイルにより、Visual Studio でアプリケーションをローカルで実行するときに使用される設定が決まります。*ServiceConfiguration.Cloud.cscfg* ファイルにより、アプリケーションをクラウドに展開するときに使用される設定が決まります。この後で、前の手順で作成した Azure のストレージ アカウントを使用するようにアプリケーションを構成する方法について説明します。





<h2><a name="StorageExpVS"></a><span class="short-header">開発ストレージ</span>Visual Studio で開発ストレージを表示する</h2>

**サーバー エクスプローラー**の **Azure のストレージ** ブラウザーに、Azure のストレージ リソースの使いやすい読み取り専用ビューが表示されます。

1. Visual Studio で **[表示]** メニューの **[サーバー エクスプローラー]** を選択します。

2. **[Azure のストレージ]** ノードの下にある **[(開発)]** ノードを展開します。

	>[WACOM.NOTE] 現在の SDK では、**[Microsoft Azure]**、**[ストレージ]**、**[(開発)]** の順に展開 します。

3. **[テーブル]** を展開して、前の手順で作成したテーブルを表示します。

	![サーバー エクスプローラー][mtas-serverExplorer]

3. **[MailingList]** テーブルをダブルクリックします。

   ![VS ストレージ エクスプローラー][mtas-wasVSdata]

	このウィンドウにテーブル内のさまざまなスキーマがどのように表示されているかに注目します。`MailingList` エンティティには、`Description` および `FromEmailAddress` プロパティがあります。また、`Subscriber` エンティティには、`Verified` プロパティがあります (この画像では見えていませんが `SubscriberGUID` プロパティもあります)。テーブルにはすべてのプロパティに対応する列があります。指定したテーブルの行に対応するエンティティにプロパティがない場合、そのセルは空白になります。

Visual Studio のストレージ ブラウザーでは、Azure のストレージ リソースの更新も削除もできません。[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/ )では、開発ストレージ リソースの更新も削除もできます (Azure ストレージ エクスプローラーで開発ストレージを使用するように構成するには、**[ストレージ アカウントの追加]** ダイアログ ボックスで **[開発ストレージ]** チェック ボックスをオンにします)。

>[WACOM.NOTE] 最新の SDK を使用すると、**サーバー エクスプローラー**で開発ストレージを更新できます。



<h2><a name="conf4azureStorage"></a><span class="short-header">ストレージ アカウントの使用</span>Azure のストレージ アカウントを使用するようにアプリケーションを構成する</h2>

次のステップでは、Visual Studio での実行時に開発ストレージではなく Azure のストレージ アカウントを使用するようにアプリケーションを構成する方法について説明します。Visual Studio では、そのための新しい方法が SDK のバージョン 1.8 で導入されました。以前の方法では、Azure の管理ポータルから設定をコピーして貼り付けていました。次のステップでは、ストレージ アカウントの設定を構成する新しい方法を示します。

4. **ソリューション エクスプローラー**で、**AzureEmailService** プロジェクトの **[ロール]** の下にある **MvcWebRole** を右クリックし、**[プロパティ]** をクリックします。

	![[プロパティ] を右クリック][mtas-rt-prop]<br/>

5. **[設定]** タブをクリックします。**[サービス構成]** ボックスの一覧で **[ローカル]** を選択します。

6. **StorageConnectionString** エントリを選択すると、行の右端に省略記号 (**...**) ボタンが表示されます。省略記号ボタンをクリックすると、**[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

	![[プロパティ] を右クリック][mtas-elip]<br/>

7. **[ストレージ接続文字列の作成]** ダイアログで、**[サブスクリプション]** をクリックし、**[発行設定のダウンロード]** をクリックします。

	>[WACOM.NOTE] 最新の SDK では、この]ダイアログ ボックスから Azure にサインインし、使用するストレージ アカウントを選択することができます。

	![[プロパティ] を右クリック][mtas-enter]<br/>

	Visual Studio によって、既定のブラウザーの新しいインスタンスが起動され、Azure ポータルの発行設定のダウンロード ページの URL が指定されます。ポータルにログインしていない場合は、ログインを求められます。ログインしたら、ブラウザーで発行設定の保存を求められます。設定を保存した場所をメモしておきます。

   ![発行の設定][mtas-3]

1. **[ストレージ接続文字列の作成]** ダイアログで **[インポート]** をクリックし、前の手順で保存した発行設定ファイルに移動します。

1. 使用するサブスクリプションとストレージ アカウントを選択し、**[OK]** をクリックします。

	![ストレージ アカウントの選択][mtas-5]

1. `StorageConnectionString` 接続文字列に使用した同じ手順に従って `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列を設定します。

	発行設定ファイルを再度ダウンロードする必要はありません。`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列の [...] をクリックすると、**[ストレージ接続文字列の作成]** ダイアログ ボックスでは、サブスクリプション情報が記憶されていることがわかります。**[サブスクリプション]** ラジオ ボタンをクリックすると、前の手順で選択した同じ**サブスクリプション**と**アカウント名**を選択して **[OK]** をクリックするだけで済みます。

2. MvcWebRole ロールの 2 つの接続文字列に使用した同じ手順に従って、WorkerRoleA ロールと WorkerRoleB ロールの接続文字列を設定します。

### ストレージ アカウントの資格情報を手動で構成する方法

次のステップでは、ストレージ アカウントの設定を手動で構成する方法について説明します。前の手順で示した自動による方法を使用した場合は、この手順をスキップしてもかまいません。この手順の説明を読むことで、自動による方法によりバックグラウンドで実行されていた処理を確認することもできます。

1. ブラウザーで、[Azure 管理ポータル][NewPortal]を開きます。

2. **[ストレージ]** タブをクリックしてから、前の手順で作成したテスト アカウントをクリックし、**[キーの管理]** アイコンをクリックします。

   ![キーの管理][mtas-manage-keys]<br/>

   ![GUID キー][mtas-guid-keys]<br/>

3. プライマリまたはセカンダリ アクセス キーをコピーします。


4. **ソリューション エクスプローラー**で、**AzureEmailService** プロジェクトの **[ロール]** の下にある **MvcWebRole** を右クリックし、**[プロパティ]** をクリックします。

   ![[プロパティ] を右クリック][mtas-rt-prop]<br/>

5. **[設定]** タブをクリックします。**[サービス構成]** ボックスの一覧で **[ローカル]** を選択します。

6. **StorageConnectionString** エントリを選択すると、行の右端に省略記号 (**...**) ボタンが表示されます。省略記号ボタンをクリックすると、**[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

   ![[プロパティ] を右クリック][mtas-elip]<br/>

7. **[ストレージ接続文字列の作成]** ダイアログ ボックスで、**[手動で入力された資格情報]** ラジオ ボタンを選択します。ストレージ アカウント名と、ポータルからコピーしたプライマリまたはセカンダリ アクセス キーを入力します。

7. **[OK]** をクリックします。

Web ロールの設定に使用した同じ手順を使用してワーカー ロールの設定を構成するか、構成ファイルの編集により Web ロールの設定をワーカー ロールに反映できます。次のステップでは、構成ファイルの編集による方法について説明します (この手順もストレージの資格情報を手動で設定する方法に含まれます。自動による方法を使用してワーカー ロールに設定を既に反映した場合は、この手順を実行する必要はありません)。

8. **AzureEmailService** プロジェクトにある **ServiceConfiguration.Local.cscfg** ファイルを開きます。

	`MvcWebRole` の `Role` 要素の中で、Visual Studio の UI を使用して更新した設定を含む `ConfigurationSettings` 要素が表示されます。

		  <Role name="MvcWebRole">
		    <Instances count="1" />
		    <ConfigurationSettings>
		      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
		      <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
		    </ConfigurationSettings>
		  </Role>
		
	2 つのワーカー ロールの `Role` 要素には、同じ 2 つの接続文字列が表示されます。

9. `WorkerRoleA` 要素と `WorkerRoleB` 要素からこれらの 2 つの接続文字列の `Setting` 要素を削除し、`MvcWebRole` 要素から `Setting` 要素をコピーして、削除した要素があった場所に貼り付けます。

構成ファイルの詳細については、「[Azure プロジェクトの構成](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405486.aspx)」を参照してください。

### ストレージ アカウントを使用するように構成されたアプリケーションをテストする

9. Ctrl + F5 キーを押して、アプリケーションを実行します。このチュートリアルの前の手順で行ったように、**[Mailing Lists]**、**[Subscribers]**、**[Messages]** のリンクをクリックして、何らかのデータを入力します。

これで、**Azure ストレージ エクスプローラー**と**サーバー エクスプローラー**のどちらかを使用して、アプリケーションによって Azure のテーブルに入力されたデータを表示できます。

### ストレージ アカウントに入力されたデータを Azure ストレージ エクスプローラーで表示する

10. **Azure ストレージ エクスプローラー**を開きます。

11. 前の手順で入力した資格情報に対応するストレージ アカウントを選択します。

12. **[ストレージの種類]** で **[テーブル]** を選択します。

12. `MailingList` テーブルを選択し、**[クエリ]** をクリックすると、アプリケーションの **[Mailing List]** および **[Subscriber]** ページで入力したデータが表示されます。

   ![ASE][mtas-ase1]<br/>

### ストレージ アカウントに入力したデータをサーバー エクスプローラーで表示する

11. **サーバー エクスプローラー** (または**データベース エクスプローラー**) で、**[Azure のストレージ]** を右クリックし、**[新しいストレージ アカウントの追加]** をクリックします。

12. 前に説明した同じ手順に従って、ストレージ アカウントの資格情報を設定します。

13. **[Azure のストレージ]** の下にある新しいノードを展開して、Azure のストレージ アカウントに保存されたデータを表示します。

	![ASE][mtas-se3]<br/>

### Azure ストレージ エミュレーターの自動起動を無効にする (省略可能な手順)

ストレージ エミュレーターを使用しない場合は、Azure のストレージ エミュレーターの自動起動を無効にすることで、プロジェクトの起動時間を短縮し、使用するローカル リソースを節約できます。

11. **ソリューション エクスプローラー**で **AzureEmailService** クラウド プロジェクトを右クリックし、**[プロパティ]** をクリックします。

	![クラウド プロジェクトのプロパティの選択][mtas-aesp]<br/>

11. **[開発]** タブを選択します。

12. **[Azure のストレージ エミュレーターの起動]** を **[False]** に設定します。

	![ストレージ エミュレーターの自動起動の無効化][mtas-1]<br/>

	**注**: [False] に設定する必要があるのは、ストレージ エミュレーターを使用しない場合のみです。

	このウィンドウでは、アプリケーションをローカル実行するときに使用される **[サービス構成]** ファイルを **[ローカル]** から **[クラウド]** に (*ServiceConfiguration.Local.cscfg* から *ServiceConfiguration.Cloud.cscfg* に) 変更することもできます。

13. Windows システム トレイで、コンピューティング エミュレーターのアイコンを右クリックし、**[ストレージ エミュレーターのシャットダウン]** をクリックします。

	![ASE][mtas-se4]<br/>





<h2><a name="sendGrid"></a><span class="short-header">SendGrid</span>SendGrid を使用するようにアプリケーションを構成する</h2>

サンプル アプリケーションでは、SendGrid を使用して電子メールを送信します。SendGrid を使用して電子メールを送信するには、SendGrid アカウントを設定し、SendGrid 資格情報で構成ファイルを更新する必要があります。

<div class="note"><p><strong>注:</strong> SendGrid を使用したくない場合や使用できない場合は、任意の電子メール サービスに簡単に置き換えることができます。SendGrid を使用するコードは Worker ロール B の 2 つのメソッド内に分離されています。[Tutorial 5][tut5] で、別の方法を使用して電子メールを送信するために必要な変更点について説明しています。別の方法を使用する場合は、この手順をスキップして、このチュートリアルを続行してもかまいません。電子メールの実際の送信以外、アプリケーションの他のすべての機能 (Web ページ、電子メール スケジューラなど) は動作します。</p></div>

### SendGrid アカウントの作成
 
1. 「[SendGrid を使用した Microsoft Azure での電子メールの送信方法](http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid")」の指示に従って、無料アカウントにサインアップします。

### SendGrid 資格情報でワーカー ロールのプロパティを更新する
 
このチュートリアルの前の手順で、Web ロールと 2 つのワーカー ロールのストレージ アカウントの資格情報を設定するとき、ワーカー ロール B に、Web ロールまたはワーカー ロール A にはない 3 つの設定がありました。同じ UI を使用して (**[サービス構成]** ドロップダウン リストで **[クラウド]** を選択して) これらの 3 つの設定を構成できます。

次の手順ではその代替方法として、構成ファイルを編集してそれらのプロパティを設定する方法について説明します。

2. `AzureEmailService` プロジェクトで *ServiceConfiguration.Cloud.cscfg* ファイルを編集し、前の手順で取得した SendGrid ユーザー名とパスワードの値を、これらの設定がある `WorkerRoleB` 要素に入力します。次のコードは WorkerRoleB 要素のものです。

	![SendGridSettings][mtas-sg]<br/>

3. AzureMailServiceURL 設定もあります。この値には Azure クラウド サービスの作成時に選択した URL を設定します。たとえば、"http://aescloud.cloudapp.net" を設定します。

クラウド構成ファイルを更新することで、アプリケーションがクラウド内で実行されるときに使用される設定を構成することになります。ローカルでの実行中に電子メールを送信するようにアプリケーションを構成する場合は、*ServiceConfiguration.Local.cscfg* ファイルも更新する必要があります。




<h2><a name="deployAz"></a><span class="short-header">Azure へのデプロイ</span>アプリケーションを Azure にデプロイする</h2>

アプリケーションをデプロイするには、Visual Studio でパッケージを作成し、Azure の管理ポータルからアップロードできます。または、Visual Studio から直接発行することもできます。このチュートリアルでは発行による方法を使用します。

まずアプリケーションをステージング環境に発行し、後で展開をステージングから運用に昇格します。

### IP 制限を実装する

ステージングに展開すると、そのアプリケーションには URL を知っているだれもがパブリックにアクセスできるようになります。したがって、最初の手順は IP 制限を実装して、許可されていない人がそのアプリケーションを使用できないようにすることです。運用アプリケーションでは、ASP.NET メンバーシップ システムのような認証と権限承認のメカニズムを実装します。ただしこれらの機能は、セットアップ、展開、テストがシンプルになるように、サンプル アプリケーションからは省略されています。

1. `MvcWebRole` プロジェクトの root フォルダーにある *Web.Release.config* ファイルを開き、**IPAddress** 属性値 127.0.0.1 を自身の IP アドレスに置き換えます (**ソリューション エクスプローラー**で **Web.Release.config** ファイルを参照するには、*Web.config* ファイルを展開する必要があります)。

	[Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP") やその他の検索エンジンで "Find my IP" を検索することで、自身の IP アドレスを調べることができます。

	アプリケーションが発行されると、*Web.release.config* ファイルで指定した変換が適用され、クラウド内に展開されている *web.config* ファイルの IP 制限要素が更新されます。変換された *web.config* ファイルは、パッケージの作成後、*AzureEmailService\MvcWebRole\obj\Release\TransformWebConfig\transformed* フォルダーで確認できます。

### クラウド内での実行時にストレージ アカウントを使用するようにアプリケーションを構成する

このチュートリアルの前の手順で、Web ロールと 2 つの Worker ロールのストレージ アカウントの資格情報を設定するとき、アプリケーションのローカルでの実行に使用する資格情報を設定しました。次のステップでは、アプリケーションのクラウド内での実行に使用するストレージ アカウントの資格情報を設定する必要があります。

このテストの実行には、ローカルでの実行に使用しているクラウド用の同じ資格情報を使用します。運用アプリケーションを展開する場合は通常、テストに使用するものとは別のアカウントを運用に使用することになります。また、運用に関するベスト プラクティスは、診断 connectionString にストレージ接続文字列とは別のアカウントを使用することです。ただしこのテストの実行では、同じアカウントを使用します。

同じ UI を使用して (**[サービス構成]** ドロップダウン リストで **[クラウド]** を選択して) 接続文字列を構成できます。次のステップではその代替方法として、構成ファイルを編集してそれらの接続文字列を構成する方法について説明します。

1. **AzureEmailService** プロジェクトで *ServiceConfiguration.Local.cscfg* ファイルを開き、`StorageConnectionString` と `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` の `Setting` 要素をコピーします。

2. **AzureEmailService** プロジェクトで *ServiceConfiguration.Cloud.cscfg* ファイルを開き、コピーした要素を `MvcWebRole`、`WorkerRoleA`、`WorkerRoleB` の `Configuration Settings` 要素に貼り付けて、既存の `Setting` 要素を置き換えます。

2. Web ロールと 2 つのワーカー ロール要素がすべて同じ接続文字列を定義していることを確認します。

### アプリケーションを発行する

2. Visual Studio をまだ開いていない場合は、管理者として起動し、**AzureEmailService** ソリューションを開きます。

3.	**AzureEmailService** クラウド プロジェクトを右クリックし、**[発行]** を選択します。

	![パッケージ][mtas-6]

	**[Azure アプリケーションの発行]** ダイアログ ボックスが表示されます。

	![クラウド パッケージ][mtas-16]

4. 前の手順でストレージ アカウントの資格情報のインポートに自動による方法を使用した場合は、Azure サブスクリプションがドロップダウン リストにあります。そのサブスクリプションを選択し、**[次へ]** をクリックすることができます。それ以外の場合は、**[サインインして資格情報をダウンロードする]** をクリックし、「[Azure のストレージ用にアプリケーションを構成する][Azure ストレージを使用するためにアプリケーションを構成する]」の指示に従って、発行設定をダウンロードしてインポートします。

1. **[共通設定]** タブで、**[クラウド サービス]** ボックスの一覧の設定を確認します。

2. **[環境]** ボックスの一覧の **[運用]** を **[ステージング]** に変更します。

	![ダッシュボード][mtas-7]

3. **[ビルド構成]** には既定の **[リリース]** 設定を、**[サービス構成]** には **[クラウド]** をそのまま使用します。

	**[詳細設定]** タブの既定の設定はこのチュートリアルではそのまま使用します。**[詳細設定]** タブには開発やテストに便利な設定がいくつかあります。[詳細設定] タブの詳細については、「[Microsoft Azure アプリケーションの発行ウィザード](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz")」を参照してください。
   
1. **[次へ]** をクリックします。

1. ウィザードの **[概要]** 手順で、**[保存]** アイコン ([ターゲット プロファイル] ドロップダウン リストの右側に表示されているフロッピー ディスクのアイコン) をクリックして、発行設定を保存します。

	次回にアプリケーションを発行するときは、保存した設定が使用されるため、発行ウィザードの一連の手順を再度実行する必要はありません。

1. 設定を確認し、**[発行]** をクリックします。

	![発行][mtas-8]

   **[Azure のアクティビティ ログ]** ウィンドウが Visual Studio で開きます。

2. 右矢印のアイコンをクリックしてデプロイの詳細を展開します。

	![発行][mtas-11]
	<br/><br/>
	![発行][mtas-9]

	展開は完了までに 5 分程度かかる場合があります。

1. デプロイが完了したら、**Web サイトの URL** をクリックしてアプリケーションを起動します。

	![ダッシュボード][mtas-c55]

9. **[Mailing List]**、**[Subscriber]**、**[Message]** の Web ページで何かデータを入力して、アプリケーションをテストします。

	**注**: アプリケーションはテストの終了後に削除して、使用していないリソースに対して課金されないようにしてください。[Azure の無料評価版のアカウント](http://www.windowsazure.com/ja-jp/pricing/free-trial/ "free-trial account")を使用している場合、3 つのデプロイされたロールは数週間で月間の上限に達します。	Azure 管理ポータルを使用してデプロイを削除するには、クラウド サービスを選択し、ページの下部にある **[削除]** をクリックしてから、運用またはステージングのデプロイを選択します。

	![発行][mtas-19]

1. Visual Studio の [Azure のアクティビティ ログ] で、**[サーバー エクスプローラーで開く]** を選択します。

	**サーバー エクスプローラー**の **[Azure コンピューティング]** でデプロイを監視できます。**Azure アプリケーションの発行**ウィザードで **[すべてのロールのリモート デスクトップを有効にする]** を選択した場合は、ロール インスタンスを右クリックし、**[リモート デスクトップを使用して接続]** を選択できます。

	![発行][mtas-12]


<h2><a name="swap"></a><span class="short-header">運用</span>ステージング環境から運用環境にアプリケーションを昇格する</h2>

1. [Azure 管理ポータル][NewPortal]の左側のウィンドウで **[クラウド サービス]** アイコンをクリックし、目的のクラウド サービスを選択します。

2. **[スワップ]** をクリックします。

2. **[はい]** をクリックして VIP (仮想 IP) スワップを完了します。この手順は完了までに数分かかる場合があります。

	![ダッシュボード][mtas-c6]

3. スワップが完了した時点で、左側のウィンドウで **[クラウド サービス]** アイコンをクリックし、目的のクラウド サービスを選択します。

4. **[運用]** デプロイの **[ダッシュボード]** タブで、ページの右下部分にある **[概要]** セクションまで下にスクロールします。**[サイトの URL]** で GUID プレフィックスからクラウド サービスの名前に変更されていることに注目してください。

	![ダッシュボード][mtas-c7]

5. **[サイトの URL]** の下に表示されているリンクをクリックするか、ブラウザーにコピーして貼り付けて、運用環境でアプリケーションをテストします。

	ストレージ アカウントの設定を変更しなかった場合、クラウド内でアプリケーションを実行すると、アプリケーションのステージング バージョンのテスト中に入力したデータが表示されます。




<h2><a name="trace"></a><span class="short-header">トレース</span>トレース データを構成および表示する</h2>

トレースはクラウド アプリケーションのデバッグに便利なツールです。チュートリアルのこのセクションでは、トレース データの表示方法について説明します。

1. 開発ストレージではなく Azure ストレージ アカウントを使用するように診断接続文字列が構成されていることを確認します。

	このチュートリアルでの前の手順に従うと、ストレージ アカウントは同一になります。Visual Studio の UI (ロールの **[プロパティ]** の **[設定]** タブ) を使用するか、*ServiceConfiguration.\*.cscfg* を参照すると、ストレージ アカウントが同一になっていることを確認できます。
 
   **注:** ベスト プラクティスは、運用データに使用されるストレージ アカウントとは別のストレージ アカウントをデータのトレースに使用することです。ただしこのチュートリアルでは、シンプルになるように、トレースに同じアカウントを構成しています。

1. Visual Studio で、**WorkerRoleA** プロジェクトの *WorkerRoleA.cs* を開き、`ConfigureDiagnostics` を検索して、`ConfigureDiagnostics` メソッドを調べます。

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

	このコードで、`DiagnosticMonitor` は、トレース情報を 500 MB まで保存するように構成されています (500 MB を超えた後は、最も古いデータから上書きされます)。また、すべてのトレース メッセージを保存するように構成されています (LogLevel.Verbose)。`ScheduledTransferPeriod` は、トレース データを 1 分単位でストレージに転送するように構成されています。トレース データを保存するには、`ScheduledTransferPeriod` を設定する必要があります。

	ワーカー ロールと Web ロールのそれぞれの `ConfigureDiagnostics` メソッドで、Trace API の呼び出し時にデータを記録するようにトレース リスナーを構成します。詳細については、[Microsoft Azure クラウド アプリケーションでのトレースの使用に関するページ](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Windows Azure")を参照してください。

1. **サーバー エクスプローラー**で、前に追加したストレージ アカウントに対応する **WADLogsTable** (**[ストレージ]** / **yourstorageaccountname** / **[テーブル]** を展開) をダブルクリックします。[WCF Data Services フィルター](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff683669.aspx "WCF filter")を入力して、表示されるエンティティを制限できます。次の画像では、警告およびエラー メッセージのみ表示されています。

	![ダッシュボード][mtas-trc]





<h2><a name="addRole"></a><span class="short-header">ロール インスタンスの追加</span>増加した負荷の処理用に別のワーカー ロール インスタンスを追加する</h2>

Azure ロール内のコンピューティング リソースのスケーリングには 2 つの方法があります。[仮想マシンのサイズを指定する](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee814754.aspx "VM sizes")ことと、実行中の仮想マシンのインスタンス数を指定することです。

仮想マシン (VM) のサイズは、*ServiceDefinition.csdef* ファイル内の `WebRole` または `WorkerRole` 要素の `vmsize` 属性で指定します。既定の設定は `[小]` で、1 コアと 1.75 GB RAM の VM が提供されます。マルチスレッドのアプリケーションや、大量のメモリ、ディスク、帯域幅を使用するアプリケーションでは、パフォーマンスの向上のために VM のサイズを増やすことができます。たとえば `[特大]` では、8 CPU コアと 14 GB RAM の VM が提供されます。1 つのマシン上のメモリ、CPU コア、ディスク、帯域幅を増やすことは、*スケール アップ*として知られています。スケール アップの良い候補は、[非同期メソッド](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC")を使用する ASP.NET Web アプリケーションなどです。VM のサイズ別に提供されるリソースの説明については、「[クラウド サービスのサイズを構成する](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee814754.aspx "VM sizes")」を参照してください。

このアプリケーションの Worker ロール B は、電子メールの送信処理を行うため高負荷下にある限定コンポーネントです (ワーカー ロール A は、リソースを集中的に使用しないキュー メッセージの作成処理のみ行います)。ワーカー ロール B はマルチスレッドではなく、メモリ フットプリントも大きくないため、スケール アップの良い候補ではありません。ワーカー ロール B は、インスタンス数を増やすことで直線的にスケーリングできます (インスタンス数を 2 倍にすると、パフォーマンスもほぼ 2 倍になります)。コンピューティング インスタンスの数を増やすことは、*スケール アウト*として知られています。各インスタンスにはコストがあるため、スケール アウトはアプリケーションに必要なときのみ行う必要があります。

Visual Studio の UI で設定を更新するか、*ServiceConfiguration.\*.cscfg* ファイルを直接編集することで、Web ロールまたは Worker ロールをスケール アウトできます。インスタンス数はロールの**プロパティ** ウィンドウの **[構成]** タブ、および *.cscfg* ファイルの `Instances` 要素で指定します。設定を更新するときは、更新した構成ファイルをデプロイして、変更を有効にする必要があります。または、負荷の増加が一時的な場合は、Azure 管理ポータルでロール インスタンスの数を変更できます。Azure 管理 API を使用してインスタンスの数を構成することもできます。最後に、[オートスケーリング アプリケーション ブロック][autoscalingappblock]を使用して、負荷の増加に合わせて自動的にスケール アウトされるようにもできます。オートスケーリングの詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されている関連情報を参照してください。

チュートリアルのこのセクションでは、管理ポータルを使用してワーカー ロール B をスケール アウトする方法について説明しますが、その前に Visual Studio でスケール アウトする方法を紹介します。

Visual Studio でスケール アウトを行うには、クラウド プロジェクトの **[ロール]** の下にある目的のロールを右クリックし、**[プロパティ]** を選択します。

   ![[プロパティ] を右クリック][mtas-rt-prop]
 
その後、左側の **[構成]** タブを選択し、**[サービス構成]** ボックスの一覧の **[クラウド]** を選択します。

  ![インスタンス数][mtas-instanceCnt]

このタブで VM のサイズも構成できることに注目してください。

次のステップでは、Azure 管理ポータルを使用してスケール アウトする方法について説明します。

1. Azure 管理ポータルで、目的のクラウド サービスを選択し、**[スケール]** をクリックします。

1. ワーカー ロール B のインスタンスの数を増やし、**[保存]** をクリックします。

	![インスタンス数の増加][mtas-in3]

	新しい VM がプロビジョニングされるまで数分かかる場合があります。

1. **[インスタンス]** タブを選択して、アプリケーションの各ロール インスタンスを表示します。

	![インスタンスの表示][mtas-in2]




<h2><a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ</h2>

ここまで、完成したアプリケーションの構成、展開、スケーリング方法を見てきました。この後の一連のチュートリアルでは、アプリケーションを新しくビルドする方法について説明します。[次のチュートリアル][tut3]では、Web ロールを作成します。

Azure のストレージのテーブル、キュー、BLOB に関する参考情報は、[このシリーズの最終チュートリアル][tut5]の末尾に記載されています。



<div><a href="/ja-jp/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">チュートリアル 3</a></div>

[開発環境を設定する]: #setupdevenv
[無料 Azure アカウントを設定する]: #setupwindowsazure
[Azure のストレージ アカウントの作成]: #createWASA
[Azure ストレージ エクスプローラーをインストールする]: #installASE
[クラウド サービスを作成する]: #createcloudsvc
[完成したソリューションをダウンロードして実行する]: #downloadcnfg
[Visual Studio で開発ストレージを表示する]: #StorageExpVS
[Azure ストレージを使用するためにアプリケーションを構成する]: #conf4azureStorage
[Azure にアプリケーションを展開する]: #deployAz
[ステージング環境から運用環境にアプリケーションを昇格する]: #swap
[SendGrid を使用するようにアプリケーションを構成する]: #sendGrid
[トレース データを構成および表示する]: #trace
[増加した負荷の処理用に別のワーカー ロール インスタンスを追加する]: #addRole

[firsttutorial]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/

[tut3]: /ja-jp/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut5]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[NewPortal]: http://manage.windowsazure.com
[managestorage]: /ja-jp/manage/services/storage/how-to-manage-a-storage-account/
[autoscalingappblock]: /ja-jp/develop/net/how-to-guides/autoscaling/


[mtas-portal-new-storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
[mtas-storage-quick]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
[mtas-create-storage-url-test]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
[mtas-manage-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
[mtas-guid-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
[mtas-new-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
[mtas-create-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
[mtas-ase-add]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png
[mtas-ase-add2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png

[mtas-rt-prop]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
[mtas-mailinglist1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
[mtas-create1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
[mtas-serverExplorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
[mtas-wasVSdata]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
[mtas-ase1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png


[mtas-se3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png
[mtas-aesp]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
[mtas-1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
[mtas-se4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png









[mtas-c6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
[mtas-c7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png

[mtas-sg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
[mtas-trc]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
[mtas-instanceCnt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png

[mtas-in3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
[mtas-in2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png
[mtas-6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
[mtas-16]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
[mtas-7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
[mtas-8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
[mtas-9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
[mtas-11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
[mtas-12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
[mtas-c55]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png

[mtas-19]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png














