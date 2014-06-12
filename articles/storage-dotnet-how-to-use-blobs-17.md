<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="BLOB サービス (2.0)" pageTitle="BLOB ストレージの使用方法 - Azure の機能ガイド" metaKeywords="Azure BLOB の概要、Azure 非構造化データ、Azure 非構造化ストレージ、Azure BLOB、Azure BLOB ストレージ、Azure BLOB .NET、Azure BLOB C#、Azure BLOB C#" description="Azure BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法を学習します。サンプルは C# で記述されています。" metaCanonical="" services="" documentationCenter="" title=".NET での Azure BLOB ストレージ サービスの使用方法" authors=""  solutions="" writer="" manager="" editor=""  />

#.NET での Azure BLOB ストレージ サービスの使用方法

<div class="dev-center-tutorial-selector">
<a href="/ja-jp/develop/net/how-to-guides/blob-storage-v17/" title="バージョン 1.7" class="current">バージョン 1.7</a>
<a href="/ja-jp/develop/net/how-to-guides/blob-storage/" title="バージョン 2.0">バージョン 2.0</a>
</div>


このガイドでは、Azure BLOB ストレージ サービスを使用して一般的なシナリオを実行する方法のデモンストレーションを行います。サンプルは C\# で記述され、
.NET API を利用しています。紹介するシナリオは、
BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、「[次の手順][]」のセクションを参照してください。

<h2>目次</h2>

-   [BLOB ストレージとは][]
-   [概念][]
-   [Azure のストレージ アカウントの作成][]
-   [ストレージ接続文字列の設定][]
-   [方法: プログラムで BLOB ストレージにアクセスする][]
-   [方法: コンテナーを作成する][]
-   [方法: コンテナーに BLOB をアップロードする][]
-   [方法: コンテナー内の BLOB を一覧表示する][]
-   [方法: BLOB をダウンロードする][]
-   [方法: BLOB を削除する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">アカウントの作成</span>Azure のストレージ アカウントの作成</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定</h2>

Azure .NET ストレージ API では、ストレージ接続文字列を使用して、
ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を
構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` ファイルおよび `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure のクラウド サービスのプロジェクトに特有のものであり、これを使用すると、アプリケーションを再展開することなく Azure の管理ポータルから構成設定を動的に変更できます。

Azure サービス構成で接続文字列を構成するには:

1.  Visual Studio のソリューション エクスプローラーで、Azure 
    デプロイ プロジェクトの **[ロール]** フォルダー内の Web ロールまたは
    worker ロールを右クリックし、**[プロパティ]** をクリックします。
    ![Visual Studio で、クラウド サービス ロールのプロパティを選択します。][Blob5]

2.  **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
    ![Visual Studio でのクラウド サービス設定の追加][Blob6]

    新しい [**Setting1**] エントリが設定グリッドに表示されます。

3.  新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、
    **[接続文字列]** をクリックします。
    ![Blob7][Blob7]

4.  [**Setting1**] エントリの右端にある [**...**] をクリックします。
    **[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5.  ストレージ エミュレーター (ローカル コンピューターでシミュレートされた
    Azure のストレージ) をターゲットとするか、クラウド内の実際の
    ストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Azure で作成したストレージ アカウントに 
BLOB データを格納する場合は、このチュートリアルの前の手順から
コピーした**プライマリ アクセス キー**の値を入力します。
    ![Blob8][Blob8]

6.  エントリの **[名前]** を **Setting1** から **StorageConnectionString**
    などのわかりやすい名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。
    ![Blob9][Blob9]
	
### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成方法][]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。

<h2> <a name="configure-access"> </a><span  class="short-header">プログラムでのアクセス</span>方法: プログラムで BLOB ストレージにアクセスする</h2>

プログラムを使用して Azure のストレージにアクセスするすべての C\# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 型と
**CloudConfigurationManager** 型を使用すると、
Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

**CloudBlobClient** 型では、Blob ストレージ サービス内に格納されている
コンテナーと BLOB を表すオブジェクトを取得できます。次の
コードは、前に取得したストレージ アカウント オブジェクトを使用して、
**CloudBlobClient** オブジェクトを作成します。

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">コンテナーを作成する</span>方法: コンテナーを作成する</h2>

ストレージの BLOB はすべてコンテナー内に格納されます。**CloudBlobClient** オブジェクトを
使用すると、使用するコンテナーへの参照を
取得できます。コンテナーが存在しない場合は、次のようにして作成できます。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // コンテナーへの参照を取得します
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // コンテナーがまだ存在しない場合、コンテナーを作成します
    container.CreateIfNotExist();

既定では、新しいコンテナーはプライベートなので、このコンテナーから
BLOB をダウンロードするにはストレージ アクセス キーを指定する必要が
あります (前と同じ方法で)。コンテナー内のファイルをだれでも利用できるようにする場合は、
次のコードを使ってコンテナーをパブリックに設定できます。

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが
表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

<h2> <a name="upload-blob"> </a><span  class="short-header">コンテナーにアップロードする</span>方法: コンテナーに BLOB をアップロードする</h2>

ファイルを BLOB にアップロードするには、コンテナーの参照を取得し、
それを使用して BLOB の参照を取得します。BLOB の参照を取得したら、その **UploadFromStream**
メソッドを呼び出すことによって、データの任意のストリームを BLOB に
アップロードできます。この操作により、BLOB が存在しない場合は作成され、
存在する場合は上書きされます。次のコード サンプルはこのことを示しており、既に
コンテナーが作成されていることを前提としています。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "myblob" という名前の BLOB への参照を取得します
    CloudBlob blob = container.GetBlobReference("myblob");

    // "myblob" BLOB を作成するか、ローカル ファイルの内容で上書きします
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">コンテナー内の BLOB を一覧表示する</span>方法: コンテナー内の BLOB を一覧表示する</h2>

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、
コンテナーの **ListBlobs** メソッドを使って、その中の BLOB を
取得します。次のコードは、コンテナー内の各 BLOB の **Uri** を取得して
出力する方法を示しています。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // コンテナー内の BLOB をループ処理して、各 BLOB の URI を出力します
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

BLOB サービスには、コンテナー内のディレクトリの概念も
あります。これは、BLOB をよりフォルダーに近い構造で整理できるように
するためです。たとえば、"photos" という名前のコンテナーを作成して、その中に
'rootphoto1'、'2010/photo1'、'2010/photo2'、および '2011/photo1' という
名前の BLOB をアップロードすることができます。このようにすると、実際には 'photos' コンテナー内
に '2010' および '2011' というディレクトリが作成されます。'photos' コンテナーの
**ListBlobs** を呼び出すと、返されるコレクションには最上位レベルに
あるディレクトリおよび BLOB を表す **CloudBlobDirectory** および
**CloudBlob** オブジェクトが含まれています。この
例の場合は、ディレクトリ '2010' および '2011' と、写真 'rootphoto1' が
返されます。必要に応じて、**UseFlatBlobListing** を **true** に
設定した新しい **BlobRequestOptions** クラスを渡すことが
できます。そうすると、ディレクトリに関係なく、すべての BLOB が返されるようになります。詳細については、[CloudBlobContainer.ListBlobs][] を参照してください。

<h2> <a name="download-blobs"> </a><span  class="short-header">BLOB をダウンロードする</span>方法: BLOB をダウンロードする</h2>

BLOB をダウンロードするには、まず、BLOB の参照を取得します。次の例は、
**DownloadToStream** メソッドを使用して、ローカル ファイルに
保存できるストリーム オブジェクトに BLOB の内容を転送します。
BLOB の **DownloadToFile**、**DownloadByteArray**、または **DownloadText** メソッドを呼び出すこともできます。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "myblob" という名前の BLOB への参照を取得します
    CloudBlob blob = container.GetBlobReference("myblob");

    // BLOB の内容をディスクに保存します
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">BLOB を削除する</span>方法: BLOB を削除する</h2>

最後に、BLOB を削除するには、BLOB の参照を取得し、その **Delete** メソッドを呼び出します。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // BLOB クライアントを作成します
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 前に作成したコンテナーへの参照を取得します
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // "myblob" という名前の BLOB への参照を取得します
    CloudBlob blob = container.GetBlobReference("myblob");

    // BLOB を削除します
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">次のステップ</span>次のステップ</h2>

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。
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
    <li>構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/table-services/">テーブル ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/ja-jp/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [次の手順]: #next-steps
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: プログラムで BLOB ストレージにアクセスする]: #configure-access
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx
  [.NET クライアント ライブラリ リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/wl_svchosting_mref_reference_home
  [REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355

