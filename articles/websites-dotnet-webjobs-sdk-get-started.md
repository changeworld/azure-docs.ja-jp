<properties pageTitle="Get Started with the Azure WebJobs SDK" metaKeywords="Azure tutorial, Azure WebJobs tutorial, Azure multi-tier tutorial, MVC tutorial, Azure blobs tutorial, Azure queues tutorial, Azure storage tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The frontend runs in a website, and the backend runs as a WebJob. The app uses Entity Framework, SQL Database, and Azure storage queues and blobs." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Get Started with the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Azure Web ジョブ SDK の使用

このチュートリアルでは Web ジョブ SDK の概要を説明し、[Azure Web サイト][Azure Web サイト]内で Web ジョブ SDK を使用する多層 ASP.NET MVC アプリケーションを作成する方法を示します。このアプリケーションでは、[Azure SQL Database][Azure SQL Database]、[Azure BLOB サービス][Azure BLOB サービス]、および [Azure Queue サービス][Azure Queue サービス]を使用します。

このサンプル アプリケーションは、広告の掲示板です。ユーザーは、テキストを入力し、画像をアップロードして広告を作成します。広告の一覧には縮小表示画像が表示されます。広告を選択すると、フルサイズ画像と詳細が表示されます。スクリーンショットを次に示します。

![Ad list][Ad list]

MSDN コード ギャラリーから、対象の [Visual Studio プロジェクトをダウンロード][Visual Studio プロジェクトをダウンロード]できます。

## 目次

このチュートリアルに含まれるセクションは次のとおりです。

-   [前提条件][前提条件]
-   [学習内容][学習内容]
-   [Web ジョブ SDK][Web ジョブ SDK]
-   [アプリケーションのアーキテクチャ][アプリケーションのアーキテクチャ]
-   [開発環境を設定する][開発環境を設定する]
-   [アプリケーションをビルド、実行、およびデプロイする][アプリケーションをビルド、実行、およびデプロイする]
-   [アプリケーションを最初から作成する][アプリケーションを最初から作成する]
-   [アプリケーション コードを確認する][アプリケーション コードを確認する]
-   [トラブルシューティング][トラブルシューティング]
-   [次のステップ][次のステップ]

## <span id="prerequisites"></span></a>前提条件

このチュートリアルは、Visual Studio で [ASP.NET MVC][ASP.NET MVC] または [Web フォーム][Web フォーム] プロジェクトを操作する方法を理解していることを前提としています。サンプル アプリケーションでは MVC を使用しますが、チュートリアルのほとんどは Web フォームにも当てはまります。

このチュートリアルの手順は、次のどちらの製品でも使用できます。

-   Visual Studio 2013
-   Visual Studio 2013 Express for Web

これらの製品をお持ちでない場合、Azure SDK をインストールすると Visual Studio 2013 Express for Web が自動的にインストールされます。

[WACOM.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <span id="learn"></span></a>学習内容

このチュートリアルでは、次のタスクの実行方法を示します。

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する。
-   関連付けられた Web プロジェクトをデプロイしたときに、Azure Web ジョブとして自動的にデプロイするコンソール アプリケーション プロジェクトを作成する。
-   開発コンピューター上で Web ジョブ SDK バックエンドをローカルにテストする。
-   Web ジョブ バックエンドと共にアプリケーションを Azure Web サイトに発行する。
-   ファイルを Azure BLOB サービスにアップロードして保存する。
-   Azure Web ジョブ SDK を使用して Azure ストレージ キューと BLOB を操作する。

## <span id="webjobssdk"></span></a>Web ジョブ SDK の概要

[Web ジョブ][Web ジョブ]は、お使いの Web サイトと同じコンテキストでプログラムまたはスクリプトを実行できる Azure Websites の機能の 1 つです。プログラムを Web ジョブとして実行するために Web ジョブ SDK を使用する必要はありません。Web ジョブ SDK の目的は、Azure ストレージ キュー、BLOB、テーブル、および Service Bus キューと組み合わせて動作するコードを記述するタスクを簡素化することです。

Web ジョブ SDK には次のコンポーネントが含まれています。

-   **NuGet パッケージ**。Visual Studio プロジェクトに追加する NuGet パッケージは、コードで Azure ストレージ サービスまたは Service Bus キューを操作するために使用するフレームワークを提供します。
-   **ダッシュボード**。Web ジョブ SDK の一部は、Azure Websites に含まれており、NuGet パッケージを使用して記述するプログラム向けに豊富な監視および診断機能を提供します。これらの監視および診断機能を使用するためにコードを記述する必要はありません。

Web ジョブ SDK NuGet パッケージを使用するプログラムはどこでも実行できます。Azure Web ジョブとして実行する必要はありません。ただし、ダッシュボードは Azure Web サイトのサイト拡張機能としてのみ使用可能です。たとえば、開発コンピューター上で Web ジョブ SDK をローカルに使用するプログラムを実行することもできれば、Azure クラウド サービス worker ロールでプログラムを実行することもできます。その後、Web ジョブ SDK プログラムが使用しているものと同じストレージ アカウントを使用するように Azure Web サイトの Web ジョブ SDK ダッシュボードを構成することで、監視情報を取得できます。

このチュートリアルが作成されている時点では、Web ジョブ SDK はベータ リリースです。運用環境での使用はサポートされていません。

### 標準的なシナリオ

ここでは、Azure Web ジョブ SDK を使用してより簡単に処理できる標準的なシナリオをいくつか取り上げます。

-   画像処理または CPU 負荷の高い作業。Web サイトの一般的な機能は、画像やビデオをアップロードする機能です。アップロード後にコンテンツを操作する必要が生じ、その作業の間ユーザーを待たせたくない場合がよくあります。
-   キューの処理。Web フロントエンドがバックエンド サービスと通信する一般的な方法は、キューを使用することです。Web サイトは、処理を完了する必要がある場合に、メッセージをキューにプッシュします。バックエンド サービスは、キューからメッセージをプルし、処理を完了します。たとえば、画像処理でキューを使用できます。ユーザーがいくつかのファイルをアップロードした後、それらをバックエンドで取り出して処理できるように、ファイル名をキュー メッセージに格納します。また、キューを使用してサイトの応答性を改善できます。たとえば、SQL データベースに直接書き込む代わりに、キューに書き込んで、完了したことをユーザーに通知します。その間、バックエンド サービスでは、待ち時間の長いリレーショナル データベースの作業を処理することができます。
-   RSS 情報集約。RSS フィードのリストを維持するサイトがある場合は、フィードからのすべての記事をバックグラウンド プロセスにプルすることができます。
-   ログ ファイルの集計やクリーンアップなどのファイルのメンテナンス。分析ジョブを実行するために、いくつかのサイトによって作成されたログ ファイルや別々の期間に作成されたログ ファイルを組み合わせる作業が必要になる場合があります。また、使用していないログ ファイルをクリーンアップするために毎週実行するタスクをスケジュール設定する場合もあります。
-   電子メールの送信など、バックグラウンド スレッドで実行したいその他の時間のかかるタスク。

Web サイトが一定時間操作されずスリープに切り替わると、時間のかかるバックグラウンド タスクが中断されるため、それを防ぐために Azure Websites の [AlwaysOn][AlwaysOn] 機能を使用できます。

### コード サンプル

Azure ストレージと組み合わせて動作する標準的なタスクを処理するコードは、シンプルです。コンソール アプリケーションで、実行するバックグラウンド タスクのメソッドを記述し、Web ジョブ SDK の属性でそれらを装飾します。`Main` メソッドは、記述したメソッドの呼び出しを調整する `JobHost` オブジェクトを作成します。Web ジョブ SDK フレームワークは、メソッド内で使用した WebJobs SDK 属性に基づいて、そのメソッドを呼び出すタイミングを認識します。次に例を示します。

        static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

`JobHost` オブジェクトは、バックグラウンドの関数セット用のコンテナーです。`JobHost` オブジェクトは、関数を監視し、それらをトリガーするイベントを待機して、トリガー イベントが発生した時点でその関数を実行します。`JobHost` メソッドを呼び出すと、コンテナー プロセスを現在のスレッドまたはバックグラウンド スレッドのどちらで実行するかを指定できます。例では、`RunAndBlock` メソッドは、現在のスレッド上でプロセスを継続的に実行しています。

この例の `ProcessQueueMessage` メソッドには `QueueTrigger` 属性があるため、新規キュー メッセージの受信がその関数のトリガーとなります。`JobHost` オブジェクトは指定されたキュー (このサンプルでは "webjobsqueue") で新規キュー メッセージを待機し、それを見つけた時点で、`ProcessQueueMessage` を呼び出します。また、`QueueTrigger` 属性も、`inputText` パラメーターをキュー メッセージの値にバインドするようフレームワークに通知します。

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
[Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

さらに、フレームワークは、`TextWriter` オブジェクトを "containername" というコンテナー内の "blobname" という BLOB にバインドします。

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
[Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

その後、関数はこれらのパラメーターを使用して、キュー メッセージの値を BLOB に書き込みます。

        writer.WriteLine(inputText);

おわかりのとおり、Web ジョブ SDK のトリガー機能とバインダー機能は、Azure ストレージ オブジェクトを操作するために記述する必要のあるコードを大幅に簡易化します。キューと BLOB のプロセスを処理するために必要な低レベルのコードは Web ジョブ SDK フレームワークによって実行されます。フレームワークは、まだ存在していないキューの作成、キューを開く、キュー メッセージの読み込み、処理の完了時のキュー メッセージの削除、まだ存在しない BLOB コンテナーの作成、BLOB への書き込みなどを処理します。

Web ジョブ SDK は Azure ストレージを操作するための多数の方法を提供しています。たとえば、`QueueTrigger` 属性で装飾したパラメーターがバイト配列またはカスタム型の場合は、JSON から自動的に逆シリアル化されます。さらに、`BlobTrigger` 属性を使用して、新しい BLOB が Azure ストレージ アカウントで作成されたときに常にプロセスがトリガーされるように指定できます (`QueueTrigger` は新しいキュー メッセージを数秒で見つけますが、`BlobTrigger` が新しい BLOB を検出するまで最大 20 分かかる場合がある点に注意してください。`BlobTrigger` は `JobHost` が起動したときは常に BLOB をスキャンし、その後、新しい BLOB を検出するために定期的に Azure ストレージ ログを確認します)。

## <span id="contosoads"></span></a>アプリケーションのアーキテクチャ

サンプル アプリケーションでは、[キューを中心とした作業パターン][Azure Queue サービス]を使用して、CPU 負荷の高い縮小表示の作成をバックエンド プロセスにオフロードします。

このアプリでは、広告を SQL データベースに格納します。その際、テーブルを作成してデータにアクセスするために Entity Framework Code First を使用します。それぞれの広告に対し、フルサイズ画像用と縮小表示画像用の 2 つの URL がデータベースに格納されます。

![Ad table][Ad table]

ユーザーが画像をアップロードすると、フロントエンド Web サイトによってその画像が [Azure BLOB][Azure BLOB サービス] に格納され、広告情報がその BLOB を示す URL と共にデータベースに格納されます。同時に、メッセージが Azure キューに書き込まれます。Azure Web ジョブとして実行されるバックエンド プロセスは、新しいメッセージを検出するため、Web ジョブ SDK を使用してキューをポーリングします。新しいメッセージが出現すると、Web ジョブはその画像の縮小表示を作成し、その広告の縮小表示 URL データベース フィールドを更新します。次の図に、アプリケーションの各パーツのやり取りを示します。

![Contoso Ads architecture][Contoso Ads architecture]

### 代替のアーキテクチャ

Web ジョブは Web サイトのコンテキストで実行され、個別に拡大縮小することはできません。たとえば、1 つの標準の Web サイト インスタンスがある場合、バックグラウンド プロセスを実行するインスタンスを 1 つだけ保持できます。それにはサーバー リソース (CPU、メモリなど) の一部が使用され、それ以外のリソースは Web コンテンツを処理するために使用できます。

トラフィックが時間帯や曜日によって変わる場合、および実行する必要のあるバックエンド処理を待機できる場合は、トラフィックの少ない時間帯に Web ジョブを実行するようにスケジュールできます。そのソリューションでも負荷が引き続き高すぎる場合は、次のような、バックエンド プログラム用の代替の環境を検討できます。

-   その目的専用の個別の Web サイトで、Web ジョブとしてプログラムを実行します。その後、フロントエンド Web サイトから独立して、バックエンド Web サイトを拡張します。
-   Azure クラウド サービス worker ロールでプログラムを実行します。このオプションを選択した場合は、クラウド サービス Web ロールまたは Web サイトでフロントエンドを実行できます。

このチュートリアルでは、フロントエンドを Web サイトで実行し、バックエンドを Web ジョブとして同じ Web サイトで実行する方法を示しています。自分のシナリオに最適な環境を選択する方法の詳細については、「[Azure の Web サイト、クラウド サービス、および仮想マシンの比較][Azure の Web サイト、クラウド サービス、および仮想マシンの比較]」を参照してください。

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## <span id="storage"></span></a>Azure ストレージ アカウントの作成

Azure Storage アカウントは、キューおよび BLOB データをクラウドに格納するためのリソースを提供します。また、ダッシュボードのログ データを格納するために Web ジョブ SDK によっても使用されます。

現実のアプリケーションでは、通常、アプリケーション データとログ データ、テスト データと運用データに別個のアカウントを作成します。このチュートリアルでは、アカウントを 1 つだけ使用します。

1.  [Azure 管理ポータル][Azure 管理ポータル]で、**[新規]**、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

2.  **[URL]** ボックスに、URL プレフィックスを入力します。

    このプレフィックスに、このボックスの下に表示されているテキストを連結したものが、ストレージ アカウントに固有の URL になります。入力したプレフィックスが既に他のユーザーによって使用されている場合は、異なるプレフィックスを選択します。

    このセクションの終わりにある画像では、ストレージ アカウントは `contosoads.core.windows.net` という URL で作成されます。

3.  **[リージョン]** ボックスの一覧で、現在の所在地に最も近いリージョンを選択します。

    この設定は、お使いのストレージ アカウントをホストしている Azure データセンターを指定します。このチュートリアルでは、選択肢によって顕著な相違は生じませんが、運用サイトの場合は、待ち時間とデータ送信料金を最小限に抑えるために、Web サーバーとストレージ アカウントを同じリージョンに設定することが望まれます。待ち時間を最小限にするには、Web サイト (後で作成する) をサイトにアクセスするブラウザーのできるだけ近くに配置します。

4.  **[レプリケーション]** ボックスを **[ローカル冗長]** に設定します。

    Geo レプリケーションをストレージ アカウントに対して有効にすると、1 次拠点で重大な障害が発生した場合に備えて、保存したコンテンツは 2 次データセンターに複製されて、フェイルオーバーが可能になります。Geo レプリケーションには追加費用が発生する場合があります。また、テストおよび開発アカウントの場合は、一般的に Geo レプリケーションに対する課金は避けたいと考えるでしょう。詳細については、[ストレージ アカウントの管理方法に関するページ][ストレージ アカウントの管理方法に関するページ]を参照してください。

5.  **[ストレージ アカウントの作成]** をクリックします。

    ![New storage account][New storage account]

## <span id="download"></span></a>アプリケーションのダウンロード

1.  [完成したソリューション][Visual Studio プロジェクトをダウンロード]をダウンロードして解凍します。

2.  Visual Studio を起動します。

3.  **[ファイル]** メニューで **[開く]**、**[プロジェクト/ソリューション]** の順に選択し、ソリューションをダウンロードした場所に移動して、ソリューション ファイルを開きます。

4.  Ctrl + Shift + B キーを押して、ソリューションをビルドします。

    既定では、*.zip* ファイルに含まれていなかった NuGet パッケージのすべての内容が Visual Studio によって自動的に復元されます。パッケージが復元されない場合は、**[ソリューションの NuGet パッケージの管理]** ダイアログの右上にある **[復元]** ボタンをクリックしてパッケージを手動でインストールします。

5.  **ソリューション エクスプローラー**で、**ContosoAdsWeb** がスタートアップ プロジェクトとして選択されていることを確認します。

## <span id="configurestorage"></span></a>ストレージ アカウントを使用するためのアプリケーションの構成

1.  ContosoAdsWeb プロジェクトでアプリケーションの *Web.config* ファイルを開きます。

    ファイルには、BLOB とキューを操作するための SQL 接続文字列と Azure ストレージ接続文字列が含まれます。

    SQL 接続文字列は [SQL Server Express LocalDB][SQL Server Express LocalDB] データベースを指しています。

    ストレージ接続文字列にはプレースホルダーがあり、次の手順でストレージ アカウント名とアクセス キーを挿入します。

    ``` prettyprint
    <connectionStrings>
    <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
    </connectionStrings>
    ```

    ストレージ接続文字列は AzureWebJobsStorage という名前になります。これは、Web ジョブ SDK が既定で使用する名前であるためです。Azure 環境では 1 つの接続文字列を設定する必要しかないため、ここでも同じ名前が使用されます。

2.  [Azure 管理ポータル][1]で、ストレージ アカウントを選択し、ページ下部の **[アクセス キーの管理]** をクリックします。

    ![Manage Access Keys button][Manage Access Keys button]

    ![Manage Access Keys dialog][Manage Access Keys dialog]

3.  接続文字列の *[accountname]* は **[ストレージ アカウント名]** ボックスの値で置き換えてください。

4.  接続文字列の *[accesskey]* は **[プライマリ アクセス キー]** ボックスの値で置き換えてください。

5.  ContosoAdsWeb プロジェクトで *Web.config* ファイルを開きます。

    このファイルには、アプリケーション データ用に 1 つとログ用に 1 つの計 2 つのストレージ接続文字列があります。このチュートリアルでは、どちらも同じアカウントを使用します。接続文字列には、前出と同じプレースホルダーがあります。

  	<pre class="prettyprint">&lt;configuration&gt;
    &lt;connectionStrings&gt;
        &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt;
    &lt;/connectionStrings&gt;
        &lt;startup&gt; 
            &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt;
    &lt;/startup&gt;
&lt;/configuration&gt;</pre>
    既定では、Web ジョブ SDK は、AzureWebJobsStorage および AzureWebJobsDashboard という名前の接続文字列を探します。代替として、希望する接続文字列を格納し、それを明示的に `JobHost` オブジェクトに渡すこともできます。

6.  Web プロジェクトで実行したのと同様に、出現する *[accountname]* と *[accesskey]* をすべて、使用しているストレージ アカウントの値で置き換えます (または、完成した接続文字列を *Web.config* ファイルから *App.config* ファイルの両方の接続文字列にコピーできます)。

7.  変更を保存します。

## <span id="run"></span></a> ローカルでアプリケーションを実行する

1.  アプリケーションの Web フロントエンドを起動するには、Ctrl キーを押しながら F5 キーを押します。

    既定のブラウザーでホーム ページが開きます (Web プロジェクトをスタートアップ プロジェクトとしているため、Web プロジェクトが実行されます)。

    ![Contoso Ads home page][Contoso Ads home page]

2.  アプリケーションの Web ジョブ バックエンドを起動するには、**ソリューション エクスプローラー**で ContosoAdsWebJob プロジェクトを右クリックし、次に、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックします。

    コンソール アプリケーション ウィンドウが開き、"Job host started" と表示され、それが実行中であることが示されます。

    ![Console application window showing that the backend is running][Console application window showing that the backend is running]

3.  ブラウザーで、**[広告を作成]** をクリックします。

4.  何らかのテスト データを入力し、アップロードする画像を選択したら、**[Create]** をクリックします。

    ![Create page][Create page]

    [Index] ページが表示されますが、新しい広告の処理が完了していないため、縮小表示はまだ表示されません。

    その間、しばらく待機した後、コンソール アプリケーション ウィンドウのログ メッセージによって、キュー メッセージが受信され処理されたことが示されます。

    ![Console application window showing that a queue message has been processed][Console application window showing that a queue message has been processed]

5.  コンソール アプリケーション ウィンドウでログ メッセージを確認した後、[インデックス] ページを更新すると縮小表示が現れます。

    ![Index ページ][Ad list]

6.  広告に対応する **[Details]** をクリックすると、フルサイズの画像が表示されます。

    ![Details page][Details page]

アプリケーションはローカル コンピューター上で実行されており、同じコンピューター上の SQL Server データベースを使用していますが、クラウド内のキューおよび BLOB と連携動作しています。次のセクションでは、クラウド データベースおよびクラウドの BLOB とキューを使用して、アプリケーションをクラウド内で実行します。

## <span id="runincloud"></span></a>アプリケーションのクラウド内での実行

アプリケーションをクラウドで実行するには、次の手順を実行します。

-   Azure Web サイトにデプロイします。Visual Studio は、新しい Azure Web サイトと SQL データベース インスタンスを自動的に作成します。
-   Azure SQL データベースとストレージ アカウントを使用するように Web サイトを構成します。

クラウド内で実行中にいくつかの広告が作成された後、Web ジョブ SDK ダッシュボードを表示して、提供される豊富な監視機能を確認できます。

### Azure Web サイトへのデプロイ

1.  ブラウザーとコンソール アプリケーション ウィンドウを閉じます。

2.  **ソリューション エクスプローラー**で ContosoAdsWeb プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure Websites]** をクリックします。

    ![Select Azure Website publish target][Select Azure Website publish target]

4.  **[既存の Web サイトを選択]** ボックスで、**[サインイン]** をクリックします。

    ![Click Sign In][Click Sign In]

5.  サインインした後、[新規] をクリックします。

    ![Click New][Click New]

6.  **[Microsoft Azure でサイトを作成]** ダイアログ ボックスで、**[サイト名]** ボックスに一意の名前を入力します。

    URL 全体は、ここに入力した内容に .azurewebsites.net を追加して構成されます (**[サイト名]** ボックスの横を参照)。たとえば、サイト名が ContosoAds の場合、URL は ContosoAds.azurewebsites.net となります。

7.  **[リージョン]** ボックスの一覧で、ストレージ アカウント用に選択したリージョンと同じリージョンを選択します。

    この設定により、Web サイトが実行される Azure データセンターが指定されます。Web サイトとストレージ アカウントを同じデータセンター内に保持することで、待ち時間とデータ送信料金を最小限に抑えることができます。

8.  **[データベース サーバー]** ボックスの一覧で、**[新しいサーバーの作成]** を選択します。

    または、サブスクリプションに既にサーバーが設定されている場合は、ドロップダウン リストからそのサーバーを選択します。

9.  **[データベースのユーザー名]** および **[データベース パスワード]** にそれぞれ管理者の該当する値を入力します。

    **[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成したサーバーを選択した場合は、既に作成されている管理ユーザー アカウントのパスワードを入力します。

10. **[作成]** をクリックします。

    ![Create site on Microsoft Azure dialog][Create site on Microsoft Azure dialog]

    Visual Studio でソリューション、Web プロジェクト、Azure Web サイト、および Azure SQL データベース インスタンスが作成されます。

11. **Web の発行**ウィザードの **[接続]** ステップで、**[次へ]** をクリックします。

    ![Connection step][Connection step]

12. **[設定]** ステップで、**[実行時にこの接続文字列を使用する]** チェック ボックスをオフにして **[次へ]** をクリックします。

    ![Settings step][Settings step]

    SQL 接続文字列を設定するために発行ダイアログを使用する必要はありません。後から Azure 環境で値を設定します。

    このページでの警告は無視します。

    -   通常、Azure で実行する際に使用するストレージ アカウントとローカルで実行する際に使用するストレージ アカウントは異なりますが、このチュートリアルでは、両方の環境で同じものを使用しています。そのため、AzureWebJobsStorage 接続文字列を変換する必要はありません。クラウド内で異なるストレージ アカウントを使用することを希望した場合でも、アプリケーションは Azure 内で実行される際には Azure 環境設定を使用するため接続文字列を変換する必要はありません。この点は後でチュートリアルの中で確認できます。

    -   このチュートリアルでは、ContosoAdsContext データベース用に使用されるデータ モデルには変更を加えないため、デプロイメントのために Entity Framework Code First Migrations を使用する必要はありません。Code First は、アプリケーションが SQL データへのアクセスを最初に試行したときに、新しいデータベースを自動的に作成します。

    このチュートリアルの場合、**[ファイル発行オプション]** の下のオプションは既定値のままでかまいません。

13. **[プレビュー]** ステップで、**[プレビューの開始]** をクリックします。

    ![Click Start Preview][Click Start Preview]

    データベースが発行されていないことに関する警告は無視します。Entity Framework Code First によってデータベースが作成されます。発行する必要はありません。

    プレビュー ウィンドウに、Web ジョブ プロジェクトのバイナリ ファイルと構成ファイルが Web サイトの *app\_data\\jobs\\continuous* フォルダーにコピーされることが示されます。

    ![WebJobs files in preview window][WebJobs files in preview window]

14. **[発行]** をクリックします。

    Visual Studio によってアプリケーションがデプロイされ、ブラウザーでホーム ページの URL が開かれます。

    次のセクションで、Azure 環境内で接続文字列を設定するまで、サイトは使用できません。以前に選択したサイトおよびデータベースの作成オプションに応じて、エラー ページまたはホーム ページが表示されます。

### Azure SQL データベースとストレージ アカウントを使用するように Web サイトを構成する

セキュリティ上のベスト プラクティスとして、[接続文字列などの機密情報をソース コード リポジトリに格納されるファイルに含めるのは避ける][接続文字列などの機密情報をソース コード リポジトリに格納されるファイルに含めるのは避ける]必要があります。Azure には、そのための方法が用意されています。接続文字列とその他の設定値を Azure 環境内で設定できます。その後、アプリケーションを Azure 内で実行するときに、ASP.NET 構成 API が自動的にそれらの値を取り出します。このセクションでは、Azure 内で接続文字列値を設定します。

1.  ブラウザーで、Azure 管理ポータルに移動し、Contoso Ads アプリケーションをデプロイした Web サイトを選択します。

2.  **[構成]** タブをクリックし、**[接続文字列]** セクションまで下へスクロールします。

3.  DefaultConnection 接続文字列の名前を ContosoAdsContext に変更します。

    この接続文字列は、サイトおよび関連付けられたデータベースを作成したときに Azure で自動的に作成されたものであるため、既に正しい接続文字列値になっています。単に、コードで検索される名前へと変更しているだけです。

4.  AzureWebJobsStorage および AzureWebJobsDashboard という名前の 2 つの新しい接続文字列を追加します。型を [カスタム] に設定し、接続文字列値を *Web.config* および *App.config* で以前に使用した値と同じ値に設定します (必ず、アクセス キーだけでなく、接続文字列全体を含めてください。引用符は含めないでください)。

    これらの接続文字列は Web ジョブ SDK によって、アプリケーション データ用とログ用に 1 つずつ使用されます。以前に見たとおり、アプリケーション データ用の接続文字列は Web フロントエンド コードによっても使用されます。

5.  **[保存]** をクリックします。

    ![Connection strings in management portal][Connection strings in management portal]

6.  **[ダッシュボード]** タブをクリックし、**[再起動]** をクリックします。

    Web ジョブは、発行時に自動的に起動されますが、構成を変更すると停止します。再起動するには、サイトを再起動するか、または Web ジョブを再起動します。一般に、構成の変更後はサイトを再起動することをお勧めします。

7.  アドレス バーにサイトの URL が示されているブラウザー ウィンドウを更新します。

    ホーム ページが表示されます。

8.  アプリケーションをローカルで実行したときと同様に、広告を作成します。

    [インデックス] ページは最初に縮小表示なしで表示されます。

9.  数秒後、ページを更新すると、縮小表示が現れます。

    縮小表示が現れない場合は、Web ジョブが自動的に起動されていない場合があります。その場合は、Web ジョブに移動します。

### Web ジョブ SDK ダッシュボードの表示

1.  Azure 管理ポータルで、Web サイトを選択します。

2.  **\[Web ジョブ]** タブをクリックします。

3.  Web ジョブの [ログ] 列で URL をクリックします。

    ![WebJobs tab][WebJobs tab]
    新しいブラウザー タブが開き、Web ジョブ SDK ダッシュボードが表示されます。ダッシュボードには、Web ジョブが実行中であることと、Web ジョブ SDK がトリガーしたコード内の関数の一覧が表示されます。

4.  関数のいずれかをクリックすると、その実行に関する詳細が表示されます。

    ![WebJobs SDK dashboard][WebJobs SDK dashboard]

    ![WebJobs SDK dashboard][2]

    このページの **[関数の再生]** をクリックすると、Web ジョブ SDK フレームワークは関数を再度呼び出すため、その機会に、最初に関数へ渡すデータを変更することができます。

> [WACOM.NOTE] テストを終了した時点で、Web サイトと SQL データベース インスタンスを削除してください。Web サイトは無料ですが、SQL データベース インスタンスとストレージ アカウントは有料です (サイズが小さいため少額)。また、サイトを実行したままにしておくと、その URL を見つけた他のユーザーが広告を作成して表示する可能性があります。Azure 管理ポータルで、Web サイトの **[ダッシュボード]** タブに移動し、ページの下部の **[削除]** をクリックします。その後、SQL データベース インスタンスを同時に削除するためのチェック ボックスをオンにします。一時的に他のユーザーがこのサイトにアクセスできないようにするには、代わりに **[停止]** をクリックします。その場合、引き続き、SQL データベースとストレージ アカウントについては料金が生じます。同様の手順で、不要になった SQL データベースとストレージ アカウントを削除できます。

## <span id="create"></span></a>アプリケーションを最初から作成する

このセクションでは、次のタスクを実行します。

-   Web プロジェクトを使用して Visual Studio ソリューションを作成します。
-   フロントエンドとバックエンド間で共有されるデータ アクセス レイヤーのクラス ライブラリ プロジェクトを追加します。
-   バックエンドのコンソール アプリケーション プロジェクトを追加し、Web ジョブのデプロイメントを有効にします。
-   NuGet パッケージを追加します。
-   プロジェクト参照を設定します。
-   アプリケーション コードと構成ファイルを、チュートリアルの以前のセクションで作業したダウンロード済みのアプリケーションからコピーします。
-   コード内で Azure の BLOB とキューおよび Web ジョブ SDK を操作する部分を確認します。

### Web プロジェクトおよびクラス ライブラリ プロジェクトを使用して Visual Studio ソリューションを作成する

1.  Visual Studio で、**[ファイル]** メニューから **[新規]**、**[プロジェクト]** の順に選択します。

2.  **\[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]**、**[Web]**、**[ASP.NET Web アプリケーション]** の順にクリックします。

3.  プロジェクトに ContosoAdsWeb と名前を付け、ソリューションに ContosoAdsWebJobsSDK と名前を付けて (ダウンロードしたソリューションと同じフォルダーに入れる場合は、ソリューション名を変更します)、**[OK]** をクリックします。

    ![新しいプロジェクト][新しいプロジェクト]

4.  **[新しい ASP.NET プロジェクト]** ダイアログで、MVC テンプレートを選択し、**[Microsoft Azure]** の下の **[クラウドでのホスト]** チェック ボックスをオフにします。

    **[クラウドでのホスト]** を選択すると、Visual Studio は新しい Azure Web サイトと SQL データベースを自動的に作成できるようになります。これらは以前に作成済みのため、ここで、プロジェクトの作成時に再度作成する必要はありません。新しいサイトとデータベースを作成する場合は、チェック ボックスをオンにします。その後、以前にアプリケーションをデプロイしたときと同じ方法で、新しい Web サイトと SQL データベースを構成できます。

5.  **[認証の変更]** をクリックします。

    ![Change Authentication][Change Authentication]

6.  **[認証の変更]** ダイアログで、**\[認証なし]** をクリックし、**[OK]** をクリックします。

    ![認証なし][認証なし]

7.  **[新しい ASP.NET プロジェクト]** ダイアログで **[OK]** をクリックします。

    Visual Studio によって、ソリューションと Web プロジェクトが作成されます。

8.  **ソリューション エクスプローラー**で (プロジェクトではなく) ソリューションを右クリックし、**[追加]**、**\[新しいプロジェクト]** の順に選択します。

9.  **[新しいプロジェクトの追加]** ダイアログ ボックスで、**[Visual C#]**、**[Windows デスクトップ]**、**[クラス ライブラリ]** テンプレートの順にクリックします。

10. プロジェクトに *ContosoAdsCommon* という名前を付けて **[OK]** をクリックします。

    このプロジェクトには、Entity Framework コンテキストおよびフロントエンドとバックエンドの両方が使用するデータ モデルが含まれます。代替として、Entity Framework に関連するクラスを Web プロジェクトに定義し、Web ジョブ プロジェクトからそのプロジェクトを参照することもできます。ただしその場合、Web ジョブ プロジェクトは、必要としない Web アセンブリへの参照を含むことになります。

### Web ジョブのデプロイメントが有効なコンソール アプリケーション プロジェクトの追加

1.  Web プロジェクト (ソリューションまたはクラス ライブラリ プロジェクトではなく) を右クリックし、**[追加]**、**[新しい Azure Web ジョブ プロジェクト]** の順にクリックします。

    ![New Azure WebJob Project menu selection][New Azure WebJob Project menu selection]

2.  **[Azure Web ジョブの追加]** ダイアログで、**[プロジェクト名]** と **[Web ジョブ名]** の両方に「ContosoAdsWebJob」と入力します。**[Web ジョブ実行モード]** は **[連続的に実行]** に設定したままにします。

3.  **[OK]** をクリックします。

    Visual Studio は、Web プロジェクトがデプロイされたときに常に Web ジョブとしてデプロイするように構成されたコンソール アプリケーションを作成します。そのために、プロジェクトの作成後に次のタスクが実行されました。

    -   *webjob-publish-settings.json* ファイルが Web ジョブ プロジェクトの Properties フォルダーに追加されました。
    -   *webjobs-list.json* ファイルが Web プロジェクトの Properties フォルダーに追加されました。
    -   Microsoft.Web.WebJobs.Publish NuGet パッケージが Web ジョブ プロジェクトにインストールされました。

    これらの変更の詳細については、「[How to Deploy WebJobs by using Visual Studio (Visual Studio を使用した Web ジョブのデプロイ方法)][How to Deploy WebJobs by using Visual Studio (Visual Studio を使用した Web ジョブのデプロイ方法)]」を参照してください。

### NuGet パッケージの追加

最初に、Web ジョブ SDK を Web ジョブ プロジェクトにインストールします。

1.  ソリューションの **[NuGet パッケージの管理]** ダイアログを開きます。

2.  左側のウィンドウで、**[オンライン]** を選択します。

3.  **[安定版のみ]** を **[プレリリースを含む]** に変更します。

4.  *Microsoft.Azure.WebJobs* NuGet パッケージを見つけ、それを ContosoAdsWebJob プロジェクトにインストールします。

    ![Find WebJobs SDK package][Find WebJobs SDK package]

    ![Install WebJobs SDK package only in WebJob project][Install WebJobs SDK package only in WebJob project]

    これにより、別の Web ジョブ SDK パッケージ *Microsoft.Jobs.Core* を含め、従属するパッケージもインストールされます (個別の DLL でユーザー関数を作成する場合のみ、他の Web ジョブ SDK パッケージを個別に使用します。このチュートリアルでは、すべてのコードをコンソール アプリケーションで記述します)。Web プロジェクトおよび Web ジョブ プロジェクト内でキューと BLOB を操作するには Azure ストレージ クライアント ライブラリ (SCL) が必要です。

Azure ストレージ クライアント ライブラリ (SCL) NuGet パッケージは、Web ジョブ SDK の従属物として Web ジョブ プロジェクトに自動的にインストールされます。ただし、Web プロジェクトで BLOB とキューを操作するには、これも使用する必要があります。

1.  左側のウィンドウで、**[インストール済みのパッケージ]** を選択します。

2.  *Azure Storage* パッケージを見つけ、**[管理]** をクリックします。

3.  **[プロジェクトの選択]** ボックスで、**[ContosoAdsWeb]** チェック ボックスをオンにし、**[OK]** をクリックします。

3 つのプロジェクトすべてが Entity Framework を使用して SQL データベース内のデータを操作します。

1.  左側のウィンドウで、**[オンライン]** を選択します。

2.  **[プレリリースを含む]** を **[安定版のみ]** に変更します。

3.  *EntityFramework* NuGet パッケージを見つけて、3 つのプロジェクトすべてにインストールします。

### プロジェクト参照の設定

Web と Web ジョブ プロジェクトはどちらも SQL データベースと連携して動作するため、両方に ContosoAdsCommon プロジェクトへの参照が必要です。

1.  ContosoAdsWeb プロジェクトで、ContosoAdsCommon プロジェクトの参照を設定します。(ContosoAdsWeb プロジェクトを右クリックし、**[追加]**、**[参照]** の順にクリックします。**[参照マネージャー]** ダイアログ ボックスで、**[ソリューション]**、**[プロジェクト]**、**[ContosoAdsCommon]** の順に選択し、**[OK]** をクリックします。)

2.  ContosoAdsWebJob プロジェクトで、ContosAdsCommon プロジェクトへの参照を設定します。

Web ジョブ プロジェクトには、画像を操作し、接続文字列にアクセスするための参照が必要です。

1.  ContosoAdsWebJob プロジェクトで、`System.Drawing` および `System.Configuration` への参照を設定します。

### コードと構成ファイルの追加

このチュートリアルでは、[スキャフォールディングを使用した MVC コントローラーおよびビューの作成][ASP.NET MVC]方法、[SQL Server データベースで動作する Entity Framework コードの作成][SQL Server データベースで動作する Entity Framework コードの作成]方法、または [ASP.NET 4.5 での非同期プログラミングの基礎][ASP.NET 4.5 での非同期プログラミングの基礎]については説明していません。そのため、実行する必要のある残りの作業は、コードと構成ファイルをダウンロード済みのソリューションから新しいソリューションへコピーすることだけです。コピーの実行後に、以降のセクションでコードの重要な部分について説明します。

プロジェクトまたはフォルダーにファイルを追加するには、プロジェクトまたはフォルダーを右クリックし、**[追加]**、**[既存の項目]** の順にクリックします。目的のファイルを選択し、**[追加]** をクリックします。既存のファイルを置き換えるかどうかをたずねるメッセージが表示されたら、**[はい]** をクリックします。

1.  ContosoAdsCommon プロジェクトで、*Class1.cs* ファイルを削除します。その場所に、ダウンロードしたプロジェクトから次のファイルを追加します。

    -   *Ad.cs*
    -   *ContosoAdscontext.cs*
    -   *BlobInformation.cs*

2.  ContosoAdsWeb プロジェクトで、ダウンロードしたプロジェクトから次のファイルを追加します。

    -   *web.config*
    -   *Global.asax.cs*
    -   *Controllers* フォルダー: *AdController.cs*
    -   *Views\\Shared* フォルダー: *\_Layout.cshtml* ファイル。
    -   *Views\\Home* フォルダー: *Index.cshtml*。
    -   *Views\\Ad* フォルダー (最初にフォルダーを作成します): 5 つの *.cshtml* ファイル。

3.  ContosoAdsWebJob プロジェクトで、ダウンロードしたプロジェクトから次のファイルを追加します。

    -   *App.config* (ファイルの種類のフィルターを **[すべてのファイル]** に変更します)
    -   *Program.cs*

これで、前の説明に従って、アプリケーションをビルド、実行、およびデプロイできます。ただし、その前に、デプロイ先の最初の Web サイトで実行中の Web ジョブを停止します。停止しないと、すべてが同じストレージ アカウントを使用しているため、ローカルに作成されたキュー メッセージと新しい Web サイトで実行するアプリケーションによって作成されたキュー メッセージの両方が、その Web ジョブによって処理されます。

## <span id="code"></span></a>アプリケーション コードを確認する

以降のセクションでは、Web ジョブ SDK および Azure ストレージの BLOB とキューの操作に関連するコードについて説明します。Web ジョブ SDK 固有のコードについては、[「Program.cs」のセクション][「Program.cs」のセクション]を参照してください。

### ContosoAdsCommon - Ad.cs

Ad.cs ファイルは、広告カテゴリの enum と広告情報の POCO エンティティ クラスを定義します。

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### ContosoAdsCommon - ContosoAdsContext.cs

ContosoAdsContext クラスは、Entity Framework によって SQL データベースに格納される DbSet コレクションで Ad クラスを使用することを指定します。

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

このクラスには 2 つのコンストラクターがあります。1 つ目のコンストラクターは Web プロジェクトによって使用され、Web.config ファイルまたは Azure ランタイム環境に格納される接続文字列の名前を指定します。2 つ目のコンストラクターは、実際の接続文字列を渡すために使用します。これは、Web ジョブ プロジェクトで必要になります。その理由は、Web ジョブ プロジェクトが Web.config ファイルを持たないためです。この接続文字列がどこに格納されるかについては既に説明しました。後のセクションで、DbContext クラスを初期化するときに接続文字列がどのように取得されるかについて説明します。

### ContosoAdsCommon - BlobInformation.cs

`BlobInformation` クラスは、画像 BLOB に関する情報をキュー メッセージに格納するために使用されます。

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }
            
            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }

### ContosoAdsWeb - Global.asax.cs

`Application_Start` メソッドから呼び出されるコードにより、*images* BLOB コンテナーと *images* キューが存在しない場合はこれらが作成されます。これにより、新しいストレージ アカウントを使用して起動するたびに、必要な BLOB コンテナーとキューが自動的に作成されます。

次に、*Web.config* ファイルまたは Azure ランタイム環境からのストレージ接続文字列を使用してストレージ アカウントへのアクセスが取得されます。

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

次に、*images* BLOB コンテナーの参照が取得され、コンテナーがまだ存在していない場合はこれが作成され、新しいコンテナーのアクセス許可が設定されます。既定では、新しいコンテナーは、ストレージ アカウント資格情報を持つクライアントのみに BLOB へのアクセスを許可します。Web サイトで画像 BLOB を指し示す URL を使用して画像を表示できるようにするには、BLOB をパブリックに設定する必要があります。

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

似たようなコードを使用して、*blobnamerequest* キューの参照を取得し、新しいキューを作成できます。この場合、アクセス許可の変更は必要ありません。後の [ResolveBlobName][ResolveBlobName] セクションで、Web アプリケーションの書き込み先のキューが縮小表示を生成するためではなく、BLOB 名を取得するためにだけ使用されている理由を説明します。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

*\_Layout.cshtml* ファイルは、アプリ名をヘッダーとフッターに設定し、"Ads" メニュー エントリを作成します。

### ContosoAdsWeb - Views\\Home\\Index.cshtml

*Views\\Home\\Index.cshtml* ファイルは、ホーム ページにカテゴリ リンクを表示します。リンクは、querystring 変数の `Category` enum の整数値を Ads Index ページに渡します。

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

*AdController.cs* ファイル内では、コンストラクターによって `InitializeStorage` メソッドが呼び出され、BLOB およびキューを操作するための API を提供する Azure Storage クライアント ライブラリ オブジェクトが作成されます。

次に、*Global.asax.cs* と同様に、*images* BLOB コンテナーへの参照が取得されます。この処理中に、Web アプリに適した既定の[再試行ポリシー][再試行ポリシー]が設定されます。既定の指数関数的バックオフ再試行ポリシーでは、一時的な障害に対する反復的再試行操作によって Web アプリが 1 分以上停止する可能性があります。ここでは、再試行ごとに 3 秒待機し、再試行の回数を 3 回までとする再試行ポリシーを指定しています。

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

似たようなコードを使用して、*images* キューの参照を取得できます。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

コントローラー コードのほとんどは、DbContext クラスを使用した Entity Framework データ モデルの操作に典型的なものです。ただし、ファイルをアップロードして BLOB ストレージに保存する HttpPost `Create` メソッドは例外です。モデル バインダーは、このメソッドに [HttpPostedFileBase][HttpPostedFileBase] オブジェクトを提供します。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

ユーザーがアップロードするファイルを選択すると、ファイルがアップロードされて BLOB に保存されます。さらに、Ad データベース レコードが更新され、その BLOB を示す URL が反映されます。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

アップロード操作を実行するコードは `UploadAndSaveBlobAsync` メソッドに含まれています。このコードでは、BLOB の GUID 名が作成され、ファイルがアップロードおよび保存され、保存された BLOB への参照が返されます。

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

HttpPost `Create` メソッドは、BLOB をアップロードしてデータベースを更新した後、画像を縮小表示に変換する準備が整ったことをバックエンド プロセスに通知するキュー メッセージを作成します。

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

HttpPost `Edit` メソッドのコードも似ていますが、ユーザーが新しい画像ファイルを選択した場合に、この広告に既存の BLOB を削除する必要がある点が異なります。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

次に示すのは、広告が削除されたときに BLOB を削除するコードです。

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### ContosoAdsWeb - Views\\Ad\\Index.cshtml および Details.cshtml

*Index.cshtml* ファイルは、縮小表示を他の広告データと共に表示します。

        <img  src="@Html.Raw(item.ThumbnailURL)" />

*Details.cshtml* ファイルは、フルサイズの画像を表示します。

        <img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml および Edit.cshtml

*Create.cshtml* ファイルと *Edit.cshtml* ファイルは、フォームのエンコードを指定してコントローラーが `HttpPostedFileBase` オブジェクトを取得できるようにします。

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

`<input>` 要素は、ファイル選択ダイアログを表示するようにブラウザーに指示します。

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <span id="programcs"></span></a>ContosoAdsWebJob - Program.cs - Main メソッドと Initialize メソッド

Web ジョブが起動すると、`Main` メソッドが `Initialize` を呼び出し、Entity Framework データベース コンテキストがインスタンス化されます。その後、Web ジョブ SDK `JobHost.RunAndBlock` メソッドを呼び出し、現在のスレッド上でトリガーされた関数のシングルスレッド実行を開始します。

        static void Main(string[] args)
        {
            Initialize();
        
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
        private static void Initialize()
        {
            db = new ContosoAdsContext();
        }

### <span id="generatethumbnail"></span></a>ContosoAdsWebJob - Program.cs - GenerateThumbnail メソッド

Web ジョブ SDK は、キュー メッセージを受信したときに、このメソッドを呼び出します。メソッドは、縮小表示を作成し、縮小表示 URL をデータベースに登録します。

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }
        
            var id = blobInfo.AdId;
            Ad ad = db.Ads.Find(id);
            if (ad == null)
            {
                throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
            }
            ad.ThumbnailURL = outputBlob.Uri.ToString();
            db.SaveChanges();
        }

-   `QueueTrigger` 属性は、thumbnailrequest キューで新しいメッセージを受信したときに、このメソッドを呼び出すように Web ジョブ SDK に指示します。

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    キュー メッセージ内の `BlobInformation` オブジェクトは、自動的に `blobInfo` パラメーターへ逆シリアル化されます。メソッドが完了すると、キュー メッセージは削除されます。完了前にメソッドが失敗した場合は、キュー メッセージは削除されません。10 分間のリースが期限切れになると、メッセージは開放され、再度選択され処理されます。メッセージが常に例外を引き起こす場合、このシーケンスが無限に繰り返されることはありません。メッセージの処理試行に 5 回失敗した後、メッセージは {queuename}-poison という名前のキューに移動されます。最大試行回数は構成可能です。

-   2 つの `Blob` 属性は、BLOB にバインドされるオブジェクトを提供します。1 つは既存の画像 BLOB に、もう 1 つはメソッドが作成する新しい縮小表示 BLOB にバインドされます。

        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    BLOB 名は、キュー メッセージで受信した `BlobInformation` オブジェクトのプロパティに由来します (`BlobName` および `BlobNameWithoutExtension`)。ストレージ クライアント ライブラリの機能性を最大限活用するために、`CloudBlockBlob` クラスを使用して BLOB を操作できます。`Stream` オブジェクトを操作するために記述したコードを再利用する場合は、`Stream` クラスを使用できます。

> [WACOM.NOTE]
>
> -   Web サイトが複数の VM 上で稼働している場合、このプログラムは各マシン上で実行され、各マシンがトリガーを待機して関数の実行を試行します。一部のシナリオでは、これによっていくつかの関数が同じデータを 2 回処理する場合があるため、関数をべき等にする (同じ入力データで関数を繰り返し呼び出しても重複した結果を生成しないように記述する) 必要があります。
> -   正常なシャットダウンの実装方法の詳細については、[Web ジョブ SDK 0.3.0 ベータの発表][Web ジョブ SDK 0.3.0 ベータの発表]を参照してください。
> -   `ConvertImageToThumbnailJPG` メソッドのコード (ここでは示していません) では、簡易化のために `System.Drawing` 名前空間のクラスを使用します。ただし、この名前空間のクラスは Windows フォーム用に設計されています。これらのクラスは、Windows または ASP.NET サービスでの使用に関してサポートされていません。

### Web ジョブとクラウド サービス worker ロール

このサンプル アプリケーションの `GenerateThumbnails` メソッド内のコード量を[アプリケーションのクラウド サービス版][アプリケーションのクラウド サービス版]の worker ロールのコードと比較すると、Web ジョブ SDK が実行する作業量がわかります。クラウド サービス サンプル アプリケーション コードは、運用アプリケーションで行う作業のすべて (有害メッセージの処理など) は実行せず、それらは Web ジョブ SDK では実行されるため、実際には示される以上の利点があります。

このサンプル アプリケーションのクラウド サービス版には、次のタスクを実行する `ProcessQueueMessage` メソッドがあります。

-   データベース レコード ID をキュー メッセージから取得する。
-   データベースを読み取り、BLOB の URL を取得する。
-   画像を縮小表示に変換し、縮小表示を新しい BLOB に保存する。
-   縮小表示 BLOB URL を追加してデータベース レコードを更新する。

アプリケーションのクラウド サービス版では、レコード ID がキュー メッセージ内の唯一の情報であり、バックグラウンド プロセスは画像 URL をデータベースから取得します。アプリケーションの Web ジョブ SDK 版では、`Blob` 属性に提供できるように、キュー メッセージに画像 URL が含まれています。キュー メッセージに BLOB URL が含まれない場合は、BLOB URL を取得するためのコードをメソッド内に記述する必要があり、さらにオブジェクトを入力 BLOB と出力 BLOB にバインドするコードを記述する必要があります。Web ジョブ SDK では、そのようなコードを記述しなくても処理されます。

### worker ロールでの Web ジョブ SDK の使用方法

おわかりのとおり、Web ジョブ SDK を使用するプログラムは、Azure の Web ジョブ内で実行される必要はありません。ローカルで実行することも、worker ロール内で実行することもできます。ただし、Web ジョブ SDK ダッシュボードには、Azure Web サイトを介してのみアクセス可能です。ダッシュボードを使用するには、管理ポータルの [構成] タブで AzureWebJobsDashboard 接続文字列を設定して、使用しているストレージ アカウントに Web サイトを接続する必要があります。その後、URL [https://{websitename}.scm.azurewebsites.net/azurejobs/\#/functions][https://{websitename}.scm.azurewebsites.net/azurejobs/\#/functions] を使用してダッシュボードにアクセスできます。詳細については、「[Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用ダッシュボードへのアクセス)][Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用ダッシュボードへのアクセス)]」を参照してください。ただし、既に使用されていない接続文字列名が示されている点に注意してください。

## 次のステップ

このチュートリアルでは、バックエンド処理に Web ジョブ SDK を使用するシンプルな多層アプリケーションについて取り上げました。アプリケーションは、入門用のチュートリアルという目的から意図的にシンプルに作られています。たとえば、このアプリケーションには、[依存関係の挿入][依存関係の挿入]や[作業パターンのリポジトリと単位][作業パターンのリポジトリと単位]が実装されていません。さらに、[ログのためのインターフェイス][ログのためのインターフェイス]、データ モデルの変更を管理するための [EF Code First Migrations][EF Code First Migrations]、一時的なネットワーク エラーを管理するための [Entity Framework の接続の回復性][Entity Framework の接続の回復性]なども省略されています。

別のシナリオで Web ジョブ SDK を使用する方法を示したその他のサンプルについては、ASP.NET CodePlex プロジェクトの [AzureWebJobs][AzureWebJobs] フォルダーを参照してください。

Web ジョブを Web サイトで実行する場合は、Web サイトの場合と同様にデバッグ モードをリモートで使用してトラブルシューティングを実行できます。詳細については、「[Visual Studio での Azure の Web Sites のトラブルシューティング][Visual Studio での Azure の Web Sites のトラブルシューティング]」を参照してください。手動で Web ジョブ プロセスにアタッチする必要があります。プロセスへのアタッチ方法の詳細については、チュートリアル内の Visual Studio 2012 に関する指示を参照してください。

詳細については、「[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)][Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)]」を参照してください。

  [Azure Web サイト]: /ja-jp/documentation/articles/fundamentals-application-models/#WebSites
  [Azure SQL Database]: http://msdn.microsoft.com/library/azure/ee336279
  [Azure BLOB サービス]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Azure Queue サービス]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Ad list]: ./media/websites-dotnet-webjobs-sdk-get-started/list.png
  [Visual Studio プロジェクトをダウンロード]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb
  [前提条件]: #prerequisites
  [学習内容]: #learn
  [Web ジョブ SDK]: #webjobssdk
  [アプリケーションのアーキテクチャ]: #contosoads
  [開発環境を設定する]: #setupdevenv
  [アプリケーションをビルド、実行、およびデプロイする]: #storage
  [アプリケーションを最初から作成する]: #create
  [アプリケーション コードを確認する]: #code
  [トラブルシューティング]: #troubleshoot
  [次のステップ]: #next-steps
  [ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Web フォーム]: http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview
  [free-trial-note]: ../includes/free-trial-note.md
  [Web ジョブ]: /ja-jp/documentation/articles/web-sites-create-web-jobs/
  [AlwaysOn]: http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx
  [Ad table]: ./media/websites-dotnet-webjobs-sdk-get-started/adtable.png
  [Contoso Ads architecture]: ./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png
  [Azure の Web サイト、クラウド サービス、および仮想マシンの比較]: /ja-jp/documentation/articles/choose-web-site-cloud-service-vm/
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [ストレージ アカウントの管理方法に関するページ]: /ja-jp/documentation/articles/storage-manage-storage-account/
  [New storage account]: ./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png
  [SQL Server Express LocalDB]: http://msdn.microsoft.com/ja-jp/library/hh510202.aspx
  [1]: http://manage.windowsazure.com/
  [Manage Access Keys button]: ./media/websites-dotnet-webjobs-sdk-get-started/mak.png
  [Manage Access Keys dialog]: ./media/websites-dotnet-webjobs-sdk-get-started/cpak.png
  [Contoso Ads home page]: ./media/websites-dotnet-webjobs-sdk-get-started/home.png
  [Console application window showing that the backend is running]: ./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png
  [Create page]: ./media/websites-dotnet-webjobs-sdk-get-started/create.png
  [Console application window showing that a queue message has been processed]: ./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png
  [Details page]: ./media/websites-dotnet-webjobs-sdk-get-started/details.png
  [Select Azure Website publish target]: ./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png
  [Click Sign In]: ./media/websites-dotnet-webjobs-sdk-get-started/signin.png
  [Click New]: ./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png
  [Create site on Microsoft Azure dialog]: ./media/websites-dotnet-webjobs-sdk-get-started/newdb.png
  [Connection step]: ./media/websites-dotnet-webjobs-sdk-get-started/connstep.png
  [Settings step]: ./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png
  [Click Start Preview]: ./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png
  [WebJobs files in preview window]: ./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png
  [接続文字列などの機密情報をソース コード リポジトリに格納されるファイルに含めるのは避ける]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets
  [Connection strings in management portal]: ./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png
  [WebJobs tab]: ./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png
  [WebJobs SDK dashboard]: ./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png
  [2]: ./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png
  [新しいプロジェクト]: ./media/websites-dotnet-webjobs-sdk-get-started/newproject.png
  [Change Authentication]: ./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png
  [認証なし]: ./media/websites-dotnet-webjobs-sdk-get-started/noauth.png
  [New Azure WebJob Project menu selection]: ./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png
  [Find WebJobs SDK package]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg.png
  [Install WebJobs SDK package only in WebJob project]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg2.png
  [SQL Server データベースで動作する Entity Framework コードの作成]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc
  [ASP.NET 4.5 での非同期プログラミングの基礎]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [「Program.cs」のセクション]: #programcs
  [ResolveBlobName]: #resolveblobname
  [再試行ポリシー]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
  [HttpPostedFileBase]: http://msdn.microsoft.com/ja-jp/library/system.web.httppostedfilebase.aspx
  [Web ジョブ SDK 0.3.0 ベータの発表]: http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/
  [アプリケーションのクラウド サービス版]: /ja-jp/documentation/articles/cloud-services-dotnet-get-started/
  [依存関係の挿入]: http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection
  [作業パターンのリポジトリと単位]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo
  [ログのためのインターフェイス]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log
  [EF Code First Migrations]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
  [Entity Framework の接続の回復性]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
  [AzureWebJobs]: http://aspnet.codeplex.com/SourceControl/latest#Samples/AzureWebJobs/ReadMe.txt
  [Visual Studio での Azure の Web Sites のトラブルシューティング]: /ja-jp/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
