<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Azure 電子メール サービス アプリケーションで使用する Worker ロール A (電子メール スケジューラ) の作成 (4/5)

この 4 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service のサンプル アプリケーションを作成してデプロイする方法を説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

このチュートリアルでは、次のことについて説明します。

-   Azure ストレージ テーブルを照会し、更新する方法
-   キューに作業項目を追加し、別のワーカー ロールが処理できるようにする方法
-   `OnStop` メソッドをオーバーライドして、計画的シャットダウンを処理する方法
-   計画外シャットダウンの際、電子メールの不達や重複が発生しないようにする方法
-   サーバー エクスプローラーを使用して、Azure のストレージのテーブルを使用するワーカー ロールをテストする方法

クラウド サービス プロジェクトを作成した時点で、ワーカー ロール A プロジェクトも作成しました。あとは、ワーカー ロールをプログラミングするだけです。

## このチュートリアルの内容

-   [Web プロジェクトへの参照の追加][Web プロジェクトへの参照の追加]
-   [SendEmail モデルの追加][SendEmail モデルの追加]
-   [ワーカー ロール B の起動時に実行するコードを追加][ワーカー ロール B の起動時に実行するコードを追加]
-   [Worker ロール A のテスト][Worker ロール A のテスト]
-   [次のステップ][次のステップ]

## <a name="addref"></a><span class="short-header">プロジェクト参照の追加</span>Web プロジェクトへの参照を追加する

エンティティ クラスが定義されている Web プロジェクトへの参照を追加する必要があります。ワーカー ロール B にある同じエンティティ クラスを使用して、このアプリケーションで使用する Azure テーブルのデータを読み書きします。

**注:** 運用アプリケーションでは、ワーカー ロール プロジェクトから Web プロジェクトへの参照を設定しません。このワーカー ロールで不要な依存アセンブリを参照することになるからです。通常、クラス ライブラリ プロジェクトには共有モデル クラスを保持し、Web ロール プロジェクトとワーカー ロール プロジェクトの両方がこのクラス ライブラリ プロジェクトを参照します。ソリューション構成をシンプルにするため、このチュートリアルではモデル クラスを Web プロジェクトに格納します。

1.  WorkerRoleA プロジェクトを右クリックして、**[追加]**、**[参照]** の順に選択します。

2.  **[参照マネージャー]** で MvcWebRole プロジェクトへの参照を追加し、**[OK]** をクリックします。

    ![MvcWebRole への参照を追加][MvcWebRole への参照を追加]

## <a name="addmodel"></a>SendEmail モデルの追加

Worker ロール A は `Message` テーブル内で `SendEmail` の行を作成し、Worker ロール B はそれらの行を読み取って、電子メールの送信に必要な情報を取得します。次の図は、`Message` テーブルに格納されている 2 つの `Message` 行と 3 つの `SendEmail` 行のプロパティを示しています。

![sendmail 行を含む message テーブル][sendmail 行を含む message テーブル]

`Message` テーブルのこれらの行にはいくつかの目的があります。

-   ワーカー ロール B が各電子メールを送信する際に必要となるすべての情報を提供します。
-   電子メールが既に送信されているかどうか追跡します。これは、ワーカー ロールに障害が発生して再起動したとき、同じメールが何度も送信されないようにするためです。
-   Worker ロール A は、このテーブルを参照して "メッセージがすべての登録者にメール送信された" ことを確認し、そのメッセージの状態を `Complete` に設定します。

`SendEmail` の行を読み書きするにはモデル クラスが必要です。ワーカー ロール A とワーカー ロール B の両方がモデル クラスにアクセスでき、その他すべてのモデル クラスを Web プロジェクトで定義する必要があるので、これについても Web プロジェクトで定義します。

1.  Web プロジェクトの *Models* フォルダーで、ダウンロードしたプロジェクトから *SendEmail.cs* ファイルを追加します。

このコードは他のモデル クラスとほぼ同じですが、このモデルには UI が関連付けられていないので DataAnnotations 属性がありません。MVC コントローラーでは UI を使用しません。

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">ワーカー ロール コードの追加</span>ワーカー ロールの起動時に実行するコードを追加

WorkerRoleA プロジェクトで、プロジェクト テンプレートは次のコードを使用して *WorkerRole.cs* を作成しました。

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

これはワーカー ロールの既定のテンプレート コードです。この中の `OnStart` メソッドには、Worker ロール インスタンスの起動時にのみ実行される初期化コードを挿入します。`OnStart` メソッドが完了した後で `Run` メソッドが呼び出されます。実際の初期化コードに書き換えて、実行してください。

1.  WorkerRoleA プロジェクトで *WorkerRole.cs* を削除し、ダウンロードしたプロジェクトから *WorkerRoleA.cs* ファイルを追加します。

### OnStart メソッド

`OnStart` メソッドは、Azure のストレージ エンティティの操作に必要なコンテキスト オブジェクトを初期化します。さらに、`Run` メソッドで使用するテーブル、キュー、BLOB コンテナーがすべて存在するかどうかを確認ます。これらのタスクを実行するコードは、前に紹介した MVC コントローラー コンストラクターのコードと似ています。このメソッドが使用する接続文字列は後で構成します。

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

Azure のストレージの操作に関する一部のドキュメントには、ループ内でトランスポート エラーをチェックする初期化コードが記載されています。現在、API には再試行機能が組み込まれており、3 回まではネットワーク エラーに対応できるようになっているので、トランスポート エラーをチェックする必要はありません。

`OnStart` メソッドによって呼び出される `ConfigureDiagnostics` メソッドは、トレースを設定し、`Trace.Information` メソッドおよび `Trace.Error` メソッドの出力を表示できるようにします。このメソッドについては、[2 番目のチュートリアル][2 番目のチュートリアル]の説明を参照してください。

[ServicePointManager.DefaultConnectionLimit][ServicePointManager.DefaultConnectionLimit] 設定は、.NET で開くことができる最大の同時 TCP 接続数を指定します。[ServicePointManager][ServicePointManager] クラスのドキュメントでは、Worker ロール インスタンスが接続する一意のホストはそれぞれ個別の接続であることを説明しています。たとえば、この Worker ロールは、テーブル、BLOB、キューへの 3 つの同時接続を行います。SQL Server などの一部の接続では、クライアント ソフトウェアが接続プールを行うことで、`ServicePointManager` が管理する同時接続数を削減できます。最適な `DefaultConnectionLimit` の数は、接続先のバックエンド サービスによって異なります。サービスによっては 500 の開かれた接続が適している場合がありますが、5 つの接続のみで上限に達する場合もあります。プロセッサごとに 12 の接続制限は、多くのシナリオでうまく動作する一般的なガイドラインです。

### OnStop メソッド

`OnStop` メソッドはグローバル変数 `onStopCalled` を true に設定し、`Run` メソッドによってグローバル変数 `returnedFromRunMethod` が true に設定されるまで待機します。この変数は、クリーン シャットダウン可能な状態であることを知らせます。

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

次のいずれかの理由で Worker ロールがシャットダウンするとき、`OnStop` メソッドが呼び出されます。

-   Azure が仮想マシン (Web ロール インスタンスまたはワーカー ロール インスタンス) を再起動する必要がある、または仮想マシンをホストしている物理マシンを再起動する必要がある。
-   Azure 管理ポータルの **[停止]** ボタンを使用して、クラウド サービスを停止した。
-   クラウド サービス プロジェクトの更新をデプロイした。

`Run` メソッドは変数 `onStopCalled` を監視し、この変数が `true` に変わった時点で新しい作業項目の処理を中止します。`OnStop` メソッドと `Run` メソッド間のこうした調整により、ワーカー プロセスを正しい手順でシャットダウンすることができます。

常に安全で信頼性が高く、パフォーマンスに優れたプラットフォームを維持するため、Azure はオペレーティング システムの更新プログラムを定期的にインストールします。更新プログラムをインストールするには、通常、クラウド サービスをホストしているマシンをシャットダウンして再起動する必要があります。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)][Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)]」を参照してください。

### Run メソッド

`Run` メソッドは次の 2 つの機能を実行します。

-   `message` テーブルをスキャンし、送信予定日が今日までであるメッセージを探します。これらのメッセージについてはキュー作業項目がまだ作成されていません。

-   `message` テーブルをスキャンして状態を確認し、キュー作業項目はすべて作成されているが、まだ登録者全員に送信されていないメッセージを探します。該当するメッセージが見つかった場合は、そのメッセージの `SendEmail` 行を調べて未送信電子メールの有無をチェックします。電子メールがすべて送信された時点で状態を `Completed` に更新し、その `message` 行をアーカイブに保存します。

Run メソッドはグローバル変数 `onStopCalled` もチェックします。この変数が `true` の場合、Run メソッドは新しい作業項目の取り出しを中止し、既に開始しているタスクが完了した時点で再開します。

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }

                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

`while` ブロックの無限ループ内ですべての作業が実行されることに注意してください。`while` ブロックのすべてのコードが `try`-`catch` ブロックでラップされ、例外が未処理状態にならないようにしています。未処理の例外が発生した場合は Azure が [UnhandledException][UnhandledException] イベントを生成します。これを受けてワーカー プロセスが終了し、ロールがオフラインになります。ワーカー ロールは Azure によって再起動されますが、これには数分かかります。`try` ブロックが `TraceError` を呼び出してエラーを記録し、60 秒間スリープします。これは、エラーが繰り返し発生したとき、エラー メッセージが何度も記録されないようにするためです。運用アプリケーションの場合、必要であれば `try` ブロックで管理者に電子メールを送信します。

`Run` メソッドは、`message` テーブル内で、送信予定日が翌日以前に設定されている `message` 行へのクエリを処理します。

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**注:** 処理したメッセージ行を `messagearchive` テーブルに移動しておけば、このクエリを実行するとき、検索条件として `PartitionKey` と `RowKey` を指定するだけで済むというメリットがあります。処理済みの行をアーカイブしないとすると、キー以外のフィールド (`Status`) も指定する必要があり、検索対象行が増えてしまいます。テーブル サイズが大きければクエリに時間がかかり、継続トークンを取得するようになります。

メッセージが `Pending` 状態にあるときは、まだ処理が開始されていません。`Queuing` 状態の場合、処理は開始されていますが、まだすべてのキュー メッセージが作成されたわけではありません。この場合、ワーカー ロール B は電子メールを送信するたびにステータスをチェックし、電子メールがまだ送信されていないことを確認する必要があります。そのための変数が `restartFlag` です。

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

次に、このコードは `Pending` 状態の `message` 行を `Queuing` に設定します。さらに、これらの行に加え、既に `Queuing` 状態であった行について `ProcessMessage` メソッドを呼び出し、メッセージを電子メールで送信するためのキュー作業項目を作成します。

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

ステータスが `Queuing` であるメッセージを処理した後、`Message` 行のステータスを `Processing` に設定します。`Pending` 状態または `Queuing` 状態でない `message` テーブルの行は、既に `Processing` 状態です。これらの行について、メッセージがすべての登録者にメール送信されたかどうかをチェックするメソッドを呼び出します。すべての登録者にメール送信されている場合、その `message` 行はアーカイブされます。

クエリによって取得されたすべてのレコードを処理した後、1 分間スリープします。

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

データが何も返されなくても、Azure のストレージの各クエリについて最小限の料金がかかります。そのため、繰り返しスキャンすると Azure の費用が不必要にかさみます。このチュートリアルを執筆している時点での料金は 100 万トランザクションあたり $0.10 (クエリーはトランザクションとしてカウントされます)。したがって、スリープ時間が 1 分より短ければ、テーブルをスキャンして送信メッセージを探すための料金が最小限ですみます。料金設定の詳細については、[最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

**スレッドおよび最適な CPU 使用率に関する注意事項:** `Run` メソッドには 2 つのタスクがあり (電子メールの照会と処理が完了したメッセージのチェック)、これらは 1 つのスレッドで順次実行されます。S サイズの仮想マシン (VM) は 1.75 GB RAM と CPU 1 基を搭載しており、これらのタスクを単一スレッドで順次実行する場合は問題ありません。S サイズの VM に搭載されているメモリではアプリケーションを効率的に実行できない場合はどうでしょうか。M サイズの VM には 3.5 GB RAM と CPU 2 基が搭載されています。ただし、実行するアプリケーションはシングル スレッドなので、CPU を 1 つしか使用しません。両方の CPU を利用するには、CPU ごとにワーカー スレッドを作成する必要があります。さらに、CPU ごとにワーカー スレッドを作成したとしても、実行するスレッドが 1 つだけでは CPU がフル活用されません。スレッドがネットワークや I/O を呼び出したとき、その呼び出しが完了するまで待機する必要があり、その間は有用な処理を実行できません。2 つのスレッドを使用するように `Run` メソッドを実装すれば、一方のスレッドがネットワーク操作または I/O 操作が完了するのを待っている間、もう一方のスレッドで有益な処理を実行できます。

### ProcessMessage メソッド

`ProcessMessage` メソッドは、すべての電子メール アドレスを取得してメール送信先リストに取り込み、各電子メール アドレスのキュー作業項目を作成します。さらにこのとき、`Message` テーブルに `SendEmail` 行も作成します。Worker ロール B はこれらの行から電子メールの送信に必要な情報を取得します。また、各電子メールの送信/未送信状態を管理する `EmailSent` プロパティも含まれています。

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }

            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

このコードでは、まず、メール送信先リストの `mailinglist` テーブルからメーリング リスト行を取得します。この行には、ワーカー ロール B が電子メールを送信する際に必要となる "差出人" 電子メール アドレスが指定されています。

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

次に、`mailinglist` テーブルを照会し、送信先メーリング リストで使用するすべての登録者行を取得します。

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

この照会結果を処理するループで、登録者電子メール アドレスが有効かどうかをチェックし、有効でない場合は電子メールが照会されません。

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

次に、`message` テーブルに \``SendEmail`\` 行を作成します。この行には、ワーカー ロール B が電子メールを送信するときに使用する情報が格納されます。この行の作成時には `false` プロパティが `EmailSent` に設定されます。

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

ワーカー ロール A に障害が発生して再起動した場合、この行が既に存在する可能性があるので、ここでは "upsert" 操作を使用します。

最後に、各電子メール アドレスについてキュー作業項目を作成し、Worker ロール B が電子メールを送信できるようにします。キュー作業項目は、ここで作成された `SendEmail` 行のパーティション キーと行キーの値、および前に設定した再起動フラグで構成されます。`SendEmail` 行には、Worker ロール B が電子メールを送信する際に必要となるすべての情報が含まれています。

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### CheckAndUpdateStatusIfComplete メソッド

`CheckAndUpdateStatusIfComplete` メソッドは、Processing 状態のメッセージがすべての登録者に送信されたかどうかをチェックします。未送信の電子メールがなければ、行の状態を `Completed` に更新して、その行をアーカイブに移動します。

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;
            }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Worker ロール A のテスト

1.  F5 キーを押してアプリケーションを実行します。

2.  管理者用の Web ページを使用してメーリング リストを作成し、そのメーリング リストの登録者を作成します。少なくとも 1 人の登録者の `Verified` プロパティを `true` に設定し、メールを受信できるアドレスを電子メール アドレスとして設定します。

    ワーカー ロール B を実装するまで電子メールは送信されませんが、ワーカー ロール B をテストするときに同じテスト データを使用します。

3.  作成したメーリング リストに送信するメッセージを作成し、送信予定日を今日または過去の日付に設定します。

    ![pending 状態の新しいメッセージ][pending 状態の新しいメッセージ]

4.  (Run メソッドのスリープ時間が 1 分であるため) 1 分強たったら、Messages Web ページを再表示します。状態が Processing に変わります (実際には最初に Queuing に変わりますが、その後すぐに Processing に変わります)。

    ![processing 状態の新しいメッセージ][processing 状態の新しいメッセージ]

5.  **[サーバー エクスプローラー]** を開き、開発ストレージのノードを開きます。

6.  **[キュー]** を展開して **[azuremailqueue]** を右クリックし、**[キューの表示]** をクリックします。

    送信先電子メール リストの検証済み登録者ごとに 1 件のキュー メッセージが表示されます。

    ![ASE のキュー メッセージ][ASE のキュー メッセージ]

7.  キュー メッセージをダブルクリックします。

    キュー メッセージの内容が表示されます。具体的には、パーティション キー (日付)、行キー (MessageRef 値と電子メール アドレス)、再起動フラグがコンマで区切られて表示されます。

    ![ASE に表示されたキュー メッセージの内容][ASE に表示されたキュー メッセージの内容]

8.  **[メッセージの表示]** ダイアログ ボックスを閉じます。

9.  **[テーブル]** ノードを展開して **[メッセージ]** テーブルを右クリックし、**[テーブルの表示]** をクリックします。

    スケジュール設定したメッセージ (行キーの値が "message") と、その後に検証済み登録者の行 (行キーの値が電子メール アドレス) が表示されます。

10. 行キーに "message" が設定されている行をダブルクリックします。Web ロールが作成した行の内容が表示されます。

    ![メッセージ行][メッセージ行]

11. 行キーに電子メール アドレスが設定されている行をダブルクリックします。Worker ロール A が作成した `SendEmail` 行の内容が表示されます。

    ![Message テーブルの SendEmail 行][Message テーブルの SendEmail 行]

## <a name="nextsteps"></a>次のステップ

ここでは Worker ロール A を作成し、Worker ロール B が電子メールを送信する際に必要となるテーブル行とキュー メッセージが作成されていることを検証しました。[次のチュートリアル][次のチュートリアル]では、ワーカー ロール B を作成してテストします。

Azure Storage のテーブル、キュー、BLOB に関する参考情報については、[このシリーズの最終チュートリアル][次のチュートリアル]を参照してください。

<div><a href="/ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">チュートリアル 5</a></div>

  [シリーズの最初のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Web プロジェクトへの参照の追加]: #addref
  [SendEmail モデルの追加]: #addmodel
  [ワーカー ロール B の起動時に実行するコードを追加]: #addcode
  [Worker ロール A のテスト]: #testing
  [次のステップ]: #nextsteps
  [MvcWebRole への参照を追加]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [sendmail 行を含む message テーブル]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [2 番目のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/ja-jp/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/ja-jp/library/system.net.servicepointmanager.aspx
  [UnhandledException]: http://msdn.microsoft.com/ja-jp/library/system.appdomain.unhandledexception.aspx
  [pending 状態の新しいメッセージ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [processing 状態の新しいメッセージ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [ASE のキュー メッセージ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [ASE に表示されたキュー メッセージの内容]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [メッセージ行]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [Message テーブルの SendEmail 行]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [次のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
