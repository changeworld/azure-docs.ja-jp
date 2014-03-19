<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="BLOB サービス (2.0)" pageTitle="BLOB ストレージの使用方法 - Windows Azure の機能ガイド" metaKeywords="Azure BLOB の概要   Azure 非構造化データ   Azure 非構造化ストレージ   Azure BLOB   Azure BLOB ストレージ   Azure BLOB .NET   Azure BLOB C#   Azure BLOB C#" description="Windows Azure BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法を学習します。サンプルは C# で記述されています。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title=".NET での Windows Azure BLOB ストレージ サービスの使用方法" />


#.NET での Windows Azure BLOB ストレージ サービスの使用方法

<div class="dev-center-tutorial-selector">
<a href="/en-us/develop/net/how-to-guides/blob-storage-v17/" title="version 1.7">バージョン 1.7</a>
<a href="/en-us/develop/net/how-to-guides/blob-storage/" title="version 2.0" class="current">バージョン 2.0</a>
</div>


このガイドでは、Windows Azure BLOB ストレージ サービスを使用して一般的なシナリオを実行する方法のデモンストレーションを行います。サンプルは C\# で記述され、Windows Azure .NET 用ストレージ クライアント ライブラリ (バージョン 2.0) を利用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、「[次の手順][]」のセクションを参照してください。

##目次

-   [BLOB ストレージとは][]
-   [概念][]
-   [Windows Azure のストレージ アカウントの作成][]
-   [ストレージ接続文字列の設定][]
-   [方法: プログラムで BLOB ストレージにアクセスする][]
-   [方法: コンテナーを作成する][]
-   [方法: コンテナーに BLOB をアップロードする][]
-   [方法: コンテナー内の BLOB を一覧表示する][]
-   [方法: BLOB をダウンロードする][]
-   [方法: BLOB を削除する][]
-   [次の手順][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">アカウントの作成</span>Windows Azure のストレージ アカウントの作成
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定

Windows Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Windows Azure のクラウド サービスを使用するときには、Windows Azure サービス構成システム ('*.csdef' ファイルおよび '*.cscfg' ファイル) を使用して接続文字列を格納することをお勧めします。
- Windows Azure の Web サイトまたは Windows Azure の仮想マシンを使用する場合や、Windows Azure の外部で実行される .NET アプリケーションを作成する場合は、.NET 構成システム ('web.config' ファイル、'app.config' ファイルなど) を使用して接続文字列を格納することをお勧めします。

接続文字列を取得する方法は後ほど説明します。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Windows Azure のクラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを再展開
することなく Windows Azure の管理ポータルから構成設定を動的に変更
できます。

Windows Azure サービス構成で接続文字列を構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Windows Azure 
展開プロジェクトの **[ロール]** フォルダー内の Web ロールまたは 
Worker ロールを右クリックし、**[プロパティ]** をクリックします。
    ![Visual Studio で、クラウド サービス ロールのプロパティを選択します。][Blob5]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
    ![Visual Studio でのクラウド サービス設定の追加][Blob6]

    新しい **[Setting1]** エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、
**[接続文字列]** をクリックします。
    ![Blob7][Blob7]

4.   **[Setting1]** エントリの右端にある **[...]** をクリックします。
    **[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた 
Windows Azure のストレージ) をターゲットとするか、クラウド内の
ストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Windows Azure で作成したストレージ アカウントに BLOB 
データを格納する場合は、このチュートリアルの前の手順からコピーした
 **プライマリ アクセス キー**の値を入力します。
    ![Blob8][Blob8]

6.  エントリの **[名前]** を **Setting1** から **StorageConnectionString** 
などのわかりやすい名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。
    ![Blob9][Blob9]
	
### .NET 構成を使用した接続文字列の構成

Windows Azure のクラウド サービスではないアプリケーション (前のセクションを参照) を記述する場合は、.NET 構成システム ('web.config'、'app.config' など) を使用することをお勧めします。これには、Windows Azure の Web サイトや Windows Azure の仮想マシンのほか、Windows Azure の外部で実行されるアプリケーションも含まれます。次のように、'<appSettings>' 要素を使用して接続文字列を格納します。

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成方法][]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。

## <a name="configure-access"> </a><span  class="short-header">プログラムでのアクセス</span>方法: プログラムで BLOB ストレージにアクセスする

###アセンブリの取得
NuGet を使用して 'Microsoft.WindowsAzure.Storage.dll' アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Windows Azure のストレージのパッケージと依存関係をインストールします。

'Microsoft.WindowsAzure.Storage.dll' は、<a href="http://www.windowsazure.com/en-us/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Windows Azure SDK for .NET 2.0 にも含まれています。アセンブリは '%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\' ディレクトリにインストールされます。

###名前空間宣言
プログラムを使用して Windows Azure のストレージにアクセスするすべての C\# ファイルの冒頭部分に、次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

'Microsoft.WindowsAzure.Storage.dll' アセンブリを参照してください。

###接続文字列の取得
**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Windows Azure プロジェクト 
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager への
参照がある場合は、**CloudConfigurationManager** 型を
使用して Windows Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が 'web.config' または 'app.config' に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 型では、Blob ストレージ サービス内に格納されている
コンテナーと BLOB を表すオブジェクトを取得できます。次の
コードは、前に取得したストレージ アカウント オブジェクトを使用して、
**CloudBlobClient** オブジェクトを作成します。

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 依存
.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData]、[Edm]、および [Spatial] です。

## <a name="create-container"> </a><span  class="short-header">コンテナーを作成する</span>方法: コンテナーを作成する

ストレージの BLOB はすべてコンテナー内に格納されます。**CloudBlobClient** オブジェクトを
使用すると、使用するコンテナーへの参照を
取得できます。コンテナーが存在しない場合は、次のようにして作成できます。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // コンテナーへの参照を取得します。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // コンテナーがまだ存在しない場合、コンテナーを作成します。
    container.CreateIfNotExists();

既定では、新しいコンテナーはプライベートなので、このコンテナーから 
BLOB をダウンロードするにはストレージ アクセス キーを
指定する必要があります。コンテナー内のファイルをだれでも利用できるようにする場合は、
次のコードを使ってコンテナーをパブリックに設定できます。

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが
表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

<h2> <a name="upload-blob"> </a><span  class="short-header">コンテナーにアップロードする</span>方法: コンテナーに BLOB をアップロードする</h2>

Windows Azure BLOB ストレージでは、ブロック BLOB とページ BLOB がサポートされています。ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック BLOB にアップロードするには、コンテナーの参照を取得し、
それを使用してブロック BLOB の参照を取得します。BLOB の参照を取得したら、**UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "myblob" という名前の BLOB への参照を取得します。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // "myblob" BLOB を作成するか、ローカル ファイルの内容で上書きします。
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">コンテナー内の BLOB を一覧表示する</span>方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、コンテナーの **ListBlobs** メソッドを使って、その中の BLOB やディレクトリを取得します。返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。次のコードは、
'photos' コンテナー内の各アイテムの URI を取得して出力する方法を
示しています。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// 前に作成したコンテナーへの参照を取得します。
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// コンテナー内の項目をループ処理して、長さと URI を出力します。
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

この例からわかるように、BLOB サービスにはコンテナー内のディレクトリの概念も
あります。これは、BLOB をよりフォルダーに近い構造で整理できるように
するためです。たとえば、'photos' というコンテナーに次のブロック BLOB があったとします。

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

'photos' コンテナーの **ListBlobs** を呼び出すと (上の例を参照)、
返されるコレクションには
最上位レベルにあるディレクトリおよび BLOB を表す **CloudBlobDirectory** 
および **CloudBlockBlob** オブジェクトが含まれています。結果の出力は次のようになります。

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


必要に応じて、**ListBlobs** メソッドの **UseFlatBlobListing** パラメーターを 
**true** に
設定することもできます。そうすると、ディレクトリに関係なく、すべての BLOB が **CloudBlockBlob** として返されるようになります。この場合、**ListBlobs** の呼び出しは次のようになります。

    // コンテナー内の項目をループ処理して、長さと URI を出力します。
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

結果は次のようになります。

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

詳細については、[CloudBlobContainer.ListBlobs][] を参照してください。

## <a name="download-blobs"> </a><span  class="short-header">BLOB をダウンロードする</span>方法: BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。次の例は、
**DownloadToStream** メソッドを使用して、ローカル ファイルに
保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "photo1.jpg" という名前の BLOB への参照を取得します。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // BLOB の内容をファイルに保存します
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

**DownloadToStream** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

	// 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// "myblob.txt-" という名前の BLOB への参照を取得します
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">BLOB を削除する</span>方法: BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次にその **Delete** メソッドを呼び出します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します。
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します。
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "myblob.txt-" という名前の BLOB への参照を取得します。
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // BLOB を削除します。
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">次の手順</span>次の手順

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。
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
    <li>構造化データの格納には、<a href="/en-us/develop/net/how-to-guides/table-services/">テーブル ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/en-us/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [次の手順]: #next-steps
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Windows Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: プログラムで BLOB ストレージにアクセスする]: #configure-access
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Windows Azure のストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
  [.NET クライアント ライブラリ リファレンス]: http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home
  [REST API リファレンス]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2


