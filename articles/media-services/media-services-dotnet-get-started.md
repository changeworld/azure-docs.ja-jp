<properties
	pageTitle=".NET SDK を使用してビデオ オン デマンド (VoD) を配信する"
	description="このチュートリアルでは、Azure Media Services と .NET を使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順を紹介します。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/18/2015"
	ms.author="juliako"/>


# .NET SDK を使用してビデオ オン デマンド (VoD) を配信する

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

このチュートリアルでは、Azure Media Services (AMS) SDK for .NET を使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順について説明します。


チュートリアルでは Media Services の基本的なワークフローや、Media Services 開発に必要となる一般的なプログラミング オブジェクトやタスクについて紹介します。このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、ダウンロードして、ストリーミングやプログレッシブ ダウンロードを実行できます。

## 前提条件
Media Services SDK for .NET で開発を行うには、次の前提条件を満たす必要があります。

- オペレーティング システム: Windows 8 以降、Windows 2008 R2、Windows 7。
- .NET Framework 4.5 か .NET Framework 4.0
- Visual Studio 2013、Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate、Express のいずれか)。


このクイック スタートでは、次のタスクが表示されます。

1.  Media Services アカウントの作成 (Azure ポータルを使用)
2.  ストリーミング エンドポイントの構成 (ポータルを使用)
3.  Visual Studio プロジェクトの作成と構成
5.  メディア サービス アカウントに接続します。
6.  新しいアセットを作成し、ビデオ ファイルをアップロードします。
7.  一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードします。
8.  資産を発行してストリーミング URL とプログレッシブ ダウンロード URL を取得します。  
9.  コンテンツの再生


##ポータルを使用した Media Services アカウントの作成

1. Azure ポータルで、**[新規]**、**[メディア サービス]**、**[簡易作成]** の順にクリックします。

	![メディア サービスの簡易作成](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. **[名前]** ボックスに新しいアカウントの名前を入力します。メディア サービス アカウント名は、使用できる文字が小文字または数字だけで、空白を含めることはできず、長さは 3 ～ 24 文字です。

3. **[リージョン]** ボックスで、メディア サービス アカウントのメタデータ レコードを保存するリージョンを選択します。ドロップダウン リストには利用可能な メディア サービス リージョンのみが表示されます。

4. **[ストレージ アカウント]** ボックスで、メディア サービス アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。メディア サービス アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。新しいストレージ アカウントは同じリージョンに作成されます。

5. 新しいストレージ アカウントを作成した場合は、**[新しいストレージ アカウント名]** ボックスにストレージ アカウントの名前を入力します。ストレージ アカウントの命名規則は、メディア サービス アカウントと同じです。

6. フォームの下部にある **[簡易作成]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	アカウントが正常に作成されると、ステータスが **[アクティブ]** に変わります。

	ページ下部に **[キーの管理]** ボタンが表示されます。このボタンをクリックすると、Media Services アカウント名、プライマリ キー、セカンダリ キーがダイアログに表示されます。Media Services アカウントにプログラムからアクセスするには、アカウント名とプライマリ キーの情報が必要です。

	![[メディア サービス] ページ](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	アカウント名をダブルクリックすると、既定で **[クイック スタート]** ページが表示されます。このページでは、ポータルの別のページでも実行できる管理タスクをいくつか実行できます。たとえば、ビデオ ファイルのアップロードは、このページから実行することも、[コンテンツ] ページから実行することもできます。

##ポータルを使用したストリーミング エンドポイントの構成

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする (エンコーディングの手順は後述)。  
- コンテンツ配信元となる**ストリーミング エンドポイント**のストリーミング ユニットを少なくとも 1 つ取得する。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

ストリーミング予約ユニットの数を変更するには、以下の手順を実行します。

1. [ポータル](https://manage.windowsazure.com/)で、**[メディア サービス]** をクリックします。次に、メディア サービスの名前をクリックします。

2. [ストリーミング エンドポイント] ページを選択します。次に、変更するストリーミング エンドポイントをクリックします。

3. ストリーミング ユニットの数を指定するには、[スケール] タブをクリックし、**[占有容量]** スライダーを動かします。

	![[スケール] ページ](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. **[保存]** を押して変更を保存します。

	新しいユニットの割り当ては完了するまでに約 20 分かかります。

	>[AZURE.NOTE]現在のところ、ストリーミング ユニットの数を正の値からゼロに戻すと、ストリーミングが最大 1 時間無効になります。
	>
	> コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。価格の詳細については、「[Media Services の料金詳細](http://go.microsoft.com/fwlink/?LinkId=275107)」をご覧ください。



##Visual Studio プロジェクトの作成と構成

1. Visual Studio 2013、Visual Studio 2012 か Visual Studio 2010 SP1 で、C# の新しいコンソール アプリケーションを作成します。**[名前]**、**[場所]**、**[ソリューション名]** を入力し、**[OK]** をクリックします。

2. Nuget パッケージ [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) を使用して **Azure Media Services .NET SDK Extensions** をインストールします。Media Services .NET SDK Extensions は、コードを簡素化し、Media Services による開発を容易にする一連の拡張メソッドとヘルパー機能です。このパッケージをインストールすると、**Media Services .NET SDK** が一緒にインストールされるほか、必要な依存関係がすべて追加されます。

3. System.Configuration アセンブリへの参照を追加します。このアセンブリには、構成ファイル (App.config など) にアクセスするための **System.Configuration.ConfigurationManager** クラスが含まれています。

4. App.config ファイルを開き (既定で追加されていない場合はファイルをプロジェクトに追加してください)、ファイルに *appSettings* セクションを追加します。Azure Media Services のアカウント名とアカウント キーの値を設定します。次の例をご覧ください。アカウント名とキー情報を取得するには、Azure ポータルを開いて Media Services のアカウントを選択し、**[キーの管理]** をクリックします。

	 <pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>

5. Program.cs ファイルの先頭にある既存の **using** ステートメントを次のコードで上書きします。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. プロジェクト ディレクトリに新しいフォルダーを作成し、エンコード、ストリーミング、プログレッシブ ダウンロードの対象となる .mp4 ファイルか .wmv ファイルをコピーします。この例では、"C:\VideoFiles" というパスを使用しています。

##Media Services アカウントへの接続

Media Services を .NET で使用するとき、Media Services に関連したプログラミング タスクの大半、たとえば、各種オブジェクト (アセット、アセット ファイル、ジョブ、アクセス ポリシー、ロケーターなど) の作成、更新、アクセス、削除の作業で、**CloudMediaContext** クラスが必要となります。

既定の Program クラスを次のコードで上書きします。このコードは、App.config ファイルから接続値を読み取り、**CloudMediaContext** オブジェクトを作成して Media Services に接続する方法を示しています。Media Services への接続の詳細については、「[Media Services SDK for .NET を使用した Media Services への接続](http://msdn.microsoft.com/library/azure/jj129571.aspx)」をご覧ください。

**Main** 関数で呼び出すメソッドは、この後、定義していきます。

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##新しいアセットの作成とビデオ ファイルのアップロード

Media Services で、デジタル ファイルを資産にアップロードし (取り込み) ます。**Asset** エンティティには、ビデオ、オーディオ、画像、縮小表示のコレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。 ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。アセット内のこれらのファイルを**アセット ファイル**といいます。

以下に定義した **UploadFile** メソッドは、(.NET SDK Extensions で定義されている) **CreateFromFile** を呼び出します。**CreateFromFile** によって、指定されたソース ファイルのアップロード先となる新しいアセットが作成されます。

**CreateFromFile** メソッドの **AssetCreationOptions** 引数には、次のいずれかのアセット作成オプションを指定できます。

- **None**: 暗号化は使用されません。これが既定値です。このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。
- **StorageEncrypted** – Advanced Encryption Standard (AES)-256 ビット暗号化を使用してクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードすると、コンテンツが保存時に暗号化された状態で格納されます。StorageEncrypted で保護された資産は、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力資産として再びアップロードできます。StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
- **CommonEncryptionProtected**: 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
- **EnvelopeEncryptionProtected**: AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

**CreateFromFile** メソッドには、ファイルのアップロードの進行状況をレポートするためのコールバックも指定できます。

次の例では、資産のオプションに **None** を指定しています。

次のメソッドを Program クラスに追加します。

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });

	    Console.WriteLine("Asset {0} created.", inputAsset.Id);

	    return inputAsset;
	}


##一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする

Media Services に取り込んだ資産には、メディアのエンコード、再パッケージ化、透かしの追加などをクライアントへの配信前に適用できます。高いパフォーマンスと可用性を確保するために、これらの作業は、複数のバックグラウンド ロール インスタンスに対してスケジューリングされて実行されます。これらのアクティビティはジョブと呼ばれ、各ジョブは、資産ファイルの実際の作業を実行するアトミック タスクで構成されます。

冒頭で述べたように、Azure Media Services の代表的な用途の 1 つは、クライアントに対するアダプティブ ビットレート ストリーミング配信です。Media Services では、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) のいずれかの形式に一連のアダプティブ ビットレート MP4 ファイルを動的にパッケージ化することができます。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする。  
- コンテンツ配信元となるストリーミング エンドポイントのストリーミング ユニットを少なくとも 1 つ取得する。

以下のコードは、エンコーディング ジョブの送信方法を示したものです。このジョブには、**Azure メディア エンコーダー**を使用して mezzanine ファイルを一連のアダプティブ ビットレート MP4 にトランスコードするよう指定するタスクが 1 つ存在します。このコードは、ジョブを送信してその完了を待機します。

ジョブが完了したら、アセットをストリーミングしたり、トランスコードによって作成された MP4 ファイルをプログレッシブにダウンロードしたりできます。
MP4 ファイルをプログレッシブにダウンロードするためにオンデマンド ストリーミング ユニットを取得する必要はありません。


次のメソッドを Program クラスに追加します。

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);

		Console.WriteLine("Submitting transcoding job...");


	    // Submit the job and wait until it is completed.
	    job.Submit();

	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;

	    Console.WriteLine("Transcoding job finished.");

	    IAsset outputAsset = job.OutputMediaAssets[0];

	    return outputAsset;
	}

##資産を発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する

資産をストリーミングまたはダウンロードするにはまず、ロケーターを作成して資産を「発行」する必要があります。資産に含まれているファイルには、ロケーターを通じてアクセスできます。Media Services では、2 種類のロケーターがサポートされています。OnDemandOrigin ロケーターはメディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) に、Access Signature (SAS) ロケーターはメディア ファイルのダウンロードに使用します。

ロケーターを作成したら、対象ファイルのストリーミングやダウンロードに使用する URL を作成します。


スムーズ ストリーミングのストリーミング URL の形式は次のとおりです。

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS のストリーミング URL の形式は次のとおりです。

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH のストリーミング URL の形式は次のとおりです。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

ファイルをダウンロードするための SAS URL の形式は次のとおりです。

	{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK Extensions には、発行済みのアセットの URL を正しい形式で取得できるヘルパー メソッドが用意されています。

次のコードでは、.NET SDK Extensions を使用してロケーターを作成し、ストリーミング URL と プログレッシブ ダウンロード URL を取得しています。また、ローカル フォルダーにファイルをダウンロードする方法もこのコードで確認できます。

次のメソッドを Program クラスに追加します。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##コンテンツの再生  

前のセクションで定義したプログラムを実行すると、コンソール ウィンドウに次のような URL が表示されます。

アダプティブ ストリーミングの URL:

スムーズ ストリーミング

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

プログレッシブ ダウンロードの URL (オーディオとビデオ):

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


ビデオをストリーミングするには、[Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用します。

プログレッシブ ダウンロードをテストするには、ブラウザー (Internet Explorer、Chrome、Safari など) に URL を貼り付けます。

##次のステップ

ビデオ オン デマンド アプリケーションの構築の詳細については、「[VoD アプリケーションの構築](media-services-video-on-demand-workflow.md)」をご覧ください。

###その他のリソース
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 今すぐビデオをオンラインにしましょう!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動的パッケージ化機能とモバイル デバイス</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理ポータル]: http://manage.windowsazure.com/

<!---HONumber=August15_HO8-->