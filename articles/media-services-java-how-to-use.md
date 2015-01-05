<properties urlDisplayName="Media Services" pageTitle="Media Services の使用方法 (Java) - Azure の機能ガイド" metaKeywords="Azure Media Services, Azure media, Azure streaming, azure media, azure streaming, azure encoding" description="Describes how to use Azure Media Services to perform common tasks including encoding, encrypting, and streaming resources." metaCanonical="" services="media-services" documentationCenter="Java" title="How to Use Media Services" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="robmcm" />

#メディア サービスを使用する方法

このガイドでは、Azure メディア サービスを使用した Java によるプログラミングの基礎について説明します。メディア サービスの技術的概要、メディア サービスに使用する Azure アカウントの構成手順、標準的なプログラミング タスクの実行方法を示すコードなどで構成されます。 

## 目次

-   [メディア サービスとは](#media_services)
-   [メディア サービスに使用する Azure アカウントの設定](#setup-account)
-   [メディア サービス開発のための設定](#setup-dev)
-   [方法: Java でメディア サービスを使用する](#connect)
-   [その他のリソース](#additional-resources)

 
##<a id="media_services"></a>メディア サービスとは

Azure メディア サービスは、Microsoft Media Platform とサードパーティのメディア コンポーネントのそれぞれの良さを Azure に統合する拡張可能なメディア プラットフォームです。メディア サービスは、クラウドの中でメディアパイプラインとして機能します。業界パートナーは、メディア サービスを利用して、コンポーネント テクノロジを拡張したり、置き換えたりすることができます。ISV やメディア プロバイダーは、メディア サービスを利用して、エンド ツー エンドのメディア ソリューションを構築できます。ここでは、メディア サービスの全般的なアーキテクチャと一般的な開発シナリオの概要を説明します。

以下の図は、メディア サービスの基本アーキテクチャを示したものです。

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###メディア サービスの機能サポート
最新リリースのメディア サービスには、クラウドにおけるメディア アプリケーションの開発用途として、次の機能セットが用意されています。 

- **取り込み**。たとえば、Azure ストレージに配置するアセットを事前にアップロードしたり暗号化したりすることによって、システムにアセットを取り込む操作です。メディア サービスの RTM リリースまでには、パートナー コンポーネントとの統合が可能となり、高速な UDP (ユーザー データグラム プロトコル) アップロード ソリューションが実現します。
- **エンコード**。エンコード操作には、メディア アセットのエンコードや変換が伴います。エンコード タスクは、メディア サービスに付属のメディア エンコーダーを使用し、クラウドで実行することができます。次のエンコード機能があります。
   - Azure メディア エンコーダーの使用と一連の標準コーデックおよび形式への対応 (業界をリードする IIS スムーズ ストリーミング、MP4、Apple HTTP ライブ ストリーミングへの変換など)。
   - 入力と出力をトータルに制御することでライブラリ全体または個々のファイルを変換。
   - ファイル タイプ、形式、コーデックを豊富にサポート (「[Supported File Types for Media Services (メディア サービスでサポートされるファイルの種類)][]」を参照)。
   - 形式の変換。メディア サービスでは、ISO MP4 (.mp4) を Smooth Streaming File Format (PIFF 1.3) (.ismv、.isma) に変換できます。Smooth Streaming File Format (PIFF) を Apple HTTP ライブ ストリーミング (.msu8、.ts) に変換することもできます。
- **保護**。コンテンツの保護とは、ライブ ストリーミングやオンデマンド コンテンツを暗号化することによって、送信、保存、配信のセキュリティを保護することです。メディア サービスは、特定の DRM テクノロジにとらわれないコンテンツ保護ソリューションを提供します。現在サポートされている DRM テクノロジは、Microsoft PlayReady Protection および MPEG Common Encryption です。その他の DRM テクノロジも今後サポートされる予定です。 
- **ストリーム**。コンテンツのストリーミングとは、コンテンツをライブでまたはオンデマンドでクライアントに送信したり、特定のメディア ファイルをクラウドから取得 (ダウンロード) したりすることです。メディア サービスは、特定の形式にとらわれないコンテンツ ストリーミングを提供します。メディア サービスでは、スムーズ ストリーミング、Apple HTTP ライブ ストリーミング、および MP4 形式が、ストリーミングの配信元としてサポートされます。その他の形式も今後サポートされる予定です。Azure CDN またはサードパーティ CDN を使用してストリーミング コンテンツをシームレスに配信することもでき、必要に応じて、配信規模を何百万というユーザーにまで拡張することができます。   

###メディア サービス開発シナリオ
メディア サービスでサポートされる代表的なメディア開発シナリオを次の表に示します。 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>シナリオ</th>
       <th>説明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>エンド ツー エンド ワークフローの構築</td>
        <td>クラウドにより、包括的なメディア配信ワークフローを構築できます。メディアのアップロードからコンテンツ配信まで、メディアサービスは、特定のアプリケーション ワークフローに合わせて組み合わせられるコンポーネントを提供します。現在は、アップロード、ストレージ、エンコード、形式変換、コンテンツ保護、オンデマンド ストリーミング配信などの機能が含まれます。</td>
    </tr>
    <tr>
        <td>ハイブリッド ワークフローの構築</td>
        <td>Media Services は、既存のツールやプロセスと統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にトランスコードするためにメディア サービスにアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。Media Services を標準 REST API 経由で個別に呼び出し、外部アプリケーションやサービスと統合できます。</td>
    </tr>
    <tr>
        <td>メディア プレーヤーのクラウド サポート</td>
        <td>多様なデバイス (iOS、Android、Windows デバイスなど) およびプラットフォームを対象にメディアを作成、管理、配信できます。</td>
    </tr>
  </tbody>
</table>

###メディア サービス クライアント開発
SDK とプレーヤー フレームワークを使用してメディア クライアント アプリケーションを構築することによって、メディア サービス ソリューションの裾野を広げることができます。メディア サービス アプリケーションを構築する開発者は、幅広いデバイスとプラットフォームを対象に魅力的なエクスペリエンスをユーザーに提供することができます。SDK およびプレーヤー フレームワークは、構築するクライアント アプリケーションの動作環境となるデバイスに応じて Microsoft やサードパーティ パートナーから提供されています。  

以下に示したのは、クライアント SDK およびプレーヤー フレームワークの一覧です。これらの SDK とプレーヤー フレームワーク、サポート可能な機能の詳細については、「[Media Services Client Development (メディア サービス クライアント開発)]」を参照してください。今後予定されている SDK とプレーヤー フレームワークについても記載されています。 

####Mac および PC クライアントのサポート  
Microsoft Silverlight または Adobe Open Source Media Framework を使用することにより、PC および Mac を対象にしてストリーミング エクスペリエンスを提供できます。

-	[Silverlight 用 Smooth Streaming Client](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform:Silverlight 用プレーヤー フレームワーク](http://smf.codeplex.com/documentation)
-	[OSMF 2.0 用スムーズ ストリーミング プラグイン](http://go.microsoft.com/fwlink/?LinkId=275022)。このプラグインを使用する方法については、「[Adobe Open Source Media Framework 用 Microsoft スムーズ ストリーミング プラグインを使用する方法](http://go.microsoft.com/fwlink/?LinkId=275034)」を参照してください。

####Windows 8 アプリケーション
Windows 8 用には、サポートされている開発言語やコンストラクト (HTML、Javascript、XAML、C#、C+ など) を使用して Windows ストア アプリケーションを作成できます。

-	[Windows 8 用 Smooth Streaming Client SDK](http://go.microsoft.com/fwlink/?LinkID=246146)。この SDK を使用して Windows ストア アプリケーションを作成する方法の詳細については、「[スムーズ ストリーミング用の Windows ストア アプリケーションを作成する方法](http://go.microsoft.com/fwlink/?LinkId=271647)」を参照してください。HTML5 でスムーズ ストリーミング プレイヤーを作成する方法については、「[Walkthrough:Building Your First HTML5 Smooth Streaming Player (チュートリアル: 初めての HTML5 スムーズ ストリーミング プレイヤーを作成する)](http://msdn.microsoft.com/ja-jp/library/jj573656.aspx)」を参照してください。

-	[Microsoft Media Platform:Windows 8 Windows ストア アプリケーション用プレーヤー フレームワーク](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
Xbox では、スムーズ ストリーミング コンテンツを使用する Xbox LIVE アプリケーションがサポートされています。Xbox LIVE アプリケーション開発キット (ADK) には、次のコンポーネントが含まれています。

-	Xbox LIVE ADK 用 Smooth Streaming Client
-	Microsoft Media Platform:Xbox LIVE ADK 用プレーヤー フレームワーク

####組み込みデバイスまたは専用デバイス
カスタム アプリケーション開発フレームワークおよびカスタム メディア パイプラインを備えたデバイス (スマート テレビ、セットトップ ボックス、ブルーレイ プレーヤー、OTT TV ボックス、モバイル デバイスなど)。Microsoft では、次に示すライセンス取得可能な移植キットを提供しています。これらを使用すると、プラットフォームに対応したスムーズ ストリーミング再生機能の移植が可能になります。

-	[Smooth Streaming Client Porting Kit](http://www.microsoft.com/ja-jp/mediaplatform/sspk.aspx)
-	[Microsoft PlayReady Device Porting Kit](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
Microsoft では、Windows Phone 用の優れたビデオ アプリケーションを作成するために使用できる SDK を提供しています。 

-	[Silverlight 用 Smooth Streaming Client](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform:Silverlight 用プレーヤー フレームワーク](http://smf.codeplex.com/documentation)

####iOS デバイス
iPhone、iPod、iPad などの iOS デバイス用に Microsoft で用意されている SDK(Smooth Streaming SDK for iOS Devices with PlayReady) を使用すると、これらのプラットフォームを対象にして、高品質のビデオ コンテンツを提供するアプリケーションを作成できます。この SDK を使用するには、ライセンスの取得が必要です。詳細については、[Microsoft に電子メールでお問い合わせください](mailto:askdrm@microsoft.com)。iOS 開発の詳細については、「[iOS デベロッパー センター](https://developer.apple.com/devcenter/ios/index.action)」を参照してください。

####Android デバイス
複数の Microsoft パートナーにより、Android デバイスでのスムーズ ストリーミング再生機能を追加するための Android プラットフォーム用 SDK が提供されています。パートナーの詳細については、[Microsoft に電子メールでお問い合わせください](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices) 。


##<a id="setup-account"></a>メディア サービスに使用する Azure アカウントの設定

メディア サービス アカウントを設定するには、Azure 管理ポータルを使用します。「[メディア サービス アカウントの作成方法][]」を参照してください。管理ポータルでアカウントを作成すると、メディア サービス開発用にコンピューターをセットアップできるようになります。 

##<a id="setup-dev"></a>メディア サービス開発のための設定

このセクションでは、Media Services SDK for Java を使用したメディア サービス開発の大まかな前提条件について説明します。

###前提条件

-   新規または既存の Azure サブスクリプションで作成した Media Services アカウント。「[メディア サービス アカウントの作成方法][]」を参照してください。
-   Azure Libraries for Java。これは [Azure Java デベロッパー センター][]からインストールできます。

##<a if="connect"></a>方法:Java で Media Services を使用する

次のコードは、アセットを作成してメディア ファイルをアセットにアップロードし、アセットの変換タスクを伴うジョブを実行して、変換されたアセットの出力ファイルをダウンロードする方法を示しています。

このコードを使用する前に、メディア サービス アカウントを設定する必要があります。アカウントの設定方法については、「[メディア サービス アカウントの作成方法](http://www.windowsazure.com/ja-jp/manage/services/media-services/how-to-create-a-media-services-account/)」を参照してください。

変数 `clientId` および `clientSecret` は、実際の値に置き換えてください。また、このコードは、ローカル保存されているファイル `c:/media/MPEG4-H264.mp4` に依存しています。実際に使用するファイルを用意する必要があります。コードには、出力ファイルのダウンロード先となる出力フォルダー `c:/output` も必要です。

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/ja-jp/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

作成したアセットは、Azure ストレージに保存されます。ただし、アセットを追加、更新、または削除するには、(Azure ストレージ API ではなく) Azure メディア サービス API のみを使用してください。

###使用できるメディア プロセッサの判定

前のコードでは、特定のメディア プロセッサ名でアクセスしてメディア プロセッサを使用しました (複数のバージョンがある場合は最新バージョンを使用)。使用できるメディア プロセッサを判定するには、次のコードを使用できます。

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

代替方法として、次のコードは、メディア プロセッサの ID を名前で取得する方法を示しています。

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

###ジョブの取り消し
処理が完了していないジョブを取り消す必要がある場合は、次に示すコードで、ジョブをジョブ ID で取り消す方法を参照してください。

    mediaService.action(Job.cancel(jobId));

##< id="additional-resources"></a>その他のリソース

メディア サービスに関する Javadoc ドキュメントについては、[Azure Libraries for Java のドキュメント][]を参照してください。

<!-- URLs. -->

  [Media Services アカウントの作成方法]: http://go.microsoft.com/fwlink/?linkid=256662
  [Azure Java デベロッパー センター]: http://www.windowsazure.com/ja-jp/develop/java/
  [Azure Libraries for Java のドキュメント]: http://dl.windowsazure.com/javadoc/
  [メディア サービス クライアント開発]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223283.aspx


<!--HONumber=35.1-->
