<properties 
	pageTitle="既存の Blob を Media Services 資産にコピーする" 
	description="このトピックでは、既存の blob を Media Services 資産にコピーする方法を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="juliako"/>

#既存の Blob を Media Services 資産にコピーする

このトピックでは、ストレージ アカウントから新しい Microsoft Azure Media Services 資産に blob をコピーする方法について説明します。

blob は、Media Services アカウントに関連付けられた (または関連付けられていない) ストレージ アカウントに存在します。このトピックでは、ストレージ アカウントから Media Services 資産に blob をコピーする方法について説明します。データ センター間でもコピーできることにご注意ください。ただし、これによって料金が発生する可能性があります。価格の詳細については、「[データ転送](http://azure.microsoft.com/pricing/#header-11)」をご覧ください。

>[AZURE.NOTE]Media Services によって生成された BLOB コンテナーの内容を変更する場合は、必ず Media Service API を使用してください。

##前提条件

- 新規または既存の Azure サブスクリプションで作成した 2 つの Media Services アカウント。「[Media Services アカウントの作成方法](media-services-create-account.md)」を参照してください。
- オペレーティング システム: Windows 10、Windows 7、Windows Server 2008 R2、または Windows 8。
- .NET Framework 4.5。
- Visual Studio 2010 SP1 (Professional、Premium、Ultimate、または Express) 以降。

##プロジェクトの設定

このセクションでは、C# コンソール アプリケーション プロジェクトを作成、設定できます。

1. Visual Studio 2012 または Visual Studio 2010 SP1 を使用して、C# コンソール アプリケーション プロジェクトを含む新しいソリューションを作成します。 
2. 名前に「CopyExistingBlobsIntoAsset」と入力し、[OK] をクリックします。
1. Nuget を使用して Media Services 関連の Dll への参照を追加します。Visual Studio のメイン メニューで、[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール] の順に選択します。コンソール ウィンドウで「Install-Package windowsazure.mediaservices」と入力し、Enter キーを押します。
1. このプロジェクト (System.Configuration) に必要なその他の参照を追加します。
1. 既定で Programs.cs ファイルに追加されたステートメントを使用して、次と置き換えます。
		
		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure;
		using System.Web;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Microsoft.WindowsAzure.Storage.Auth;

1. appSettings セクションを .config ファイルに追加し、Media Services、ストレージ キー、名前の値に基づいて値を更新します。

		<appSettings>
		  <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
		  <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
		  <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
		  <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
		  <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
		  <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
		</appSettings>


##ストレージ アカウントから Media Services 資産に blob をコピーします。

以下のコード例では次のタスクを実行します。

1. CloudMediaContext インスタンスを作成します。 
1. CloudStorageAccount インスタンス (\_sourceStorageAccount と \_destinationStorageAccount) を作成します。
1. Smooth Streaming ファイルをローカル ディレクトリから \_sourceStorageAccount に配置された blob コンテナーにアップロードします。
1. 新しい資産を作成します。この資産用に作成される blob コンテナーは \_destinationStorageAccountに配置されます。
1. Azure Storage SDK を使用して、指定した blob を資産に関連付けられたコンテナーにコピーします。

>[AZURE.NOTE]ロケーターの有効期限が切れている場合、コピー操作では例外はスローされません。

1. .ism ファイルをプライマリ ファイルとして設定します。
1. 資産に関連付けられている OnDemandOrigin ロケーターの Smooth Streaming URL を作成します。 
		
		class Program
		{
		    // Read values from the App.config file. 
		    static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
		    static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		    static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
		    static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
		    static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
		    static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
		
		    private static MediaServicesCredentials _cachedCredentials = null;
		    private static CloudMediaContext _context = null;
		
		    private static CloudStorageAccount _sourceStorageAccount = null;
		    private static CloudStorageAccount _destinationStorageAccount = null;
		
		    static void Main(string[] args)
		    {
		        _cachedCredentials = new MediaServicesCredentials(
		                        _accountName,
		                        _accountKey);
		        // Use the cached credentials to create CloudMediaContext.
		        _context = new CloudMediaContext(_cachedCredentials);
		
		        // In this example the storage account from which we copy blobs is not 
		        // associated with the Media Services account into which we copy blobs.
		        // But the same code will work for coping blobs from a storage account that is 
		        // associated with the Media Services account.
		        //
		        // Get a reference to a storage account that is not associated with a Media Services account
		        // (an external account).  
		        StorageCredentials externalStorageCredentials =
		            new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
		        _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
		
		        //Get a reference to the storage account that is associated with a Media Services account. 
		        StorageCredentials mediaServicesStorageCredentials =
		            new StorageCredentials(_storageAccountName, _storageAccountKey);
		        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
		
		        // Upload Smooth Streaming files into a storage account.
		        string localMediaDir = @"C:\supportFiles\streamingfiles";
		        CloudBlobContainer blobContainer =
		            UploadContentToStorageAccount(localMediaDir);
		
		        // Create a new asset and copy the smooth streaming files into 
		        // the container that is associated with the asset.
		        IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
		
		        // Get the streaming URL for the smooth streaming files 
		        // that were copied into the asset.   
		        string urlForClientStreaming = CreateStreamingLocator(asset);
		        Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
		
		        Console.ReadLine();
		    }
		
		    /// <summary>
		    /// Uploads content from a local directory into the specified storage account.
		    /// In this example the storage account is not associated with the Media Services account.
		    /// </summary>
		    /// <param name="localPath">The path from which to upload the files.</param>
		    /// <returns>The container that contains the uploaded files.</returns>
		    static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
		    {
		        CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
		        CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
		
		        externalMediaBlobContainer.CreateIfNotExists();
		
		        // Upload files to the blob container.  
		        DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
		        foreach (var file in uploadDirectory.EnumerateFiles())
		        {
		            CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
		
		            blob.UploadFromFile(file.FullName, FileMode.Open);
		        }
		
		        return externalMediaBlobContainer;
		    }
		
		    /// <summary>
		    /// Creates a new asset and copies blobs from the specifed storage account.
		    /// </summary>
		    /// <param name="mediaBlobContainer">The specified blob container.</param>
		    /// <returns>The new asset.</returns>
		    static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
		    {
		        // Create a new asset. 
		        IAsset asset = _context.Assets.Create("CopyBlob_" + Guid.NewGuid(), AssetCreationOptions.None);
		
		        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy", TimeSpan.FromHours(24), AccessPermissions.Write);
		        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
		
		        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
		
		        // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
		        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
		
		        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);
		
		        if (assetContainer.CreateIfNotExists())
		        {
		            assetContainer.SetPermissions(new BlobContainerPermissions
		            {
		                PublicAccess = BlobContainerPublicAccessType.Blob
		            });
		        }
		
		        var blobList = mediaBlobContainer.ListBlobs();
		        foreach (var sourceBlob in blobList)
		        {
		            var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
		            CopyBlob(sourceBlob as ICloudBlob, assetContainer);
		            assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
		            assetFile.Update();
		        }
		
		        destinationLocator.Delete();
		        writePolicy.Delete();
		
		        // Since we copied a set of Smooth Streaming files, 
		        // set the .ism file to be the primary file. 
		        SetISMFileAsPrimary(asset);
		
		        return asset;
		    }
		
		    /// <summary>
		    /// Creates the OnDemandOrigin locator in order to get the streaming URL.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    /// <returns>The streaming URL.</returns>
		    static public string CreateStreamingLocator(IAsset asset)
		    {
		        var ismAssetFile = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
		
		        // Create a 30-day readonly access policy. 
		        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		            TimeSpan.FromDays(30),
		            AccessPermissions.Read);
		
		        // Create a locator to the streaming content on an origin. 
		        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		            policy,
		            DateTime.UtcNow.AddMinutes(-5));
		
		        return originLocator.Path + ismAssetFile.Name + "/manifest";
		    }
		
		    /// <summary>
		    /// Copies the specified blob into the specified container.
		    /// </summary>
		    /// <param name="sourceBlob">The source container.</param>
		    /// <param name="destinationContainer">The destination container.</param>
		    static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
                var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
                {
                    Permissions = SharedAccessBlobPermissions.Read,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
                });

                ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

                if (destinationBlob.Exists())
                {
                    Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
                }
                else
                {
                    try
                    {
                        Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                        destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));


                        while (true)
                        {
                            // The StartCopyFromBlob is an async operation, 
                            // so we want to check if the copy operation is completed before proceeding. 
                            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                            destinationBlob.FetchAttributes();
                            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                            {
                                break;
                            }
                            //It's still not completed. So wait for some time.
                            System.Threading.Thread.Sleep(1000);
                        }

                        Console.WriteLine("Final blob copy status = " + destinationBlob.CopyState.Status);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine(string.Format("Error copying blob '{0}': {1}", sourceBlob.Name, ex.Message));
                    }
                }
            }
		
		    /// <summary>
		    /// Sets a file with the .ism extension as a primary file.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    static private void SetISMFileAsPrimary(IAsset asset)
		    {
		        var ismAssetFiles = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
		
		        if (ismAssetFiles.Count() != 1)
		            throw new ArgumentException("The asset should have only one, .ism file");
		
		        ismAssetFiles.First().IsPrimary = true;
		        ismAssetFiles.First().Update();
		    }
		}
 

##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->