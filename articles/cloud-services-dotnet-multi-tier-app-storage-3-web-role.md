<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Azure Email Service アプリケーションで使用する Web ロールの作成 - 3/5

この 3 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service のサンプル アプリケーションを作成してデプロイする方法を説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

このチュートリアルでは、次のことについて説明します。

-   Web ロールとワーカー ロールを使用してクラウド サービス プロジェクトを含むソリューションを作成する方法
-   MVC 5 コントローラーとビューで Azure テーブル、BLOB、およびキューを操作する方法
-   Azure テーブルの操作時に同時実行の競合を処理する方法

## このチュートリアルの内容

-   [Visual Studio ソリューションの作成][Visual Studio ソリューションの作成]
-   [ストレージ クライアント ライブラリ NuGet パッケージの更新][ストレージ クライアント ライブラリ NuGet パッケージの更新]
-   [ストレージ エミュレーターを使用するためのプロジェクトの構成][ストレージ エミュレーターを使用するためのプロジェクトの構成]
-   [トレースの構成と再起動の処理][トレースの構成と再起動の処理]
-   [Application\_Start メソッドへのテーブル、キュー、および BLOB コンテナーを作成するコードの追加][Application\_Start メソッドへのテーブル、キュー、および BLOB コンテナーを作成するコードの追加]
-   [Mailing List の作成とテスト][Mailing List の作成とテスト]
-   [Subscriber コントローラーおよびビューの作成とテスト][Subscriber コントローラーおよびビューの作成とテスト]
-   [Message コントローラーおよびビューの作成とテスト][Message コントローラーおよびビューの作成とテスト]
-   [Unsubscribe コントローラーおよびビューの作成とテスト][Unsubscribe コントローラーおよびビューの作成とテスト]
-   [次のステップ][次のステップ]

## <a name="cloudproject"></a>Visual Studio ソリューションの作成

まず、Web フロントエンドに使用するプロジェクトと、バックエンドの Azure ワーカー ロールの 1 つに使用するプロジェクトで構成される Visual Studio ソリューションを作成します。後で、2 つ目のワーカー ロールを追加します。

### Web ロールとワーカー ロールを使用してクラウド サービス プロジェクトを作成する

1.  Visual Studio を起動します。

2.  **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

    ![New Project menu][New Project menu]

3.  **[インストールされたテンプレート]** の **C\#** を展開して **[クラウド]** を選択し、**[Azure Cloud Service]** を選択します。

4.  アプリケーションに「**AzureEmailService**」という名前を付けて、**[OK]** をクリックします。

    ![New Project dialog box][New Project dialog box]

5.  **[新しい Azure Cloud Service]** ダイアログ ボックスで **[ASP.NET Web ロール]** を選択し、右矢印をクリックします。

    ![New Azure Cloud Project dialog box][New Azure Cloud Project dialog box]

6.  右側の列でポインターを **[WebRole1]** に合わせて鉛筆のアイコンをクリックすると、Web ロールの名前を変更できます。

7.  新しい名前として「MvcWebRole」と入力し、Enter キーを押します。

    ![New Azure Cloud Project dialog box - renaming the web role][New Azure Cloud Project dialog box - renaming the web role]

8.  同じ手順で **Worker ロール** を追加し、「WorkerRoleA」という名前を付けて、**[OK]** をクリックします。

    ![New Azure Cloud Project dialog box - adding a worker role][New Azure Cloud Project dialog box - adding a worker role]

9.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。**[Web API]** チェック ボックスをオンにし、**[認証の変更]** をクリックします。

    ![[新しいプロジェクト] ダイアログ ボックス][]

10. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。

    ![No authentication][No authentication]

11. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで **[OK]** をクリックします。

### ページのヘッダー、メニュー、フッターを設定する

このセクションでは、管理者 Web UI の全ページに表示されるヘッダー、フッター、およびメニュー項目を更新します。このアプリケーションには、Mailing Lists 用、Subscribers 用、および Messages 用として、3 セットの管理者 Web ページを用意します。

1.  [完成したソリューション][完成したソリューション]をまだダウンロードしていない場合は、次のステップに進む前にダウンロードしてください。

    チュートリアルの残りの部分でコードを追加する必要が生じたときは、スニペットを切り取って貼り付ける代わりに、ダウンロードしたプロジェクトからファイルを新しいプロジェクトにコピーできます。チュートリアルでは、コピーするコードの重要な部分を示し、説明します。

    ダウンロードしたプロジェクトからファイルを追加するには、ファイルを追加するプロジェクトまたはフォルダーを右クリックし、コンテキスト メニューから **[追加]、[既存の項目]** の順に選択します。次に、完成したプロジェクトをダウンロードした場所に移動して目的のファイルを選択し、**[追加]** をクリックします。**[ターゲット ファイルが存在します]** ダイアログ ボックスが表示された場合は、**[はい]** をクリックします。

2.  MvcWebRole プロジェクトで、ダウンロードしたプロジェクトから *Views\\Shared\_Layout.cshtml* ファイルを追加します (*Views* の下の *Shared* フォルダーを右クリックしてファイルを追加します)。

    これにより、Mailing List、Message、および Subscriber ページのヘッダー、フッター、およびメニュー エントリが追加されます。

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### ローカルでアプリケーションを実行する

1.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

    これまで Azure クラウド サービス プロジェクトではない Web プロジェクトをよく起動していた開発者であれば、ブラウザーにホーム ページが表示されるまで通常より時間がかかることに気が付くことでしょう。

    ![home page][home page]

    この遅延は、Visual Studio によって Azure コンピューティング エミュレーターと Azure ストレージ エミュレーターが起動されることに原因があります。Windows システム トレイに、コンピューティング エミュレーター アイコンが表示されます。

    ![Compute emulator in system tray][Compute emulator in system tray]

2.  ブラウザーを閉じます。

## <a name="updatescl"></a>ストレージ クライアント ライブラリ NuGet パッケージの更新

Azure のストレージのテーブル、キュー、および BLOB を操作するために使用する API フレームワークは、ストレージ クライアント ライブラリ (SCL) です。この API は、クラウド サービス プロジェクト テンプレート内の NuGet パッケージに含まれています。ただし、プロジェクト テンプレートが作成された後に SCL の更新プログラムがリリースされることも多いため、常に SCL NuGet パッケージの更新プログラムの有無を確認することが重要です。

1.  Visual Studio の **[ツール]** メニューの **[ライブラリ パッケージ マネージャー]** にポインターを合わせて、**[ソリューションの NuGet パッケージの管理]** をクリックします。

    ![Manage NuGet Packages for Solution in menu][Manage NuGet Packages for Solution in menu]

2.  **[NuGet パッケージの管理]** ダイアログ ボックスの左側のウィンドウで、**[更新プログラム]** を選択して **[Azure Storage]** パッケージまで下方向へスクロールし、**[更新]** をクリックします。

    ![Azure Storage package in Manage NuGet Packages dialog box][Azure Storage package in Manage NuGet Packages dialog box]

3.  **[プロジェクトの選択]** ダイアログ ボックスで、両方のプロジェクトが選択されていることを確認し、**[OK]** をクリックします。

    ![Selecting both projects in the Select Projects dialog box][Selecting both projects in the Select Projects dialog box]

4.  ライセンス条項に同意してパッケージのインストールを完了し、**[NuGet パッケージの管理]** ダイアログ ボックスを閉じます。

## <a name="configurestorage"></a>ストレージ エミュレーターを使用するためのプロジェクトの構成

後で追加する Web ロールと worker ロールのコードでは、StorageConnectionString という名前の接続文字列を使用して Azure Storage に接続します。このセクションでは、ロールのプロパティに設定を追加して、ストレージ エミュレーターを使用するように構成します。このシリーズの 2 つ目のチュートリアルでは、Azure Storage アカウントを使用するように接続文字列を構成する方法について説明します。

1.  **ソリューション エクスプローラー**で、**AzureEmailService** クラウド プロジェクトの **[Roles]** の下にある **[MvcWebRole]** を右クリックし、**[プロパティ]** を選択します。

    ![Web role properties][Web role properties]

2.  **[サービス構成]** ボックスの一覧で **[すべての構成]** が選択されていることを確認します。

3.  **[設定]** タブを選択し、**[設定の追加]** をクリックします。

4.  **[名前]** 列に「StorageConnectionString」と入力します。

5.  **[種類]** ボックスの一覧の **[接続文字列]** を選択します。

6.  その行の右端にある **[...]** ボタンをクリックして、**[ストレージ アカウント接続文字列]** ダイアログ ボックスを開きます。

    ![Right Click Properties][Right Click Properties]

7.  **[ストレージ接続文字列の作成]** ダイアログ ボックスで **[Azure ストレージ エミュレーター]** をクリックし、**[OK]** をクリックします。

    `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列は既定でストレージ エミュレーターに設定されるため、変更を加える必要はありません。

8.  MvcWebRole の場合と同じ手順に従って、WorkerRoleA ロールのストレージ接続文字列を追加します。

**[設定の追加]** ボタンを使用して新しい設定を追加したときに、*ServiceDefinition.csdf* ファイルの XML と、2 つの *.cscfg* 設定ファイルにそれぞれ新しい設定が追加されました。Visual Studio によって *ServiceDefinition.csdf* ファイルに追加された XML は次のとおりです。

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

各 *.cscfg* 構成ファイルに追加された XML は次のとおりです。

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

*ServiceDefinition.csdf* ファイルと 2 つの *.cscfg* 構成ファイルに設定を手動で追加することもできますが、プロパティ エディターを使用すると、接続文字列に関して次のような利点があります。

-   新しい設定を 1 か所で追加すると、正しい設定の XML が 3 つのファイルすべてに追加される。
-   3 つの設定ファイルについて、正しい XML が生成される。*ServiceDefinition.csdf* ファイルには、各 *.cscfg* 構成ファイルに含める必要のある設定が定義されています。*ServiceDefinition.csdf* ファイルと 2 つの *.cscfg* 構成ファイルの設定に矛盾があると、Visual Studio から "*現在のサービス モデルは非同期です。サービス構成ファイルおよびサービス定義ファイルが有効であることを確認してください。*" というエラー メッセージが表示されることがあります。

    ![Invalid service configuration and definition files error][Invalid service configuration and definition files error]

このエラーが発生すると、手動でファイルを編集して矛盾の問題を解決するまでプロパティ エディターは動作しません。

## <a name="tracing"></a>トレースの構成と再起動の処理

1.  MvcWebRole プロジェクトで、ダウンロードしたプロジェクトから *WebRole.cs* ファイルを追加します。

これにより、ログを構成するメソッドが追加され、Web ロールが開始されたときに実行される `OnStart` メソッドから呼び出されるようになります。新しい `ConfigureDiagnostics` メソッドのコードについては、[2 番目のチュートリアル][2 番目のチュートリアル]の説明を参照してください。

また、シャットダウンされることが Web ロールに通知されたときに実行されるコードも追加されます。Azure クラウド サービス アプリケーションは、オペレーティング システムの更新に伴い、月に約 2 回再起動されます (OS の更新の詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)][Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)]」を参照してください)。Web アプリケーションがシャットダウンされる際には、`OnStop` イベントが発生します。Visual Studio によって作成された Web ロール ボイラー プレートでは `OnStop` メソッドをオーバーライドしていないため、アプリケーションが HTTP 要求の処理を完了するための時間はシャットダウンまでに数秒しかありません。シャットダウンが正常に処理されるようにするには、コードを追加して `OnStop` メソッドをオーバーライドすることができます。

ここで追加したファイルには、次の `OnStop` メソッドのオーバーライドが含まれています。

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

`OnStop` メソッドが終了するための時間は、アプリケーションのシャットダウンまでに最長で 5 分間あります。5 分間の Sleep 呼び出しを `OnStop` メソッドに追加することにより、アプリケーションが現在の要求を処理するための時間として最長時間を設定することもできますが、アプリケーションが正しく拡張されていれば、残っている要求を 5 分よりずっと短い時間で処理できます。望ましいのは、アプリケーションができるだけ早く再起動し、要求の処理を続行できるように、できるだけ早く停止することです。

Azure がロールをオフラインにすると、ロール インスタンスへの要求の送信がロード バランサーによって停止され、その後で、`OnStop` メソッドが呼び出されます。ロール インスタンスが他になければ、ロールのシャットダウンが完了して再起動されるまで (通常は数分かかります)、要求は処理されません。Azure サービス レベル アグリーメントでアップタイム保証を利用するためには、ロールごとに 2 つ以上のインスタンスが要求されていますが、その理由の 1 つはこの点です。

`OnStop` メソッド用に示したコードの中で、ASP.NET パフォーマンス カウンター `Requests Current` が作成されています。`Requests Current` カウンターの値には、現在の要求数が設定されます。これには、キューに登録済み、現在実行中、またはクライアントへの書き込み待ちの要求が含まれています。`Requests Current` 値は毎秒チェックされ、ゼロになると、`OnStop` メソッドが制御を返します。`OnStop` が制御を返した時点で、ロールがシャットダウンされます。

[オンデマンド転送][オンデマンド転送]を実行せずに `OnStop` メソッドから呼び出された場合、トレース データは保存されません。リモート デスクトップ接続で [dbgview][dbgview] ユーティリティを使用すると、`OnStop` トレース情報をリアルタイムで確認できます。

## <a name="createifnotexists"></a>Application\_Start メソッドへのテーブル、キュー、および BLOB コンテナーを作成するコードの追加

この Web アプリケーションでは、`MailingList` テーブル、`Message` テーブル、`azuremailsubscribequeue` キュー、および `azuremailblobcontainer` BLOB コンテナーを使用します。Azure ストレージ エクスプローラーなどのツールを使用してこれらを手動で作成することもできますが、新しいストレージ アカウントでアプリケーションの使用を開始するたびに、その処理を手動で行う必要が生じます。このセクションでは、アプリケーションの開始時に実行され、必要なテーブル、キュー、および BLOB コンテナーが存在するかどうかを確認し、存在しない場合はこれらを作成するコードを追加します。

この 1 回限りのスタートアップ コードは、*WebRole.cs* ファイル内の `OnStart` メソッドまたは *Global.asax* ファイルに追加できます。このチュートリアルでは、*Global.asax* ファイルで Azure Storage を初期化します。

1.  **ソリューション エクスプローラー**で、MvcWebRole プロジェクトを右クリックし、ダウンロードしたプロジェクトから *Global.asax.cs* ファイルを追加します。

これで、`Application_Start` から呼び出される新しいメソッドが追加されました。

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

次の各セクションでは、Web アプリケーションの各コンポーネントを構築します。これらは、開発用ストレージまたは自分のストレージ アカウントを使用して、テーブル、キュー、または BLOB コンテナーを手動であらかじめ作成することなくテストすることができます。

## <a name="mailinglist"></a>Mailing List コントローラーおよびビューの作成とテスト

**Mailing List** Web UI は、"Contoso University History Department announcements" や "Fabrikam Engineering job postings" などのメーリング リストを作成、編集、および表示するために、管理者によって使用されます。

### MailingList エンティティ クラスを Models フォルダーに追加する

`MailingList` エンティティ クラスは、メーリング リストの説明やメーリング リストに送信される "差出人" の電子メール アドレスなど、メーリング リストに関する情報を格納する `MailingList` テーブル内の行に使用されます。

1.  MVC プロジェクトの `Models` フォルダーで、ダウンロードしたプロジェクトから *MailingList.cs* ファイルを追加します。

    `MailingList` エンティティ クラスは、mailinglist テーブル内の MailingList 行の読み取りと書き込みを行うために使用します。

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    Azure Storage API では、テーブル操作に使用するエンティティ クラスが [TableEntity][TableEntity] から派生していることが求められます。`TableEntity` では、`PartitionKey`、`RowKey`、`TimeStamp`、および `ETag` の各フィールドが定義されます。`TimeStamp` および `ETag` プロパティはシステムによって使用されます。このチュートリアルの後の方で、どのように `ETag` プロパティが同時実行処理に使用されるかを説明します。

    (定義済みのモデル クラスを使用する代わりに、Dictionary コレクションのキー値ペアとしてテーブル行を操作する場合に使用する [DynamicTableEntity][DynamicTableEntity] クラスもあります。詳細については、[Azure のストレージ クライアント ライブラリ 2.0 テーブルの詳細に関するページ][Azure のストレージ クライアント ライブラリ 2.0 テーブルの詳細に関するページ]を参照してください。)

    `mailinglist` テーブルのパーティション キーはメーリング リスト名です。このエンティティ クラスでは、`PartitionKey` プロパティ (`TableEntity` クラスで定義されます) または `ListName` プロパティ (`MailingList` クラスで定義されます) を使用してパーティション キー値にアクセスできます。`ListName` プロパティでは、バッキング変数として `PartitionKey` が使用されます。`ListName` プロパティを定義すると、わかりやすい変数名をコード内で使用でき、Web UI のプログラミングが容易になります。これは、書式設定と検証の DataAnnotations 属性を `ListName` プロパティに追加できるためですが、DataAnnotations 属性を `PartitionKey` プロパティに直接追加することはできません。

    `ListName` プロパティに `RegularExpression` 属性が追加されている場合、MVC はユーザー入力を検証して、入力されたリスト名の値に含まれている文字が英数字またはアンダースコアのみであることを確認します。この制約は、URL のクエリ文字列に容易に使用できるように、リスト名をシンプルに保つために実装されています。

    > [WACOM.NOTE] リスト名の書式に対する制約を緩和するには、クエリ文字列で他の文字および URL エンコード リスト名の使用を許可することもできます。ただし、Azure テーブルのパーティション キーまたは行キーに使用できない特定の文字が存在するため、少なくともこれらの文字は除外する必要があります。パーティション キー フィールドまたは行キー フィールドで使用できない (使用すると問題の原因になる) 文字については、「[テーブル サービス データ モデルについて][テーブル サービス データ モデルについて]」および [PartitionKey または RowKey にパーセント '%' 文字が含まれていると、一部の Azure テーブル API が失敗する動作に関するページ][PartitionKey または RowKey にパーセント '%' 文字が含まれていると、一部の Azure テーブル API が失敗する動作に関するページ]を参照してください。

    `MailingList` クラスで定義されている既定のコンストラクターでは、ハードコーディングされた文字列 "mailinglist" が `RowKey` に設定されます。これは、テーブル内のすべての MailingList 行で、行キーがこの値であるためです (テーブル構造の詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください)。この目的では、任意の定数値を選択することができます。ただし、このテーブルの Subscriber 行の行キーである電子メール アドレスと同じ値にすることはできません。

    新しい `MailingList` エンティティの作成時には、必ずリスト名と "差出人" の電子メール アドレスを入力して、`Required` 属性が指定されているフィールドの値を確保します。

    `Display` 属性は、MVC UI のフィールドに、既定のキャプションを使用することを指定します。

### MailingList MVC コントローラーを追加する

1.  MVC プロジェクトの *Controllers* フォルダーで、ダウンロードしたプロジェクトから *MailingListController.cs* ファイルを追加します。

    既定のコンストラクターでは、`mailinglist` テーブルを操作するために使用する `CloudTable` オブジェクトを作成します。

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    このコードでは、ストレージ アカウントに接続するために、クラウド サービス プロジェクトの設定ファイルから Azure のストレージ アカウントの資格情報を取得しています (このチュートリアルでは、コントローラーをテストする前に、これらの設定を後で構成します)。

    次の `FindRowAsync` メソッドは、`MailingList` テーブルから特定のメーリング リスト エントリをコントローラーが検索する必要がある場合 (メーリング リスト エントリを編集する場合など) に呼び出されます。このメソッドは、渡されたパーティション キーおよび行キーの値を使用して、単一の `MailingList` エンティティを取得します。このコントローラーが編集する行は、行キーが "MailingList" の行であるため、"MailingList" を行キーとしてハードコーディングすることもできますが、パーティション キーと行キーの指定は、すべてのコントローラーの `FindRow` メソッドで使用されているパターンです。

    > [WACOM.NOTE] アプリケーションは、サーバー リソースを効率的に使用するために、ASP.NET 4.5 の非同期コードを使用して Web ロールの I/O 操作を実行します。Web アプリケーションの非同期コードの詳細については、「[Use .NET 4.5's async support to avoid blocking calls (ブロッキング呼び出しを回避するための .NET 4.5 の非同期サポートの使用)][Use .NET 4.5's async support to avoid blocking calls (ブロッキング呼び出しを回避するための .NET 4.5 の非同期サポートの使用)]」を参照してください。

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    `FindRow` メソッドのコードでは、MailingList 行が返されます。対応する `Subscriber` コントローラーの `FindRow` メソッドでは、同じ `mailinglist` テーブルから Subscriber 行が返されます。2 つのメソッドのコードは、[TableOperation.Retrieve][TableOperation.Retrieve] で使用されるモデルの種類を除き、同じです。

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    `TableOperation.Retrieve` から返される `TableOperation` オブジェクトは、クエリから返される 1 つまたは複数の行のスキーマ (プロパティ) を指定します。同じテーブルでも、行によって異なるスキーマを指定することができます。一般的に、行を読み取るときは、行を作成するために使用されたものと同じモデルの種類を指定します。

    **Index** ページには、すべての MailingList 行が表示されるため、`Index` メソッド内のクエリは、行キーが "mailinglist" であるすべての `MailingList` エンティティを返します (テーブル内の他の行では、行キーが電子メール アドレスであり、これらには登録者情報が格納されています)。

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    `ExecuteQuerySegmentedAsync` メソッドは、大きな結果セットをセグメントに分割します。このメソッドは、最大で 1,000 行を返します。1,000 行を超える行を取得するクエリを実行すると、1,000 行と継続トークンが返されます。この継続トークンを使用すると、前のクエリが終了した箇所から継続して次のクエリを実行できます。ここに示されているコードは、サンプル アプリケーション用に簡略化されているため、すべてのセグメントが 1 つのリストに集約されています。運用アプリケーションの場合は、ページング コードを実装します。大きな結果セットと継続トークンの詳細については、「[How to get most out of Windows Azure Tables (Windows Azure テーブルの最大活用)][How to get most out of Windows Azure Tables (Windows Azure テーブルの最大活用)]」および「[Windows Azure Tables: Expect Continuation Tokens, Seriously! (Windows Azure テーブル: 継続トークンの重要性)][Windows Azure Tables: Expect Continuation Tokens, Seriously! (Windows Azure テーブル: 継続トークンの重要性)]」を参照してください。.

    `OperationContext` オブジェクトを作成するとき、`ClientID` プロパティ値を設定して、Azure Storage によって書き込まれるログに含める一意の識別子を指定できます。この識別子を使用して、ストレージ サービス アクティビティを発生させたコードまでストレージ操作ログをトレースできます。Azure Storage ログの詳細については、「[Windows Azure Storage Logging: Using Logs to Track Storage Requests (Windows Azure Storage ログ: ログを使用したストレージ要求の追跡)][Windows Azure Storage Logging: Using Logs to Track Storage Requests (Windows Azure Storage ログ: ログを使用したストレージ要求の追跡)]」を参照してください。

    SCL 2.1 以降の API では、テーブル クエリに LINQ を使用することもできます。LINQ の使用法を示すコード サンプルについては、「[PhluffyFotos Sample (PhluffyFotos サンプル)][PhluffyFotos Sample (PhluffyFotos サンプル)]」を参照してください。

    再試行ポリシーを指定しなかった場合は、API により指数関数的にタイムアウト制限が延長され、試行が自動的に 3 回行われます。Web インターフェイスでユーザーがページの表示を待っている場合、この待ち時間は許容できない長さになる可能性があります。そのため、このコードでは、(タイムアウトが毎回延長されないように) 直線的な値が指定され、ユーザーの待ち時間として妥当なタイムアウト制限が指定されています。再試行ポリシーは、`ExecuteQuerySegmentedAsync` メソッドに渡される `webUIRetryPolicy` オブジェクトに指定します。`webUIRetryPolicy` オブジェクトは、コントローラー コンストラクターに定義されています。

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    `Index` メソッドには、タイムアウト条件を処理するための try-catch ブロックが含まれています。

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    ユーザーが **Create** ページで **[Create]** ボタンをクリックすると、MVC モデル バインダーによって、ビューでの入力に基づいて `MailingList` エンティティが作成され、`HttpPost Create` メソッドにより、このエンティティがテーブルに追加されます。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    **Edit** ページでは、`HttpPost Edit` メソッドにより行が更新されます。

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    `HttpPost Edit` メソッドでは、同時実行エラーが処理されます。

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    ユーザーが編集用にメーリング リストを選択し、ブラウザーに **Edit** ページが表示されている間に、別のユーザーが同じメーリング リストを編集すると、同時実行例外が発生します。この状況が発生すると、このコードでは警告メッセージが表示され、別のユーザーによって変更されたフィールドが示されます。TSL API では、`ETag` を使用して同時実行の競合チェックが行われます。テーブル行が更新されるたびに、`ETag` 値が変更されます。編集用に行を取得する際には `ETag` 値が保存され、更新操作または削除操作を実行する際には、保存した `ETag` 値が渡されます (`Edit` ビューには、ETag 値用の非表示フィールドがあります)。更新操作時に、更新対象のレコード上の `ETag` 値が、更新操作に渡された `ETag` 値と異なることが検出されると、同時実行例外が発生します。同時実行の競合が発生してもかまわない場合は、更新操作に渡すエンティティの ETag フィールドをアスタリスク ("\*") に設定しておくと、競合が無視されます。

    注: HTTP 412 エラーは、同時実行エラーに特有のものではありません。SCL API から他のエラーを対象にして発生することもあります。

    **Delete** ページでは、`HttpPost Delete` メソッドにより、`MailingList` 行が `MailingList` テーブル内の関連する `Subscriber` 行と共に削除されます。

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    多数の登録者を削除する必要がある場合は、レコードがバッチで削除されます。1 行を削除する場合も、100 行をバッチで削除する場合も、トランザクション コストは同じです。1 バッチで実行できる操作の最大数は 100 です。

    このループでは、`MailingList` 行と `Subscriber` 行の両方が処理されますが、`Delete` 操作に必要なフィールドは `PartitionKey`、`RowKey`、および `ETag` のみであるため、これらはすべて `MailingList` エンティティ クラスに読み込まれます。

### MailingList MVC ビューを追加する

1.  MVC プロジェクトの *Views* フォルダーの下に新しいフォルダーを作成し、*MailingList* という名前を付けます。

2.  新しい *Views\\MailingList* フォルダーを右クリックし、ダウンロードしたプロジェクトから 4 つの *.cshtml* ファイルをすべて追加します。

*Edit.cshtml* ファイルには、同時実行の競合処理に使用される `ETag` 値を保持するための非表示フィールドが含まれています。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

また、`ListName` フィールドでは、`EditorFor` ヘルパーではなく `DisplayFor` ヘルパーが使用されています。

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

**Edit** ページでリスト名の変更操作は有効になっていません。これは、コントローラーに複雑なコードが必要になるためです。`HttpPost Edit` メソッドで、既存の MailingList 行およびすべての関連する Subscriber 行を削除し、これらをすべて新しいキー値で再挿入する操作が必要になります。運用アプリケーションでは、このように複雑な操作が必要になることがあります。後でわかりますが、`Subscriber` コントローラーでは、メーリング リスト名の変更を許可しています。これは、一度に影響を受ける行が 1 行のみであるためです。

*Index.cshtml* ファイルの **Edit** ハイパーリンクおよび **Delete** ハイパーリンクでは、行を特定するために、クエリ文字列パラメーターとしてパーティション キーおよび行キーを指定します。`MailingList` エンティティでは、行キーは常に "MailingList" であるため、実際に必要なのはパーティション キーのみですが、MVC ビューのコードをすべてのコントローラーおよびビューで統一できるように、両方のキーを残しています。

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### MailingList を既定のコントローラーにする

1.  *[App\_Start]* フォルダー内の *Route.config.cs* を開きます。

2.  既定値を指定する行で、既定のコントローラーを "Home" から "MailingList" に変更します。

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### アプリケーションをテストする

1.  Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。

    ![空の MailingList の Index ページ][空の MailingList の Index ページ]

2.  **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

    ![MailingList Index page with rows][MailingList Index page with rows]

## <a name="subscriber"></a><span class="short-header">登録者</span>Subscriber コントローラーおよびビューの作成とテスト

**登録者** Web UI は、管理者が新しい登録者をメーリング リストに追加し、既存の登録者を編集、表示、および削除するために使用されます。

### Subscriber エンティティ クラスを Models フォルダーに追加する

`Subscriber` エンティティ クラスは、メーリング リストの登録者に関する情報を格納する `MailingList` テーブル内の行に使用されます。これらの行には、登録者の電子メール アドレスやアドレスが確認済みかどうかなどの情報が格納されます。

1.  MVC プロジェクトの *Models* フォルダーで、ダウンロードしたプロジェクトから *Subscriber.cs* ファイルを追加します。

`MailingList` エンティティ クラスと同様、`Subscriber` エンティティ クラスも、`mailinglist` テーブル内の行の読み取りと書き込みに使用されます。

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

`Subscriber` 行では、定数 "mailinglist" ではなく電子メール アドレスが行キーに使用されます (テーブル構造の詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください)。このため、`EmailAddress` が `RowKey` をバッキング フィールドとして使用するように、`ListName` プロパティは `PartitionKey` プロパティをバッキング フィールドとして使用することが定義されています。前に説明したように、これによって、書式設定と検証の DataAnnotations 属性をプロパティに追加できます。

`SubscriberGUID` 値は、`Subscriber エンティティの作成時に生成されます。この値は、許可されている人だけが電子メール アドレスを登録または登録解除できるようにするために、登録リンクと登録解除リンクで使用されます。`

新しい登録者に対して行が最初に作成されたとき、`Verified` 値は `false` です。`Verified` 値が `true` に変化するのは、新しい登録者が "ようこそ" メール内の **Confirm** ハイパーリンクをクリックした後だけです。登録者の `Verified` 値が `false` である場合にメーリング リストにメッセージを送信しても、その登録者には電子メールが送信されません。

`Subscriber` エンティティの `Verified` プロパティは、null 値を許容するように定義されています。クエリから `Subscriber` エンティティが返されるように指定した場合、取得した行の一部に `Verified` プロパティが設定されていない可能性もあります。このため、`Subscriber` エンティティでは、*Verified* プロパティのない行がクエリによって返された場合に行の実際の内容をより正確に反映できるように、`Verified` プロパティで null 値を許容するように定義されています。多くの人が、テーブルのすべての行に同じスキーマを使用する SQL Server テーブルの方に慣れているかもしれません。Azure のストレージ テーブルでは、各行はプロパティのコレクションに過ぎず、各行でそれぞれ異なるプロパティのセットが使用されます。たとえば、Azure Email Service サンプル アプリケーションの場合、行キーが "MailingList" である行には `Verified` プロパティがありません。`Verified` プロパティのないテーブル行がクエリから返された場合は、`Subscriber` エンティティ クラスがインスタンス化されるときに、エンティティ オブジェクト内の `Verified` プロパティが null になります。プロパティが null 許容ではない場合、`Verified` 値が `false` である行も `Verified` プロパティがない行も、同じ `false` 値になります。このため、Azure テーブルを使用する場合のベスト プラクティスは、行によって異なるエンティティ クラスまたは現在のエンティティ クラスの複数の異なるバージョンを使用して作成された行を正確に読み取ることができるように、エンティティ クラスの各プロパティを null 許容にすることです。

### Subscriber MVC コントローラーを追加する

1.  **ソリューション エクスプローラー**で MVC プロジェクトの *[Controllers]* フォルダーを右クリックし、**[既存項目の追加]** を選択します。

2.  サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Controllers* フォルダー内の *SubscriberController.cs* ファイルを選択して、**[追加]** をクリックします (*Subscribe.cs* ではなく *Subscriber.cs* を選択してください。*Subscribe.cs* は後で追加します)。

このコントローラーのコードの大半は、`MailingList` コントローラーで確認したものと似ています。登録者情報は `MailingList` テーブルに保存されるため、テーブル名も同じです。

`FindRowAsync` メソッドに加えて、`FindRow` メソッドがあります。これは、このメソッドを catch ブロックから呼び出す必要がなく、catch ブロックから非同期メソッドを呼び出すことができないためです。

`FindRow` メソッドの後には、`GetListNamesAsync` メソッドがあります。このメソッドでは、**Create** ページと **Edit** ページにある、電子メール アドレスを登録するメーリング リストを選択するためのドロップダウン リスト用のデータを取得します。

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

これは、`MailingList` コントローラーで確認したものと同じクエリです。このドロップダウン リストには、メーリング リストに関する情報が格納されている行が必要であるため、RowKey の値が "mailinglist" である行のみを選択します。

**Index** ページ用のデータを取得するメソッドでは、登録者情報が格納されている行が必要であるため、RowKey 値が "MailingList" ではない行をすべて選択します。

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

このクエリでは、(`<Subscriber>` を指定することによって) データを `Subscriber` オブジェクトに読み込むことが指定されていますが、取得するデータは `mailinglist` テーブルから読み込みます。

登録者数が増加すると、このように単一のクエリで処理できなくなることがあります。前述のように、運用アプリケーションでは、継続トークンを使用してページングを実装します。

`HttpGet Create` メソッドでは、ドロップダウン リストのデータをセットアップし、`HttpPost` メソッドでは、新しいエンティティを保存する前に既定値を設定します。

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

`Subscriber` ページでは、キー フィールドであるメーリング リスト名または電子メール アドレスを変更できるため、`HttpPost Edit` ページは、`MailingList` コントローラーで確認したものよりも複雑です。これらのうちいずれかのフィールドがユーザーによって変更された場合は、既存のレコードを更新するのではなく、既存のレコードを削除して新しいレコードを追加する必要があります。次のコードは、Edit メソッドのうち、キーの変更と非キーの変更で異なる手順を処理する部分を示しています。

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

MVC モデル バインダーが `Edit` メソッドに渡すパラメーターには、元のメーリング リスト名と電子メール アドレスの値 (`partitionKey` パラメーターと `rowKey` パラメーター) およびユーザーが入力した値 (`listName` パラメーターと `emailAddress` パラメーター) があります。

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

`UpdateModel` メソッドに渡されるパラメーターには、モデル バインディングからの `PartitionKey` プロパティと `RowKey` プロパティは含まれません。

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

その理由は、`ListName` プロパティと `EmailAddress` プロパティでは、バッキング プロパティとして `PartitionKey` と `RowKey` が使用されており、ユーザーによってこれらの値のいずれかが変更されている可能性があるためです。モデル バインダーが `ListName` プロパティを設定してモデルを更新すると、`PartitionKey` プロパティも自動的に更新されます。モデル バインダーが `ListName` プロパティの更新後に `PartitionKey` プロパティを元の値で更新する場合、`ListName` プロパティで設定された新しい値は上書きされます。同様に、`EmailAddress` プロパティによって自動的に `RowKey` プロパティも更新されます。

コードでは、`editedSubscriber` モデル オブジェクトを更新した後、パーティション キーまたは行キーが変更されているかどうかを判断します。いずれかのキーが変更されている場合は、既存の Subscriber 行を削除し、新しい Subscriber 行を挿入する必要があります。行キーのみが変更されている場合、削除と挿入はアトミック バッチ トランザクションで行うことができます。

コードでは、新しいエンティティを作成して `Delete` 操作に渡しています。

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

各バッチ操作に渡すエンティティは、別々のエンティティである必要があります。たとえば、`Subscriber` エンティティを作成して `Delete` 操作に渡し、同じ `Subscriber` エンティティ内の値を変更したものを `Insert` 操作に渡すことはできません。このようにした場合は、プロパティの変更後のエンティティの状態は、Delete 操作でも Insert 操作でも有効になります。

1 つのバッチに含まれる操作はすべて、対象パーティションが同じである必要があります。メーリング リスト名の変更はパーティション キーの変更も伴うため、1 つのトランザクションでは実行できません。

### Subscriber MVC ビューを追加する

1.  **ソリューション エクスプローラー**で、MVC プロジェクトの *[Views]* フォルダーの下に新しいフォルダーを作成し、「*Subscriber*」という名前を付けます。

2.  新しい *Views\\Subscriber* フォルダーを右クリックして、**[既存項目の追加]** を選択します。

3.  サンプル アプリケーションをダウンロードしたフォルダーに移動し、*Views\\Subscriber* フォルダー内にある 5 つの .cshtml ファイルをすべて選択して、**[追加]** をクリックします。

*Edit.cshtml* ファイルには、`SubscriberGUID` 値用の非表示フィールドがあります。このフィールドは表示されないため、フォーム フィールドに自動的に設定されません。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### アプリケーションをテストする

1.  Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Subscribers]** をクリックします。

    ![Empty Subscriber Index page][Empty Subscriber Index page]

2.  **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

    ![Subscribers Index page with rows][Subscribers Index page with rows]

## <a name="message"></a>Message コントローラーおよびビューの作成とテスト

**メッセージ** Web UI は、メーリング リストに送信するメッセージに関する情報を管理者が作成、編集、および表示するために使用されます。

### Message エンティティ クラスを Models フォルダーに追加する

1.  MVC プロジェクトの *Models* フォルダーで、ダウンロードしたプロジェクトから *Message.cs* ファイルを追加します。

`Message` エンティティ クラスは、メーリング リストに送信するメッセージに関する情報を格納する `Message` テーブル内の行に使用されます。これらの行には、件名、メッセージの送信先リスト、送信予定日などの情報が格納されます。

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

`Message` クラスで定義されている既定のコンストラクターでは、メッセージを示す一意の値が `MessageRef` プロパティに設定されます。この値は行キーの一部であるため、`MessageRef` プロパティの setter は自動的に `RowKey` プロパティも設定します。`MessageRef` プロパティの setter は、"message" リテラルと `MessageRef` 値を連結し、それを `RowKey` プロパティに設定します。

`MessageRef` 値は、`DateTime.Now` から `Ticks` 値を取得することによって作成されます。これにより、Web UI にメッセージを表示する場合は、既定で、指定の予定日 (`ScheduledDate` はパーティション キー) に対して作成された順序で表示されるようになります。GUID を使用して Message 行を一意にすることもできますが、この場合は、既定の取得順序がランダムになります。

既定のコンストラクターでは、新しい `message` 行に既定のステータスとして "Pending" を設定する処理も行われます。

`Message` テーブル構造の詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

### Message MVC コントローラーを追加する

1.  MVC プロジェクトの *Controllers* フォルダーで、ダウンロードしたプロジェクトから *MessageController.cs* ファイルを追加します。

このコントローラーのコードの大半は、`Subscriber` コントローラーで確認したものと似ています。新しい部分は、BLOB を操作するコードです。各メッセージについて、電子メールの HTML コンテンツとプレーンテキスト コンテンツが .htm ファイルと .txt ファイルの形式でアップロードされ、BLOB に保存されます。

BLOB は BLOB コンテナーに保存されます。Azure Email Service アプリケーションでは、すべての BLOB が "azuremailblobcontainer" という名前の単一の BLOB コンテナーに格納されます。コントローラー コンストラクターのコードでは、この BLOB コンテナーの参照を取得しています。

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

ユーザーが選択してアップロードする各ファイルについて、MVC ビューでは、ファイルに関する情報が格納された `HttpPostedFile` オブジェクトを提供します。ユーザーが新しいメッセージを作成すると、`HttpPostedFile` オブジェクトを使用してファイルが BLOB に保存されます。ユーザーがメッセージを編集する際に、ユーザーは置換ファイルをアップロードすることまたは BLOB を変更せずに維持することを選択できます。

このコントローラーには、`HttpPost Create` メソッドおよび `HttpPost Edit` メソッドから、BLOB を保存するために呼び出されるメソッドが含まれています。

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

`HttpPost Create` メソッドは 2 つの BLOB を保存してから、`Message` テーブル行を追加します。BLOB の名前は、`MessageRef` 値にファイル名拡張子 ".htm" または ".txt" を連結したものになります。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

`HttpGet Edit` メソッドでは、ワーカー ロール B で処理が開始されたメッセージをユーザーが変更できないように、取得されたメッセージが `Pending` ステータスかどうかを検証します。`HttpPost Edit` メソッド、`Delete` メソッド、および `DeleteConfirmed` メソッドにも、同様のコードが含まれています。

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

`HttpPost Edit` メソッドでは、ユーザーが新しいファイルのアップロードを選択した場合のみ、新しい BLOB を保存しています。

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

予定日が変更されると、パーティション キーが変更され、行の削除と挿入が必要になります。この操作は複数のパーティションが対象になるため、1 つのトランザクションでは実行できません。

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

`HttpPost Delete` メソッドは、テーブル内の行を削除する際に、BLOB を削除します。

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Message MVC ビューを追加する

1.  MVC プロジェクトの *Views* フォルダーの下に新しいフォルダーを作成し、`Message` という名前を付けます。

2.  新しい *Views\\Message* フォルダーを右クリックし、ダウンロードしたプロジェクトから 5 つの *.cshtml* ファイルをすべて追加します。

`HttpPost Edit` メソッドではパーティション キーと行キーが必要であるため、*Edit.cshtml* ファイルのコードではこれらが非表示フィールドで提供されています。

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

`Subscriber` コントローラーでは、非表示フィールドは不要でした。これは、(a) `Subscriber` モデルの `ListName` プロパティと `EmailAddress` プロパティにより、`PartitionKey` プロパティと `RowKey` プロパティが更新され、(b) Edit ビューには `ListName` プロパティと `EmailAddress` プロパティが `EditorFor` ヘルパーと共に含まれているためです。`Subscriber` モデルの MVC モデル バインダーが `ListName` プロパティを更新する際には `PartitionKey` プロパティが自動的に更新され、MVC モデル バインダーが `Subscriber` モデルの `EmailAddress` プロパティを更新する際には `RowKey` プロパティが自動的に更新されます。`Message` モデルでは、パーティション キーと行キーにマップされるフィールドは編集可能フィールドではないため、設定方法が異なります。

`MessageRef` プロパティ用の非表示フィールドも含まれています。これはパーティション キーと同じ値ですが、`HttpPost Edit` メソッドのコードをわかりやすくするために含まれています。`MessageRef` 非表示フィールドを含めておくと、`HttpPost Edit` メソッドのコードは、BLOB のファイル名を作成するときに、その名前で `MessageRef` 値を参照することができます。

*Index.cshtml* ファイルの Message **Index** ビューは、他の **Index** ビューと似ていますが、**Edit** リンクと **Delete** リンクが `Pending` ステータスのメッセージに対してのみ表示される点が異なります。

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

これによってユーザーは、ワーカー ロール A で処理が開始されたメッセージを変更できなくなります。

### アプリケーションをテストする

1.  Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Messages]** をクリックします。

    ![Empty Message Index page][Empty Message Index page]

2.  **Create** 機能を使用してメーリング リストを追加し、**Edit** 機能と **Delete** 機能を試して動作することを確認します。

    ![行が表示されている Subscribers の Index ページ][行が表示されている Subscribers の Index ページ]

## <a name="unsubscribe"></a>Unsubscribe コントローラーおよびビューの作成とテスト

次は、登録解除プロセスの UI を実装します。

このチュートリアルには、登録プロセスではなく登録解除プロセス用のコントローラーを構築するための手順のみが含まれています。[最初のチュートリアル][シリーズの最初のチュートリアル]で説明したように、登録プロセスの UI およびサービス メソッドは、ダウンロードに含まれていますが、サンプル アプリケーションでサービス メソッドのセキュリティを実装していないためにチュートリアルの説明から除外されています。テスト目的で、**Subscriber** 管理者ページを使用して電子メール アドレスをメーリング リストに登録できます。

### Unsubscribe ビュー モデルを Models フォルダーに追加する

1.  MVC プロジェクトの `Models` フォルダーで、ダウンロードしたプロジェクトから `UnsubscribeVM.cs` ファイルを追加します。

`UnsubscribeVM` ビュー モデルは、`Unsubscribe` コントローラーとビューとの間でデータをやり取りするために使用されます。

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

登録解除リンクには、`SubscriberGUID` が含まれています。この値は、電子メール アドレス、メーリング リスト名、およびメーリング リストの説明を `MailingList` テーブルから取得するために使用されます。ビューでは、電子メール アドレスと、登録解除するメーリング リストの説明が表示され、**[Confirm]** ボタンが表示されます。ユーザーは、このボタンをクリックして登録解除プロセスを完了する必要があります。

### Unsubscribe コントローラーを追加する

1.  MVC プロジェクトの `Controllers` フォルダーで、ダウンロードしたプロジェクトから *UnsubscribeController.cs* ファイルを追加します。

このコントローラーに含まれている `HttpGet Index` メソッドは登録解除用の初期ページを表示し、`HttpPost Index` メソッドは **[Confirm]** ボタンまたは **[Cancel]** ボタンを処理します。

`HttpGet Index` メソッドはクエリ文字列内で GUID およびメーリング リスト名を使用して、登録者に対応する `MailingList` テーブル行を取得します。次に、ビューに必要なすべての情報をビュー モデルに読み込み、**登録解除用**ページを表示します。さらに、**登録解除用**ページの初期バージョンが表示されるように、`Confirmed` プロパティを null に設定しています。

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

注: SubscriberGUID がパーティション キーまたは行キーに含まれていないため、パーティション サイズ (メーリング リスト内の電子メール アドレス数) が大きくなると、このクエリのパフォーマンスは低下します。このクエリの拡張性を向上するための代替方法の詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

`HttpPost Index` メソッドは、GUID とメーリング リスト名を使用して登録者情報を取得し、ビュー モデルのプロパティに値を設定します。次に、**[Confirm]** ボタンがクリックされた場合は、`MailingList` テーブル内の該当する Subscriber 行を削除します。**[Confirm]** ボタンが押された場合は `Confirm` プロパティを `true` に設定し、それ以外の場合は `Confirm` プロパティを `false` に設定します。`Confirm` プロパティの値は、**登録解除用**ページの確認済みバージョンを表示するか取り消し済みバージョンを表示するかを指定する値です。

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### MVC ビューを作成する

1.  MVC プロジェクトの *Views* フォルダーの下に新しいフォルダーを作成し、*Unsubscribe* という名前を付けます。

2.  新しい *Views\\Unsubscribe* フォルダーで、ダウンロードしたプロジェクトから *Index.cshtml* ファイルを追加します。

*Index.cshtml* ファイルの `Layout = null` 行では、このページの表示に \_Layout.cshtml ファイルを使用しないことを指定しています。**登録解除用**ページには、非常にシンプルな UI が表示されます。管理ページに使用されているヘッダーやフッターはありません。

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

ページの本体については、`Confirmed` プロパティによって表示内容が判断されます。このプロパティが null であれば **[Confirm]** ボタンと **[Cancel]** ボタン、プロパティが true であれば登録解除が確認されたことを示すメッセージ、プロパティが false であれば登録解除が取り消されたことを示すメッセージを表示します。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### アプリケーションをテストする

1.  Ctrl キーを押しながら F5 キーを押してプロジェクトを実行し、**[Subscribers]** をクリックします。

2.  **[作成]** をクリックして、テスト時に作成したメーリング リストに対応する新しい登録者を作成します。

    ブラウザーのウィンドウで、**Subscribers** **Index** ページを開いたままにしておきます。

3.  **サーバー エクスプローラー**を開き、**[Azure]、[ストレージ]、[(開発)]** の順に展開してノードを選択します。

4.  **[テーブル]** を展開します。**[MailingList]** テーブルを右クリックし、**[テーブルの表示]** をクリックします。

5.  追加した Subscriber 行をダブルクリックします。

6.  **[エンティティの編集]** ダイアログ ボックスで、`SubscriberGUID` の値を選択してコピーします。

    ![Azure Storage Explorer][Azure Storage Explorer]

7.  ブラウザー ウィンドウに戻ります。ブラウザーのアドレス バーで、URL に含まれる "Subscriber" を "unsubscribe?ID=[guidvalue]&listName=[listname]" ([guidvalue] は Azure ストレージ エクスプローラーからコピーした GUID、[listname] はメーリング リストの名前) に変更します。次に例を示します。

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    確認を求めるバージョンの**登録解除用**ページが表示されます。

    ![Unsubscribe page][Unsubscribe page]

8.  **[Confirm]** をクリックすると、電子メール アドレスが登録解除されたという確認が表示されます。

    ![Unsubscribe confirmed page][Unsubscribe confirmed page]

9.  **Subscribers** **Index** ページに戻り、Subscriber 行が表示されていないことを確認します。

## <a name="nextsteps"></a>次のステップ

[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]で説明したように、ASP.NET Web API サービス メソッドに共有シークレットのセキュリティが実装されていないため、このチュートリアルでは登録プロセスのコンポーネントを示しません。ただし、サービス メソッドは 2 つ目のチュートリアルで設定した IP 制限によっても保護されます。登録機能は、ダウンロードしたプロジェクトから次の各ファイルをコピーすることによって追加できます。

ASP.NET Web API サービス メソッド:

-   Controllers\\SubscribeAPI.cs

サービス メソッドで生成された電子メールに含まれる **Confirm** リンクを登録者がクリックしたときに表示される Web ページ:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

[次のチュートリアル][次のチュートリアル]では、電子メールのスケジュールを設定するワーカー ロール A の構成とプログラミングを行います。

Azure Storage のテーブル、キュー、BLOB に関する参考情報については、[このシリーズの最終チュートリアル][このシリーズの最終チュートリアル]を参照してください。

<div>

[チュートリアル 4][次のチュートリアル]

</div>

  [シリーズの最初のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Visual Studio ソリューションの作成]: #cloudproject
  [ストレージ クライアント ライブラリ NuGet パッケージの更新]: #updatescl
  [ストレージ エミュレーターを使用するためのプロジェクトの構成]: #configurestorage
  [トレースの構成と再起動の処理]: #tracing
  [Application\_Start メソッドへのテーブル、キュー、および BLOB コンテナーを作成するコードの追加]: #createifnotexists
  [Mailing List の作成とテスト]: #mailinglist
  [Subscriber コントローラーおよびビューの作成とテスト]: #subscriber
  [Message コントローラーおよびビューの作成とテスト]: #message
  [Unsubscribe コントローラーおよびビューの作成とテスト]: #unsubscribe
  [次のステップ]: #nextsteps
  [New Project menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [New Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [New Azure Cloud Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [New Azure Cloud Project dialog box - renaming the web role]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [New Azure Cloud Project dialog box - adding a worker role]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [[新しいプロジェクト] ダイアログ ボックス]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [No authentication]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [完成したソリューション]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [home page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Compute emulator in system tray]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Manage NuGet Packages for Solution in menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Azure Storage package in Manage NuGet Packages dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Selecting both projects in the Select Projects dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Web role properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Right Click Properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Invalid service configuration and definition files error]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [2 番目のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [オンデマンド転送]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/ja-jp/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Azure のストレージ クライアント ライブラリ 2.0 テーブルの詳細に関するページ]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [テーブル サービス データ モデルについて]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179338.aspx
  [PartitionKey または RowKey にパーセント '%' 文字が含まれていると、一部の Azure テーブル API が失敗する動作に関するページ]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Use .NET 4.5's async support to avoid blocking calls (ブロッキング呼び出しを回避するための .NET 4.5 の非同期サポートの使用)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [How to get most out of Windows Azure Tables (Windows Azure テーブルの最大活用)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Windows Azure Tables: Expect Continuation Tokens, Seriously! (Windows Azure テーブル: 継続トークンの重要性)]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Windows Azure Storage Logging: Using Logs to Track Storage Requests (Windows Azure Storage ログ: ログを使用したストレージ要求の追跡)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos Sample (PhluffyFotos サンプル)]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [空の MailingList の Index ページ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [MailingList Index page with rows]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Empty Subscriber Index page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Subscribers Index page with rows]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Empty Message Index page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [行が表示されている Subscribers の Index ページ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure Storage Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Unsubscribe page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [次のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [このシリーズの最終チュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
