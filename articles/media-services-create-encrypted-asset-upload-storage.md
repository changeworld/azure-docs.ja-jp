<properties linkid="develop-media-services-how-to-guides-create-assets" urlDisplayName="暗号化されたアセットを作成してストレージにアップロードする" pageTitle="暗号化されたアセットを作成してストレージにアップロードする - Azure" metaKeywords="" description="暗号化されたアセットを作成し、アップロードして、メディア サービスにメディア コンテンツを取得する方法について説明します。" metaCanonical="" services="media-services" documentationCenter="" title="方法: 暗号化されたアセットを作成してストレージにアップロードする" authors="migree" solutions="" manager="" editor="" />





<h1><a name="create-asset"> </a><span class="short header">方法: 暗号化されたアセットを作成してストレージにアップロードする</span></h1>

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[メディア サービス開発のためのコンピューターのセットアップに関するページ](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409)を参照してください。

メディア コンテンツをメディア サービスに取り込むには、まずアセットを作成してファイルを追加した後、そのアセットをアップロードします。このプロセスを "コンテンツの取り込み" といいます。

アセットを作成する際には、暗号化に関する 3 つのオプションを指定できます。

- **AssetCreationOptions.None**: 暗号化されません。暗号化されていないアセットを作成するには、このオプションを設定する必要があります。
- **AssetCreationOptions.CommonEncryptionProtected**: Common Encryption Protected (CENC) ファイル用。既に PlayReady で暗号化されている一連のファイルは、その例です
- **AssetCreationOptions.StorageEncrypted**: ストレージ暗号化。Azure ストレージへのアップロードの前にクリア入力ファイルを暗号化します。

**注**: メディア サービスでは、Digital Rights Manager (DRM) のようにネットワーク経由ではなく、オンディスクのストレージ暗号化を提供します。

このサンプル コードでは、次のことが行われます。

- 空のアセットを作成します。
- アセットに関連付ける AssetFile インスタンスを作成します。
- アセットへのアクセス許可とアクセス期間を定義する AccessPolicy インスタンスを作成します。
- アセットへのアクセスを提供する Locator インスタンスを作成します。
- 単一のメディア ファイルをメディア サービスにアップロードします。

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);
    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    return asset;
}
</code></pre>

次のコードは、アセットを作成して複数のファイルをアップロードする方法を示します。

<pre><code>
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
{
    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                        AccessPermissions.Write | AccessPermissions.List);
    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    var blobTransferClient = new BlobTransferClient();
	blobTransferClient.NumberOfConcurrentTransfers = 20;
    blobTransferClient.ParallelTransferThreadCount = 20;

    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

    var filePaths = Directory.EnumerateFiles(folderPath);

    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

    if (!filePaths.Any())
    {
        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
    }

    var uploadTasks = new List&lt;Task&gt;();
    foreach (var filePath in filePaths)
    {
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
        // It is recommended to validate AccestFiles before upload. 
        Console.WriteLine("Start uploading of {0}", assetFile.Name);
        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
    }

    Task.WaitAll(uploadTasks.ToArray());
    Console.WriteLine("Done uploading the files");

    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
{
    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
    {
        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
    }
}

</code></pre>

<h2>次のステップ</h2>
これで、アセットをメディア サービスにアップロードできました。次は、[メディア プロセッサの取得][]に関するトピックに進みます。

[メディア プロセッサの取得]:http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409

