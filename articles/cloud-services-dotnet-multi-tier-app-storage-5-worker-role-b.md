<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Azure Email Service アプリケーションで使用するワーカー ロール B (電子メール送信) の作成 (5/5)

この 5 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service のサンプル アプリケーションを作成して展開する方法を説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][シリーズの最初のチュートリアル]を参照してください。

このチュートリアルでは、次のことについて説明します。

-   クラウド サービス プロジェクトにワーカー ロールを追加する方法
-   キューをポーリングして、キューから取得した作業項目を処理する方法
-   SendGrid を使用して電子メールを送信する方法
-   `OnStop` メソッドをオーバーライドして、計画的シャットダウンを処理する方法
-   計画外シャットダウンの際、電子メールが重複して送信されないようにする方法

## このチュートリアルの内容

-   [ソリューションへのワーカー ロール B の追加][ソリューションへのワーカー ロール B の追加]
-   [Web プロジェクトへの参照の追加][Web プロジェクトへの参照の追加]
-   [プロジェクトへの SendGrid NuGet パッケージの追加][プロジェクトへの SendGrid NuGet パッケージの追加]
-   [プロジェクト設定の追加][プロジェクト設定の追加]
-   [ワーカー ロール B の起動時に実行するコードを追加][ワーカー ロール B の起動時に実行するコードを追加]
-   [ワーカー ロール B のテスト][ワーカー ロール B のテスト]
-   [次のステップ][次のステップ]

## <a name="addworkerrole"></a><span class="short-header">ワーカー ロール B の追加</span>ソリューションにワーカー ロール B を追加する

1.  ソリューション エクスプローラーで、該当するクラウド サービス プロジェクトを右クリックして **[新しいワーカー ロール プロジェクト]** を選択します。

    ![新しいワーカー ロール プロジェクト メニュー項目][]

2.  **[新しいロール プロジェクトの追加]** ダイアログ ボックスで **[Visual C#]** をクリックし、**[ワーカー ロール]** を選択します。プロジェクト名として「WorkerRoleB」と入力し、**[追加]** をクリックします。

    ![新しいロール プロジェクトの追加 ダイアログ ボックス][]

## <a name="addreference"></a>Web プロジェクトへの参照の追加

エンティティ クラスが定義されている Web プロジェクトへの参照を追加する必要があります。ワーカー ロール B にあるエンティティ クラスを使用して、このアプリケーションで使用する Azure テーブルのデータを読み書きします。

1.  WorkerRoleB プロジェクトを右クリックして、**[参照の追加]** を選択します。

2.  **[参照マネージャー]** で、MvcWebRole プロジェクトへの参照を追加します。

    ![MvcWebRole への参照を追加][MvcWebRole への参照を追加]

## <a name="addsendgrid"></a>プロジェクトへの SendGrid NuGet パッケージの追加

SendGrid を使用して電子メールを送信するには、SendGrid NuGet パッケージをインストールする必要があります。

1.  **ソリューション エクスプローラー**で WorkerRoleB プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

    ![NuGet パッケージの管理][NuGet パッケージの管理]

2.  **[NuGet パッケージの管理]** ダイアログ ボックスで **[オンライン]** タブをクリックし、検索ボックスに「sendgrid」と入力して Enter キーを押します。

3.  **SendGrid** パッケージで **[インストール]** をクリックします。

    ![Sendgrid パッケージのインストール][Sendgrid パッケージのインストール]

4.  ダイアログ ボックスを閉じます。

## <a name="addsettings"></a>プロジェクト設定の追加

ワーカー ロール A と同様、ワーカー ロール B についても、テーブル、クエリー、BLOB を操作するにはストレージ アカウントの資格情報が必要です。さらに、ワーカー ロール B が電子メールを送信するには、SendGrid サービスの呼び出しに資格情報を埋め込む必要があります。送信する電子メールに挿入する登録解除リンクを生成するには、このアプリケーションの URL がわかっていなければなりません。これらの値はプロジェクト設定に格納されます。

ストレージ アカウントの資格情報を設定する手順は、[3 番目のチュートリアル][3 番目のチュートリアル]で説明した手順と同じです。

1.  **ソリューション エクスプローラー**で、該当するクラウド サービス プロジェクトの **[ロール]** の下の **[WorkerRoleB]** を右クリックし、**[プロパティ]** をクリックします。

2.  **[設定]** タブを選択します。

3.  **[サービス構成]** ボックスの一覧で **[すべての構成]** が選択されていることを確認します。

4.  **[設定]** タブを選択し、**[設定の追加]** をクリックします。

5.  **[名前]** 列に「StorageConnectionString」と入力します。

6.  **[種類]** ボックスの一覧の **[接続文字列]** を選択します。

7.  その行の右端にある **[...]** ボタンをクリックして、**[ストレージ アカウント接続文字列]** ダイアログ ボックスを開きます。

8.  **[ストレージ接続文字列の作成]** ダイアログ ボックスで **[Azure ストレージ エミュレーター]** をクリックし、**[OK]** をクリックします。

次に、ワーカー ロール B のみが使用する 3 つの新しい設定を作成して、構成します。

1.  **[プロパティ]** ウィンドウの **[設定]** タブで **[設定の追加]** をクリックし、種類が **[文字列]** である 3 つの新しい設定を追加します。

    -   **名前**: SendGridUserName、**値**: [2 番目のチュートリアル][2 番目のチュートリアル]で指定した SendGrid ユーザー名

    -   **名前**: SendGridPassword、**値**: SendGrid パスワード

    -   **名前**: AzureMailServiceURL、**値**: アプリケーションをデプロイするときに割り当てられるベース URL (<http://sampleurl.cloudapp.net> など)

    ![WorkerRoleB プロジェクトの新しい設定][WorkerRoleB プロジェクトの新しい設定]

## <a name="addcode"></a> ワーカー ロール B の起動時に実行するコードを追加

1.  WorkerRoleB プロジェクトで WorkerRole.cs を削除します。

2.  WorkerRoleB プロジェクトで、ダウンロードしたプロジェクトから WorkerRoleB.cs ファイルを追加します。

3.  ソリューションをビルドします。

    ビルド エラーが発生した場合は、Azure ストレージ NuGet パッケージが複数のバージョンになっている可能性があります。その場合、問題を解決するには、ソリューションの NuGet パッケージ マネージャーに移動し、インストール済みのパッケージを選択して下にスクロールし、Azure ストレージ·パッケージの 2 つのインスタンスがあることを確認します。Azure ストレージ 4.0.0 のエントリを選択し、インストールされているプロジェクトからこのエントリを削除します。次に、Azure ストレージ 3.0.x のエントリを選択し、このエントリが不足しているプロジェクトにインストールします。Visual Studio を閉じて再起動し、ソリューションを再度ビルドします。

4.  クラウド サービス プロジェクトが、ソリューションのスタートアップ プロジェクトになっていることを確認します。

### OnStart メソッド

ワーカー ロール A の場合と同様、`OnStart` メソッドでは、Azure ストレージのエンティティを操作する際に必要となるコンテキスト クラスを初期化します。さらに、`Run` メソッドで使用するテーブル、キュー、BLOB コンテナーがすべて存在するかどうか確認します。

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

ワーカー ロール A と異なるのは、BLOB コンテナーと登録キューが存在しない場合はこれらも作成されることです。BLOB コンテナーは、電子メール本文 (HTML 形式とプレーン テキスト形式) が保存されたファイルを取得するときに使用します。登録キューは、登録確認メールを送信するときに使用します。

### Run メソッド

`Run` メソッドは、2 つのキューから取得した作業項目を処理します。1 つは、電子メール リストに送信するメッセージ (ワーカー ロール A が作成する作業項目) で使用するキュー。もう 1 つは、登録確認メール (MvcWebRole プロジェクトのサブスクライブ API メソッドが作成する作業項目) で使用するキューです。

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

このコードは、ワーカー ロール B がシャットダウンするまで無限ループで実行されます。メイン キューで作業項目が見つかった場合はそれを処理し、登録キューをチェックします。

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

どちらのキューにも待機中の作業項目がない場合、60 秒間スリープしてからこのループを再開します。

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

ここで 60 秒スリープするのは、[前回のチュートリアル][前回のチュートリアル]で説明したように、Azure ストレージのトランザクション コストを最小限に抑えるためです。

[GetMessage][GetMessage] メソッドを実行してキューからキュー項目を取り出したとき、その後 30 秒間、同じキューにアクセスするその他すべてのワーカー ロールと Web ロールにはそのキュー項目が見えなくなります。これは、複数のワーカー ロール インスタンスが同じキュー メッセージを処理しないようにするためのしくみです。この *"排他的占有"* 時間 (キュー項目へのアクセスが制限される時間) を明示的に設定するには、[表示タイムアウト][表示タイムアウト] パラメーターを `GetMessage` メソッドに渡します。ワーカー ロール B がキュー メッセージを処理するのに 30 秒以上かかる場合は、他のロール インスタンスがそのメッセージを同時に処理するのを防ぐため、排他的占有時間を長くする必要があります。

ただし、排他的占有時間をあまり長くするのは望ましくありません。たとえば、排他的占有時間を 48 時間に設定したとすると、メッセージをキューから取り出した後でワーカー ロールが突然シャットダウンした場合、別のワーカー ロールはそのメッセージを 48 時間処理できません。排他的に占有できるのは最大 7 日間です。

[GetMessages][GetMessages] メソッド (メソッド名の末尾に "s" が付いていることに注意) を使用すれば、1 回の呼び出しで最大 32 のメッセージをキューから取り出すことができます。キューにアクセスするたびに多少のトランザクション コストがかかります。また、返されるメッセージ数が 32件であっても、0 件であっても、かかるトランザクション コストは同じです。次のコードは、1 回の呼び出しで最大 32 のメッセージを取得して処理します。

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

`GetMessages` を使用して複数のメッセージを削除する場合、すべてのメッセージを処理できるだけの十分な表示タイムアウトを設定してください。制限時間が終了すると、他のロール インスタンスもそのメッセージへアクセスできるようになります。他のロール インスタンスが同じメッセージにアクセスした場合、最初に処理していたインスタンスは作業項目の処理が完了した時点でメッセージを削除できません。

### ProcessQueueMessage メソッド

メイン キューに作業項目が見つかると、`Run` メソッドは `ProcessQueueMessage` を呼び出します。

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

処理時に例外が発生するようなメッセージを有害なメッセージといいます。メッセージがキューから既に 5 回取り出されている場合は、そのメッセージを処理不可能とみなし、それ以上は処理せずにキューから削除します。運用アプリケーションでは有害メッセージを削除するのではなく、"デッド メッセージ" キューへ移動して分析します。

次のコードは、キュー メッセージのパーティション キーと行キからー SendEmail 行と再起動フラグを取得します。

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

予期しないシャットダウンの後でこのメッセージの処理を再開した場合、`messagearchive` テーブルをチェックして、電子メールが既に送信されているかどうかを判断します。既に送信されている場合、`SendEmail` 行が存在する場合はそれを削除し、さらに該当するキュー メッセージを削除します。

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

次に、`message` テーブルから `SendEmail` 行を取得します。この行には、電子メールの送信に必要な (BLOB 以外の) すべての情報が格納されています。BLOB には、HTML 形式とプレーン テキスト形式の電子メール本文が保存されます。

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

次に、電子メールを送信して、`SendEmail` 行をアーカイブへ移動します。

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

SendEmail 行を messagearchive テーブルへ移動する処理は複数のテーブルに影響を与えるため、トランザクションでは実行できません。

最後に、すべて正常に処理された場合は、キュー メッセージを削除します。

            sendEmailQueue.DeleteMessage(msg);

### SendEmailToList メソッド

SendGrid を使用した電子メール送信は、実際には `SendEmailToList` メソッドによって実行されます。SendGrid 以外のサービスを使用するときは、このメソッドのコードを変更するだけです。

**注:** プロジェクト設定の資格情報が無効な場合、SendGrid を呼び出すことができません。ただし、それについてのエラー情報は返されません。運用アプリケーションで SendGrid を使用する場合、Web API に別の資格情報を設定しておけば、管理者が自分の SendGrid ユーザー アカウントのパスワードを変更したときサイレント エラーが発生するのを防ぐことができます。詳細については、「[SendGrid MultiAuth - Multiple Account Credentials (SendGrid MultiAuth - 複数のアカウント資格情報)][SendGrid MultiAuth - Multiple Account Credentials (SendGrid MultiAuth - 複数のアカウント資格情報)]」を参照してください。[][]<https://sendgrid.com/credentials></a> で資格情報を設定できます。

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

`GetBlobText` メソッドでは、BLOB サイズを取得し、パフォーマンス上の理由からその値を使用して `MemoryStream` オブジェクトを初期化します。BLOB サイズを指定しない場合は `MemoryStream` によって 256 バイトが割り当てられ、ダウンロードがこのサイズを超えるときはさらに 512 バイトが割り当てられます (割り当てサイズが 2 倍になります)。BLOB サイズが大きい場合、ダウンロードの開始時に適切なサイズを割り当てるのに比べ、このプロセスでは十分に対応できません。

### ProcessSubscribeQueueMessage メソッド

登録キューに作業項目が見つかると、`Run` メソッドは `ProcessSubscribeQueueMessage` を呼び出します。

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

このメソッドは次のタスクを実行します。

-   メッセージが "有害" メッセージの場合、それを記録して削除する
-   キュー メッセージから登録者 GUID を取得する
-   GUID を使用して、MailingList テーブルから登録者情報を取得する
-   確認メールを新規登録者に送信する
-   キュー メッセージを削除する

リストに送信される電子メールと同様、電子メールの実際の送信は別のメソッドで処理されます。そのため、必要であれば他の電子メール サービスに簡単に変更できます。

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>ワーカー ロール B のテスト

1.  F5 キーを押してアプリケーションを実行します。

2.  **Messages** ページヘ移動すると、ワーカー ロール A をテストするために作成したメッセージが表示されます。1 分ほどたったら、Web ページを最新の状態に更新してください。先ほどの行がリストから削除されていることがわかります。アーカイブへ移動されたためです。

3.  電子メールが配送される受信ボックスをチェックします。SendGrid による電子メール送信や電子メール クライアントへの配信に少し時間がかかることがあります。電子メールが届いていない場合は、少し待ってください。迷惑メール フォルダーに振り分けられる可能性もあるので、チェックしてください。

## <a name="nextsteps"></a>次のステップ

Azure Email Service アプリケーションを最初から作成し、ダウンロードした完成版プロジェクトと同じ状態になりました。クラウドに展開してテストし、運用環境へ昇格するには、[2 番目のチュートリアル][2 番目のチュートリアル]で説明した手順に従います。

Azure ストレージ テーブル サービス クエリでの LINQ の使用方法を示すサンプル アプリケーションについては、「[PhluffyFotos][PhluffyFotos]」を参照してください。

Azure ストレージの詳細については、次のリソースを参照してください。

-   [Essential Knowledge for Windows Azure Storage (Windows Azure ストレージに関する基本知識)][Essential Knowledge for Windows Azure Storage (Windows Azure ストレージに関する基本知識)] (Bruno Terkaly のブログ)

Azure テーブル サービスの詳細については、次のリソースを参照してください。

-   [Essential Knowledge for Azure Table Storage (Azure テーブル ストレージに関する基本知識)][Essential Knowledge for Azure Table Storage (Azure テーブル ストレージに関する基本知識)] (Bruno Terkaly のブログ)
-   [How to get the most out of Windows Azure Tables (Windows Azure テーブルの活用方法)][How to get the most out of Windows Azure Tables (Windows Azure テーブルの活用方法)] (Azure ストレージ チームのブログ)
-   [.NET でテーブル ストレージ サービスを使用する方法][.NET でテーブル ストレージ サービスを使用する方法]
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive (Windows Azure ストレージ クライアント ライブラリ 2.0 のテーブルに関する詳細情報)][Windows Azure Storage Client Library 2.0 Tables Deep Dive (Windows Azure ストレージ クライアント ライブラリ 2.0 のテーブルに関する詳細情報)] (Azure ストレージ チームのブログ)
-   [Real World:Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)][Real World:Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)]

Azure キュー サービスおよび Azure のサービス バス キューの詳細については、次のリソースを参照してください。

-   [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) (キューを中心とした作業パターン (Windows Azure で構築する実用的なクラウド アプリケーション))][Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) (キューを中心とした作業パターン (Windows Azure で構築する実用的なクラウド アプリケーション))]
-   [Azure Queues and Azure Service Bus Queues Compared and Contrasted (Azure キューと Azure のサービス バス キューの比較)][Azure Queues and Azure Service Bus Queues Compared and Contrasted (Azure キューと Azure のサービス バス キューの比較)]
-   [How to use the Queue Storage Service in .NET (.NET でキュー ストレージ サービスを使用する方法)][How to use the Queue Storage Service in .NET (.NET でキュー ストレージ サービスを使用する方法)]

Azure BLOB サービスの詳細については、次のリソースを参照してください。

-   [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) (非構造化 BLOB ストレージ (Windows Azure で構築する実用的なクラウド アプリケーション))][Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) (非構造化 BLOB ストレージ (Windows Azure で構築する実用的なクラウド アプリケーション))]
-   [.NET での Azure BLOB ストレージ サービスの使用方法][.NET での Azure BLOB ストレージ サービスの使用方法]

Azure クラウド サービスのオートスケーリングについては、次のリソースを参照してください。

-   [オートスケーリング アプリケーション ブロックの使用方法][オートスケーリング アプリケーション ブロックの使用方法]
-   [Autoscaling and Azure (オートスケーリングと Azure)][Autoscaling and Azure (オートスケーリングと Azure)]
-   [Building Elastic, Autoscalable Solutions with Azure (Azure で柔軟性の高いオートスケーリング ソリューションを構築)][Building Elastic, Autoscalable Solutions with Azure (Azure で柔軟性の高いオートスケーリング ソリューションを構築)] (MSDN channel 9 動画)

## <a name="Acknowledgments"></a><span class="short-header">謝辞</span>謝辞

これらのチュートリアルおよびサンプル アプリケーションは [Rick Anderson][Rick Anderson] と Tom Dykstra が執筆しました。今回、協力していただいた次の方々に感謝の意を表します。

-   Barry Dorrans (Twitter [@blowdart][@blowdart])
-   [Cory Fowler][Cory Fowler] (Twitter [@SyntaxC4][@SyntaxC4] )
-   [Joe Giardino][Joe Giardino]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][Scott Hunter] (Twitter:[@coolcsh][@coolcsh])
-   [Brian Swan][Brian Swan]
-   [Daniel Wang][Daniel Wang]
-   ご意見をお寄せいただいた Developer Advisory Council の方々
	-   Damir Arh
	-   Jean-Luc Boucho
	-   Carlos dos Santos
	-   Mike Douglas
	-   Robert Fite
	-   Gianni Rosa Gallina
	-   Fabien Lavocat
	-   Karl Ots
	-   Carlos-Alejandro Perez
	-   Sunao Tomita
	-   Perez Jones Tsisah
	-   Michiel van Otegem


  [シリーズの最初のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
  [ソリューションへのワーカー ロール B の追加]: #addworkerrole
  [Web プロジェクトへの参照の追加]: #addreference
  [プロジェクトへの SendGrid NuGet パッケージの追加]: #addsendgrid
  [プロジェクト設定の追加]: #addsettings
  [ワーカー ロール B の起動時に実行するコードを追加]: #addcode
  [ワーカー ロール B のテスト]: #testing
  [次のステップ]: #nextsteps
  [ 新しいワーカー ロール プロジェクト メニュー項目]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [新しいロール プロジェクトの追加 ダイアログ ボックス]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [MvcWebRole への参照を追加]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [NuGet パッケージの管理]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Sendgrid パッケージのインストール]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [3 番目のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [2 番目のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [WorkerRoleB プロジェクトの新しい設定]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [前回のチュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee741827.aspx
  [表示タイムアウト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - Multiple Account Credentials (SendGrid MultiAuth - 複数のアカウント資格情報)]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Essential Knowledge for Windows Azure Storage (Windows Azure ストレージに関する基本知識)]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Essential Knowledge for Azure Table Storage (Azure テーブル ストレージに関する基本知識)]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [How to get the most out of Windows Azure Tables (Windows Azure テーブルの活用方法)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [.NET でテーブル ストレージ サービスを使用する方法]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/table-services/
  [Windows Azure Storage Client Library 2.0 Tables Deep Dive (Windows Azure ストレージ クライアント ライブラリ 2.0 のテーブルに関する詳細情報)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Real World:Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh508997.aspx
  [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) (キューを中心とした作業パターン (Windows Azure で構築する実用的なクラウド アプリケーション))]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Azure Queues and Azure Service Bus Queues Compared and Contrasted (Azure キューと Azure のサービス バス キューの比較)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh767287.aspx
  [How to use the Queue Storage Service in .NET (.NET でキュー ストレージ サービスを使用する方法)]: /ja-jp/develop/net/how-to-guides/queue-service/
  [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) (非構造化 BLOB ストレージ (Windows Azure で構築する実用的なクラウド アプリケーション))]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [.NET での Azure BLOB ストレージ サービスの使用方法]: /ja-jp/develop/net/how-to-guides/blob-storage/
  [オートスケーリング アプリケーション ブロックの使用方法]: /ja-jp/develop/net/how-to-guides/autoscaling/
  [Autoscaling and Azure (オートスケーリングと Azure)]: http://msdn.microsoft.com/ja-jp/library/hh680945(v=PandP.50).aspx
  [Building Elastic, Autoscalable Solutions with Azure (Azure で柔軟性の高いオートスケーリング ソリューションを構築)]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
