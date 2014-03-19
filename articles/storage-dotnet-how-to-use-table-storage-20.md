<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="テーブル サービス (2.0)" pageTitle="テーブル ストレージの使用方法 - Windows Azure の機能ガイド" metaKeywords="Azure テーブルの使用   Azure nosql   Azure 大容量構造化データ ストア   Azure テーブル   Azure テーブル ストレージ   Azure テーブル .NET   Azure テーブル ストレージ .NET   Azure テーブル C#   Azure テーブル ストレージ C#" description="テーブル ストレージを使用してテーブルを作成および削除する方法、さらにテーブル内のエンティティを挿入および照会する方法について学習します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="テーブル ストレージ サービスを使用する方法"/>



# テーブル ストレージ サービスを使用する方法

<div class="dev-center-tutorial-selector">
<a href="/en-us/develop/net/how-to-guides/table-services-v17/" title="version 1.7">バージョン 1.7</a>
<a href="/en-us/develop/net/how-to-guides/table-services/" title="version 2.0" class="current">バージョン 2.0</a>
</div>


このガイドでは、Windows Azure テーブル ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは C\# コードで記述され、Windows Azure .NET 用ストレージ クライアント ライブラリ (バージョン 2.0) を利用しています。紹介するシナリオは、**テーブルの作成と削除**、
**テーブル エンティティの操作**などです。テーブルの
詳細については、「[次の手順][]」のセクションを参照してください。

## 目次

-   [テーブル サービスとは][]
-   [概念][]
-   [Windows Azure のストレージ アカウントの作成][]
-   [ストレージ接続文字列の設定][]
-   [方法: プログラムでテーブル ストレージにアクセスする][]
-   [方法: テーブルを作成する][]
-   [方法: エンティティをテーブルに追加する][]
-   [方法: エンティティのバッチを挿入する][]
-   [方法: パーティション内のすべてのエンティティを取得する][]
-   [方法: パーティション内の一定範囲のエンティティを取得する][]
-   [方法: 単一のエンティティを取得する][]
-   [方法: エンティティを置換する][]
-   [方法: エンティティを挿入または置換する][]
-   [方法: エンティティ プロパティのサブセットを照会する][]
-   [方法: エンティティを削除する][]
-   [方法: テーブルを削除する][]
-   [次の手順][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">アカウントの作成</span>Windows Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定</h2>

Windows Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を
使用して、ストレージ サービスにアクセスするためのエンドポイント
および資格情報を構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Windows Azure のクラウド サービスを使用するときには、Windows Azure サービス構成システム ('*.csdef' ファイルおよび '*.cscfg' ファイル) を使用して接続文字列を格納することをお勧めします。
- Windows Azure の Web サイトまたは Windows Azure の仮想マシンを使用する場合や、Windows Azure の外部で実行される .NET アプリケーションを作成する場合は、.NET 構成システム ('web.config' ファイル、'app.config' ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する 'CloudConfigurationManager.GetSetting' メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Windows Azure のクラウド サービスのプロジェクトに特有のものであり、これを使用すると、アプリケーションを再展開することなく Windows Azure の管理ポータルから構成設定を動的に変更できます。

Windows Azure サービス構成で接続文字列を構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Windows Azure 展開プロジェクトの **[ロール]** フォルダー内の Web ロールまたは Worker ロールを右クリックし、**[プロパティ]** をクリックします。
    ![Blob5][Blob5]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
    ![Blob6][Blob6]

    新しい **[Setting1]** エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、**[接続文字列]** をクリックします。
    ![Blob7][Blob7]

4.  **[Setting1]** エントリの右端にある **[...]** をクリックします。
    **[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた 
Windows Azure のストレージ) をターゲットとするか、クラウド内の実際の
ストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Windows Azure で作成したストレージ アカウントに 
BLOB データを格納する場合は、このチュートリアルの前の手順から
コピーした**プライマリ アクセス キー**の値を入力します。
    ![Blob8][Blob8]

6.  エントリの **[名前]** を **Setting1** から **StorageConnectionString** 
などの "わかりやすい" 名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。
    ![Blob9][Blob9]
	
### .NET 構成を使用した接続文字列の構成

Windows Azure のクラウド サービスではないアプリケーション (前のセクションを参照) を記述する場合は、.NET 構成システム ('web.config'、'app.config' など) を使用することをお勧めします。これには、Windows Azure の Web サイトや Windows Azure の仮想マシンのほか、Windows Azure の外部で実行されるアプリケーションも含まれます。次のように、'<appSettings>' 要素を使用して接続文字列を格納します。

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成方法][]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。

<h2> <a name="configure-access"> </a><span  class="short-header">プログラムでのアクセス</span>方法: プログラムでテーブル ストレージにアクセスする</h2>

<h3>アセンブリの取得</h3>
NuGet を使用して 'Microsoft.WindowsAzure.Storage.dll' アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Windows Azure のストレージのパッケージと依存関係をインストールします。

'Microsoft.WindowsAzure.Storage.dll' は、<a href="http://www.windowsazure.com/en-us/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Windows Azure SDK for .NET 2.0 にも含まれています。アセンブリは '%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\' ディレクトリにインストールされます。

<h3>名前空間宣言</h3>
プログラムを使用して Windows Azure のストレージにアクセスするすべての C\# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

'Microsoft.WindowsAzure.Storage.dll' アセンブリを参照してください。

<h3>接続文字列の取得</h3>
**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Windows Azure プロジェクト 
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager 名前空間への参照が
ある場合は、**CloudConfigurationManager** 型を使用して 
Windows Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が 'web.config' または 'app.config' に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib 依存</h3>
.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData]、[Edm]、および [Spatial] です。

<h2><a name="create-table"></a><span class="short-header">テーブルを作成する</span>方法: テーブルを作成する</h2>

**CloudTableClient** オブジェクトを使用すると、テーブルとエンティティの参照オブジェクトを取得できます。次のコードは、**CloudTableClient** オブジェクトを作成し、これを使用して新しいテーブルを作成します。このガイドのすべてのコードでは、
作成するアプリケーションが Windows Azure のクラウド サービス プロジェクト
であること、Windows Azure アプリケーションのサービス構成に格納されている
ストレージ接続文字列を使用することを前提としています。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // テーブルが存在しない場合は作成します。
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">テーブルにエンティティを追加する</span>方法: テーブルにエンティティを追加する</h2>

エンティティは、**TableEntity** から派生するカスタム クラスを
使用して C\# オブジェクトにマップされます。エンティティをテーブルに追加するには、
エンティティのプロパティを定義するクラスを作成します。次のコードは、
ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ
使用するエンティティ クラスを定義します。エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。同じパーティション キーを
持つエンティティは、異なるパーティション キーを持つエンティティよりも
迅速に照会できます。一方、多様なパーティション キーを使用すると、
並列操作の拡張性が向上します。テーブル サービスに格納するプロパティは、'get' と 'set' の両方を公開する、サポートされている型のパブリック プロパティである必要があります。
また、エンティティ型で、パラメーターのないコンストラクターを公開する必要があります。**

    public class CustomerEntity : TableEntity
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

エンティティに関連するテーブル操作は、「方法: テーブルを作成する」で作成した
 **CloudTable** オブジェクトを使用して実行されます。実行する操作は、**TableOperation** オブジェクトによって表されます。次のコード例では、まず **CloudTable** オブジェクトを作成し、次に **CustomerEntity** オブジェクトを作成します。その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する **TableOperation** を作成します。最後に、**CloudTable.Execute** を呼び出して操作を実行します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// "people" テーブルを表す CloudTable オブジェクトを作成します。
	CloudTable table = tableClient.GetTableReference("people");

    // 新しいユーザー エンティティを作成します。
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // ユーザー エンティティを挿入する TableOperation を作成します。
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // 挿入操作を実行します。
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">エンティティのバッチを挿入する</span>方法: エンティティのバッチを挿入する</h2>

1 回の書き込み操作でエンティティのバッチをテーブルに
挿入できます。バッチ操作に関しては、次の
事項にも留意してください。

1.  更新、削除、および挿入を同じ 1 回のバッチ操作で実行できます。
2.  1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
3.  1 つのバッチ操作に含まれるすべてのエンティティの
パーティション キーが同じである必要があります。
4.  クエリをバッチ操作として実行することもできますが、バッチ内の唯一の操作である必要があります。

<!-- -->
次のコード例は、2 つのエンティティ オブジェクトを作成して、
**Insert** メソッドを使用する **TableBatchOperation** に追加します。その後、**CloudTable.Execute** を呼び出して操作を実行します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// "people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // バッチ操作を作成します。
    TableBatchOperation batchOperation = new TableBatchOperation();

    // ユーザー エンティティを作成してテーブルに追加します。
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// 別のユーザー エンティティを作成してテーブルに追加します。
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// 両方のユーザー エンティティをバッチ挿入操作に追加します。
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// バッチ操作を実行します。
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">すべてのエンティティを取得する</span>方法: パーティション内のすべてのエンティティを取得する</h2>

テーブルに対してパーティション内のすべてのエンティティを照会する場合は、**TableQuery** オブジェクトを使用します。
次のコード例は、'Smith' がパーティション キーであるエンティティに対して
フィルターを指定します。この例は、クエリ結果の各エンティティの
フィールドをコンソールに出力します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //  "people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // PartitionKey="Smith" であるすべてのユーザー エンティティの照会操作を作成します。
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // ユーザーごとのフィールドを出力します。
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">一定範囲てのエンティティを取得する</span>方法: パーティション内の一定範囲のエンティティを取得する</h2>

パーティション内の一部のエンティティのみを照会する場合は、
パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。次のコード例は、
2 つのフィルターを使用して、行キー (名) がアルファベットの 'E' より
前の文字で始まる、'Smith' というパーティション内のすべてのエンティティを
取得し、クエリ結果を出力します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //"people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

	// テーブル クエリを作成します。
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // 結果をループ処理し、エンティティに関する情報を表示します
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">単一のエンティティを取得する</span>方法: 単一のエンティティを取得する</h2>

単一の特定のエンティティを取得するクエリを記述することができます。次のコードは、
**TableOperation** を使用して、'Ben Smith' というユーザーを指定します。
このメソッドで返されるのは、エンティティのコレクションではなく、
単一のエンティティのみです。したがって、**TableResult.Result** の戻り値は **CustomerEntity** です。
クエリでパーティション キーと行キーの両方を指定することが、テーブル サービスから単一のエンティティを取得するための最速の方法です。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //  "people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // ユーザー エンティティを取得する取得操作を作成します。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 取得操作を実行します。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 結果の電話番号を出力します。
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a><span class="short-header">エンティティを置換する</span>方法: エンティティを置換する</h2>

エンティティを更新するには、そのエンティティをテーブル サービスから取得し、
エンティティ オブジェクトを変更して、変更をテーブル サービスに戻して保存します。次のコードは、既存のユーザーの電話番号を変更します。このコードでは、**Insert** を呼び出す代わりに **Replace** を使用しています。これにより、サーバーでエンティティが完全に置換されます。
ただし、エンティティを取得した後にサーバーでエンティティが変更された
場合は、操作が失敗します。このエラーは、取得と更新の間にアプリケーションの
別のコンポーネントによって行われた変更が意図せず上書きされるのを
防ぐためのものです。このエラーを正しく処理するには、
もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、
再び **Replace** 操作を実行します。次のセクションでは、
この動作をオーバーライドする方法を説明します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //  "people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // ユーザー エンティティを取得する取得操作を作成します。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 操作を実行します。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 結果を CustomerEntity オブジェクトに割り当てます。
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // 電話番号を変更します。
	   updateEntity.PhoneNumber = "425-555-0105";

	   // InsertOrReplace TableOperation を作成します
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // 操作を実行します。
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a><span class="short-header">エンティティを挿入または置換する</span>方法: エンティティを挿入または置換する</h2>

**Replace** 操作は、サーバーからエンティティを取得した後に
エンティティが変更されていると失敗します。さらに、**Replace** が成功するためには、
先にエンティティをサーバーから取得する必要があります。
しかし、サーバーにエンティティが存在するかどうかわからないが、現在格納されている値は不適切であるため、すべての値を上書きする必要がある場合もあります。そのような場合は、**InsertOrReplace** 操作を使用します。この操作は、最終更新日時に関係なく、エンティティが
存在しない場合は挿入し、存在する場合は置換します。次のコード例では、
先ほどと同じように Ben Smith のユーザー エンティティを取得していますが、
今度は **InsertOrReplace** を使用してサーバーに保存しています。そのため、
取得操作と更新操作の間に行われたエンティティの更新は
上書きされます。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //  "people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // ユーザー エンティティを取得する取得操作を作成します。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 操作を実行します。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 結果を CustomerEntity オブジェクトに割り当てます。
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // 電話番号を変更します。
	   updateEntity.PhoneNumber = "425-555-1234";

	   // InsertOrReplace TableOperation を作成します
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // 操作を実行します。
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a><span class="short-header">プロパティのサブセットを照会する</span>方法: エンティティ プロパティのサブセットを照会する</h2>

テーブル クエリでは、エンティティのすべてのプロパティではなくごくわずかのプロパティだけをエンティティから取得できます。プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。次のコードのクエリは、
テーブル内のエンティティの電子メール アドレスだけを
返します。これは、**DynamicTableEntity** のクエリと **EntityResolver** を
使用して行われます。プロジェクションの詳細については、この[ブログの記事][]を参照してください。プロジェクションはローカル ストレージ エミュレーターではサポートされていません。したがって、このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //"people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // クエリを定義し、電子メールのプロパティだけを選択します
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // 取得後にエンティティを操作するエンティティ リゾルバーを定義します。
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">エンティティを削除する</span>方法: エンティティを削除する</h2>

エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。次のコードは、
ユーザー エンティティを取得して削除します。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //"people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // ユーザー エンティティが必要な取得操作を作成します。
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // 操作を実行します。
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // 結果を CustomerEntity に割り当てます。
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // TableOperation の削除を作成します。
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // 操作を実行します。
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a><span class="short-header">テーブルを削除する</span>方法: テーブルを削除する</h2>

最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。削除されたテーブルは、削除後の一定期間は再作成できなくなります。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // テーブル クライアントを作成します。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //"people" テーブルを表す CloudTable オブジェクトを作成します。
    CloudTable table = tableClient.GetTableReference("people");

    // テーブルが存在する場合は削除します。
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">次の手順</span>次の手順</h2>

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

<ul>
<li>利用可能な API の詳細については、BLOB サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://msdn.microsoft.com/en-us/library/windowsazure/wa_storage_api_ref_reference_home.aspx">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/en-us/library/windowsazure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Windows Azure のストレージを使用して実行できるさらに高度なタスクについては、「<a href="http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx">Windows Azure のデータの格納とアクセス</a>」を参照してください。</li>
<li>Windows Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>非構造化データの格納には、<a href="/en-us/develop/net/how-to-guides/blob-storage/">BLOB ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/en-us/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [次の手順]: #next-steps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Windows Azure のストレージ アカウントの作成]: #create-account
  [Visual Studio で Windows Azure プロジェクトを作成する]: #create-project
  [アプリケーションのストレージへのアクセスの構成]: #configure-access
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: プログラムでテーブル ストレージにアクセスする]: #configure-access
  [方法: テーブルを作成する]: #create-table
  [方法: エンティティをテーブルに追加する]: #add-entity
  [方法: エンティティのバッチを挿入する]: #insert-batch
  [方法: パーティション内のすべてのエンティティを取得する]: #retrieve-all-entities
  [方法: パーティション内の一定範囲のエンティティを取得する]: #retrieve-range-entities
  [方法: 単一のエンティティを取得する]: #retrieve-single-entity
  [方法: エンティティを置換する]: #replace-entity
  [方法: エンティティを挿入または置換する]: #insert-or-replace-entity
  [方法: エンティティ プロパティのサブセットを照会する]: #query-entity-properties
  [方法: エンティティを削除する]: #delete-entity
  [方法: テーブルを削除する]: #delete-table
  [Windows Azure SDK for .NET をダウンロードしてインストールする]: /en-us/develop/net/
  [Visual Studio で Windows Azure プロジェクトを作成する]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-20/blob9.png
  
  [ブログの投稿]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Windows Azure のストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2


