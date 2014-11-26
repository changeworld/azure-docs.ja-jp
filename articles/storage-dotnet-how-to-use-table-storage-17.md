<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="テーブル サービス (2.0)" pageTitle="テーブル ストレージの使用方法 | Microsoft Azure" metaKeywords="Azure テーブルの使用、Azure nosql、Azure 大容量構造化データ ストア、Azure テーブル、Azure テーブル ストレージ、Azure テーブル .NET、Azure テーブル ストレージ .NET、Azure テーブル C#、Azure テーブル ストレージ C#" description="テーブル ストレージを使用してテーブルを作成および削除する方法、さらにテーブル内のエンティティを挿入および照会する方法について説明します。" metaCanonical="" services="storage" documentationCenter=".NET" title="テーブル ストレージ サービスを使用する方法" authors="" solutions="" manager="paulettm" editor="cgronlun" />








# テーブル ストレージ サービスを使用する方法

<div class="dev-center-tutorial-selector">
<a href="/ja-jp/develop/net/how-to-guides/table-services-v17/" title="バージョン 1.7" class="current">バージョン 1.7</a>
<a href="/ja-jp/develop/net/how-to-guides/table-services/" title="バージョン 2.0">バージョン 2.0</a> 
</div>


このガイドでは、Azure テーブル ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは C\# コードで記述され、
.NET API を利用しています。紹介するシナリオは、**テーブルの作成と削除、
テーブルのエンティティの挿入とクエリ実行**などです。テーブルの
詳細については、「[次のステップ][次のステップ]」のセクションを参照してください。

## 目次

-   [テーブル サービスとは][テーブル サービスとは]
-   [概念][概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [ストレージ接続文字列の設定][ストレージ接続文字列の設定]
-   [方法: プログラムでテーブル ストレージにアクセスする][方法: プログラムでテーブル ストレージにアクセスする]
-   [方法: テーブルを作成する][方法: テーブルを作成する]
-   [方法: エンティティをテーブルに追加する][方法: エンティティをテーブルに追加する]
-   [方法: エンティティのバッチを挿入する][方法: エンティティのバッチを挿入する]
-   [方法: パーティション内のすべてのエンティティを取得する][方法: パーティション内のすべてのエンティティを取得する]
-   [方法: パーティション内の一定範囲のエンティティを取得する][方法: パーティション内の一定範囲のエンティティを取得する]
-   [方法: 単一のエンティティを取得する][方法: 単一のエンティティを取得する]
-   [方法: エンティティを更新する][方法: エンティティを更新する]
-   [方法: エンティティ プロパティのサブセットを照会する][方法: エンティティ プロパティのサブセットを照会する]
-   [方法: エンティティを挿入または置換する][方法: エンティティを挿入または置換する]
-   [方法: エンティティを削除する][方法: エンティティを削除する]
-   [方法: テーブルを削除する][方法: テーブルを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">アカウントの作成</span>Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定</h2>

Azure .NET ストレージ API では、ストレージ接続文字列を使用して、
ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を
構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` ファイルおよび `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure のクラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを再展開する
ことなく Azure の管理ポータルから構成設定を動的に変更
できます。

Azure サービス構成で接続文字列を
構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Azure 展開
    プロジェクトの **[ロール]** フォルダー内の Web ロールまたは
    worker ロールを右クリックし、**[プロパティ]** をクリックします。  
    ![Blob5][Blob5]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。  
    ![Blob6][Blob6]

    新しい **[Setting1]** エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、
    **[接続文字列]** をクリックします。  
    ![Blob7][Blob7]

4.  **[Setting1]** エントリの右端にある **[...]** をクリックします。
    **[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた 
    Microsoft Azure のストレージ) をターゲットとするか、クラウド内の実際の
    ストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Azure で作成したストレージ アカウントに BLOB データを
    格納する場合は、このチュートリアルの前の手順からコピーした
    **プライマリ アクセス キー**の値を入力します。   
    ![Blob8][Blob8]

6.  エントリの **[名前]** を **Setting1** から **StorageConnectionString**
    などの "わかりやすい" 名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。  
    ![Blob9][Blob9]
	
### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成][接続文字列の構成]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。


<h2> <a name="configure-access"> </a><span  class="short-header">プログラムでのアクセス</span>方法: プログラムでテーブル ストレージにアクセスする</h2>

プログラムを使用して Azure ストレージにアクセスするすべての C\# ファイルの
冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 型と
**CloudConfigurationManager** 型を
使用すると、Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));


<h2><a name="create-table"></a><span class="short-header">テーブルの作成</span>方法: テーブルを作成する</h2>

**CloudTableClient** オブジェクトを使用すると、テーブルとエンティティの参照オブジェクトを取得できます。次のコードは、**CloudTableClient** オブジェクトを作成し、これを使用して新しいテーブルを作成します。このガイドのすべてのコードでは、
Azure アプリケーションのサービス構成に格納されている
ストレージ接続文字列を使用します。そのほかにも 
**CloudStorageAccount** オブジェクトを作成する方法はあります。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

<h2><a name="add-entity"></a><span class="short-header">テーブルへのエンティティの追加</span>方法: テーブルにエンティティを追加する</h2>

エンティティは、**TableServiceEntity** から派生する
カスタム クラスを使用して C\# オブジェクトにマップされます。エンティティをテーブルに追加するには、最初に、
エンティティのプロパティを定義するクラスを作成します。次のコードは、
ユーザーの名を行キーとして、姓をパーティション キーとして
使用するエンティティ クラスを定義します。エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。同じパーティション キーを
持つエンティティは、異なるパーティション キーを持つエンティティよりも
迅速に照会できます。

    public class CustomerEntity : TableServiceEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

エンティティに関連するテーブル操作には **TableServiceContext** 
オブジェクトが必要です。このオブジェクトは、クライアント コードで作成してアクセスできるすべての
テーブル エンティティのクライアント側の状態を追跡します。クライアント側のオブジェクトが
各エンティティを表すようにしておくことで、書き込み操作の効率性が高まります。
これは、保存操作の実行時に、変更のあるオブジェクトだけがテーブル サービスで
更新されるためです。次のコードでは、
**GetDataServiceContext** メソッドを呼び出すことで、**TableServiceContext** 
オブジェクトを作成します。次に、このコードは、**CustomerEntity** クラスのインスタンスを
作成します。また、このコードは、**serviceContext.AddObject** を呼び出して、
新しいエンティティをテーブルに挿入します。エンティティ オブジェクトが 
**serviceContext** に追加されますが、サービスの操作は発生しません。最後に、このコードは、
**SaveChangesWithRetries** メソッドの呼び出し時に、
新しいエンティティをテーブル サービスに送信します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="insert-batch"></a><span class="short-header">エンティティのバッチの挿入</span>方法: エンティティのバッチを挿入する</h2>

1 回の書き込み操作でエンティティのバッチをテーブル サービスに
挿入できます。次のコードは、3 つのエンティティ オブジェクトを作成し、**AddObject**
メソッドを使用してそれぞれをサービス コンテキストに追加します。次に、このコードは
**SaveChangesOptions.Batch** パラメーターを指定して
**SaveChangesWithRetries** を呼び出します。**SaveChangesOptions.Batch** を省略すると、
テーブル サービスに対して 3 つの個別の呼び出しが発生します。バッチ操作に関しては、次の
事項にも留意してください。

1.  バッチ更新、バッチ削除、またはバッチ挿入を実行できます。
2.  1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
3.  1 つのバッチ操作に含まれるすべてのエンティティの
    パーティション キーが同じである必要があります。

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

<h2><a name="retrieve-all-entities"></a><span class="short-header">すべてのエンティティの取得</span>方法: パーティション内のすべてのエンティティを取得する</h2>

テーブルに対してパーティション内のエンティティを照会する場合は、LINQ クエリを使用できます。
**serviceContext.CreateQuery** を呼び出して、データ ソースのクエリを
作成します。次のコードは、'Smith' がパーティション キーであるエンティティに
対してフィルターを指定します。LINQ クエリの結果で **AsTableServiceQuery&lt;CustomerEntity&gt;** 
を呼び出し、**CloudTableQuery** オブジェクトの作成を終了します。その後、**foreach** ループに作成した **partitionQuery** オブジェクトを
使用して、結果を処理します。このコードは、クエリ結果の各エンティティの
フィールドをコンソールに出力します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">一定範囲のエンティティの取得</span>方法: パーティション内の一定範囲のエンティティを取得する</h2>

パーティション内の一部のエンティティのみを照会する場合、一般的な 
greater-than (&gt;) 演算子と less-than (&lt;) 演算子を使用する代わりに 
**CompareTo** メソッドを使用することで、範囲を指定できます。これは、前者の
場合にはクエリの構造が不適切になるためです。次のコードは、
2 つのフィルターを使用して、行キー (名) がアルファベットの "E" までの文字で始まる、
"Smith" というパーティション内のすべてのエントリを取得します。その後、
クエリ結果が出力されます。このガイドのバッチ挿入に関するセクションでテーブルに追加した
エンティティを使用すると、この場合は 2 つのエンティティ (Ben Smith と Denise Smith) だけが
返されます。Jeff Smith は返されません。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">単一のエンティティの取得</span>方法: 単一のエンティティを取得する</h2>

単一の特定のエンティティを取得するクエリを記述することができます。次のコードは、
2 つのフィルターを使用して、"Jeff Smith" というユーザーを指定します。
**AsTableServiceQuery** を呼び出す代わりに、このコードは 
**FirstOrDefault** を呼び出します。この方法で返されるのは、エンティティのコレクション
ではなく、単一のエンティティのみです。このコードは、
戻り値を **CustomerEntity** オブジェクトに直接割り当てます。パーティション キーおよび行キーが正確に一致する
エンティティがない場合は、null 値が返されます。クエリでパーティション キーと行キーの両方を
指定することが、テーブル サービスから単一のエンティティを取得するための
最速の方法です。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

<h2><a name="update-entity"></a><span class="short-header">エンティティを更新する</span>方法: エンティティを更新する</h2>

エンティティを更新するには、そのエンティティをテーブル サービスから取得し、
エンティティ オブジェクトを変更して、変更をテーブル サービスに戻して保存します。次のコードは、既存のユーザーの電話番号を変更します。挿入の場合
のように **AddObject** を呼び出すのではなく、このコードは 
**UpdateObject** を呼び出します。このアプリケーションがエンティティを取得した後で別のアプリケーションが
変更を加えていない限り、**SaveChangesWithRetries** メソッドは
テーブル サービスを呼び出し、このエンティティは更新されます。別のアプリケーションが変更を加えた場合は、
例外がスローされるので、このエンティティを取得して変更し、
もう一度保存する必要があります。この再試行パターンは、分散したストレージ システムでは
一般的です。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="query-entity-properties"></a><span class="short-header">プロパティのサブセットの照会</span>方法: エンティティ プロパティのサブセットを照会する</h2>

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリの
パフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。次のコードのクエリは、
テーブル内のエンティティの電子メール アドレスだけを
返します。プロジェクションの詳細については、この[ブログの記事][ブログの記事]を参照してください。プロジェクションは
ローカル ストレージ エミュレーターではサポートされていません。したがって、
このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

<h2><a name="insert-entity"></a><span class="short-header">エンティティを挿入または置換する</span>方法: エンティティを挿入または置換する</h2>

エントリをテーブルに追加するときは、多くの場合、そのエントリがテーブル内に
既に存在しているかどうかを把握していません。エンティティの挿入または置換操作では、エンティティが存在しない場合に
そのエンティティを挿入し、エンティティが存在する場合はその既存のエンティティを
置き換えるという操作を 1 つの要求で処理することができます。これまでの例に対して、
次のコードは "Walter Harp" のエンティティを挿入または置換します。新しい
エンティティを作成すると、このコードは **serviceContext.AttachTo** 
メソッドを呼び出します。次に、このコードは **UpdateObject** を呼び出し、
最後に **SaveChangesOptions.ReplaceOnUpdate** パラメーターを
指定して **SaveChangesWithRetries** を呼び出します。**SaveChangesOptions.ReplaceOnUpdate** パラメーターを
省略すると、挿入またはマージ操作が発生します。挿入または置換はローカル ストレージ エミュレーターでは
サポートされていません。したがって、このコードはテーブル サービスのアカウントを
使用している場合にのみ機能します。挿入または置換、および挿入またはマージの詳細については、
この[ブログの記事][ブログの記事]を参照してください。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

<h2><a name="delete-entity"></a><span class="short-header">エンティティの削除</span>方法: エンティティを削除する</h2>

エンティティは、取得後に簡単に削除できます。**AttachTo** 
メソッドを使用することで、エンティティをサーバーから取得せずに、エンティティ
の追跡を開始することもできます (上の "挿入または置換" の説明を参照してください)。
**serviceContext** でエンティティを追跡したら、削除するエンティティを
指定して **DeleteObject** を呼び出します。次に、**SaveChangesWithRetries** を呼び出します。次のコードは、
ユーザー エンティティを取得して削除します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="delete-table"></a><span class="short-header">テーブルの削除</span>方法: テーブルを削除する</h2>

最後に、次のコードは、ストレージ アカウントからテーブルを削除します。削除されたテーブルは、削除後の一定期間は再作成できなくなります。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

<h2><a name="next-steps"></a><span class="short-header">次のステップ</span>次のステップ</h2>

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

<ul>
<li>利用可能な API の詳細については、BLOB サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/wl_svchosting_mref_reference_home">.NET クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Azure のストレージを使用して実行できるさらに高度なタスクについては、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx">Azure のデータの格納とアクセス</a>」を参照してください。</li>
<li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>非構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/blob-storage/">BLOB ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/ja-jp/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [次のステップ]: #next-steps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: プログラムでテーブル ストレージにアクセスする]: #configure-access
  [方法: テーブルを作成する]: #create-table
  [方法: エンティティをテーブルに追加する]: #add-entity
  [方法: エンティティのバッチを挿入する]: #insert-batch
  [方法: パーティション内のすべてのエンティティを取得する]: #retrieve-all-entities
  [方法: パーティション内の一定範囲のエンティティを取得する]: #retrieve-range-entities
  [方法: 単一のエンティティを取得する]: #retrieve-single-entity
  [方法: エンティティを更新する]: #update-entity
  [方法: エンティティ プロパティのサブセットを照会する]: #query-entity-properties
  [方法: エンティティを挿入または置換する]: #insert-entity
  [方法: エンティティを削除する]: #delete-entity
  [方法: テーブルを削除する]: #delete-table
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-17/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-17/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-17/blob9.png
  
  [ブログの記事]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [接続文字列の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx

