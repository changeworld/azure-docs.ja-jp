<properties
	pageTitle="PlayReady または Widevine の動的共通暗号化を使用する"
	description="Microsoft Azure Media Services を使用すると、MPEG DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを Microsoft PlayReady DRM で保護して配信できます。Widevine DRM で暗号化された DASH を配信することもできます。このトピックでは、PlayReady と Widevine DRM を使用して動的に暗号化する方法について説明します。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="10/07/2015"
	ms.author="juliako"/>


#PlayReady または Widevine の動的共通暗号化を使用する

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)

Microsoft Azure Media Services を使用すると、MPEG DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/) で保護して配信できます。Widevine DRM で暗号化された DASH を配信することもできます。PlayReady と Widevine はいずれも共通暗号化 (CENC) 仕様に従って暗号化されます。[AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (バージョン 3.5.1 以降) または REST API を使用して、Widevine を使用するように AssetDeliveryConfiguration を構成できます。

また、Media Services では、Microsoft PlayReady ライセンスの配信サービスも提供されるようになりました。また、Media Services で提供される API を使用して、保護されたコンテンツをユーザーが再生しようとしたときに PlayReady DRM ランタイムが適用する権限と制限を構成できます。ユーザーが PlayReady で保護されたコンテンツを要求すると、クライアント プレーヤー アプリケーションが Azure Media Services にコンテンツを要求します。要求を受けた Azure Media Services はクライアントを Azure Media Services PlayReady ライセンス サーバーにリダイレクトし、そこでコンテンツへのユーザーのアクセスが認証および承認されます。PlayReady ライセンスには、クライアント プレーヤーがコンテンツの暗号化解除とストリーミングに使用できる暗号化解除キーが含まれています。

>[AZURE.NOTE]現在、Media Services では、Widevine のライセンス サーバーは提供されません。Widevine ライセンスは、AMS パートナーの [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/) を通して配信できます。
>
> 詳細については、[Axinom](media-services-axinom-integration.md) と [castLabs](media-services-castlabs-integration.md) の統合を参照してください。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、IP 制限) を指定できます。トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。Media Services では、[Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 形式と [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式のトークンがサポートされます。詳細については、「コンテンツ キー承認ポリシーを構成する」を参照してください。

動的暗号化を使用するには、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産が必要です。また、資産の配信ポリシーを構成する必要があります (このトピック内で後述します)。次に、ストリーミング URL で指定された形式に基づいて、オンデマンド ストリーミング サーバーは、選択されたプロトコルでストリームを配信できるようにします。その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

このトピックには、保護されたメディアを配信するアプリケーションの開発に取り組む開発者にとって有用な情報が含まれています。このトピックでは、認証ポリシーを使用する PlayReady ライセンス配信サービスの構成方法を説明します。これにより、許可されたクライアントのみが PlayReady ライセンスを受け取ることができるようになります。また、動的暗号化の使用方法についても説明します。

>[AZURE.NOTE]動的暗号化を使用するには、まず、スケール ユニット (ストリーミング ユニットとも呼ばれる) を少なくとも 1 つ取得する必要があります。詳細については、「[Media Services の規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」をご覧ください。

##動的共通暗号化と PlayReady ライセンス配信サービスの構成

以下では、Media Services ライセンス配信サービスと動的暗号化を使用して、PlayReady で資産を保護する際に実行する必要のある一般的な手順について説明します。

1. 資産を作成し、その資産にファイルをアップロードします。 
1. ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードします。
1. コンテンツ キーを作成し、それをエンコードした資産に関連付けます。Media Services では、コンテンツ キーに資産の暗号化キーが含まれています。
1. コンテンツ キーの承認ポリシーを構成します。コンテンツ キー承認ポリシーを構成する必要があります。コンテンツ キーがクライアントに配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。 
1. 資産の配信ポリシーを構成します。配信ポリシーの構成には、配信プロトコル (たとえば、MPEG DASH、HLS、HDS、Smooth Streaming、またはそのすべて)、動的暗号化の種類 (たとえば、共通暗号化)、PlayReady ライセンス取得 URL が含まれます。 
 
	同じ資産の各プロトコルに異なるポリシーを適用できます。たとえば、PlayReady 暗号化を Smooth/DASH に適用し、AES Envelope を HLS に適用できます。配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。ただし、資産配信ポリシーをまったく定義していない場合は例外となります。この場合、すべてのプロトコルが平文で許可されます。
1. ストリーミング URL を取得するために OnDemand ロケーターを作成します。

このトピックの最後に、詳細な .NET の例があります。

前述したワークフローを図にすると、次のようになります。ここでは、認証のためにトークンが使用されています。

![Protect with PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

このトピックの残りの部分では、詳細な説明、コード例、および前述したタスクの実行方法を説明するトピックへのリンクを紹介します。

##現時点での制限事項

資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。

##資産を作成し、その資産にファイルをアップロードする

ビデオの管理、エンコード、およびストリーミングを行うには、最初にコンテンツを Microsoft Azure Media Services にアップロードする必要があります。コンテンツをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

詳細については、「[Media Services アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)」を参照してください。

##ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードする

動的暗号化を使用する場合に必要となるのは、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産の作成のみです。そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。詳細については、「[動的パッケージの概要](media-services-dynamic-packaging-overview.md)」を参照してください。

エンコード手順については、「[Media Encoder Standard を使用して資産をエンコードする方法](media-services-dotnet-encode-with-media-encoder-standard.md)」を参照してください。
	

##<a id="create_contentkey"></a>コンテンツ キーを作成し、それをエンコードした資産に関連付ける

Media Services では、コンテンツ キーに、資産を暗号化するときに使用するキーが含まれています。

詳細については、[コンテンツ キーの作成](media-services-dotnet-create-contentkey.md)に関するページを参照してください。


##<a id="configure_key_auth_policy"></a>コンテンツ キー承認ポリシーの構成

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。コンテンツ キー承認ポリシーを構成する必要があります。キーがクライアント (プレーヤー) に配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、IP 制限) を指定できます。

詳細については、「[コンテンツ キー承認ポリシーを構成する](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)」を参照してください。

##<a id="configure_asset_delivery_policy"></a>資産の配信ポリシーを構成する 

資産の配信ポリシーを構成します。資産の配信ポリシーの構成には、次の内容が含まれます。

- PlayReady ライセンス取得 URL 
- 資産配信プロトコル (たとえば、MPEG DASH、HLS、HDS、Smooth Streaming、またはすべて)。 
- 動的暗号化のタイプ (この場合は、共通暗号化) 

詳細については、「[資産の配信ポリシーを構成する](media-services-rest-configure-asset-delivery-policy.md)」を参照してください。

##<a id="create_locator"></a>ストリーミング URL を取得するために OnDemand ロケーターを作成する

Smooth、DASH、HLS のストリーミング URL をユーザーに提供する必要があります。

>[AZURE.NOTE]資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。

アセットを発行し、ストリーミング URL を構築する手順については、「[Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

##テスト トークンを取得する

キー認証ポリシーに使用されたトークンによる制限に基づいて、テスト トークンを取得します。

	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate = 
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the data in the given TokenRestrictionTemplate.
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

	
[AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用して、ストリームをテストできます。

##<a id="example"></a>例

1. 新しいコンソール プロジェクトを作成します。
1. NuGet を使用して、Azure Media Services .NET SDK Extensions をインストールして追加します。このパッケージをインストールすると、Media Services .NET SDK が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
2. その他の参照 (System.Runtime.Serialization と System.Configuration) を追加します。
2. アカウント名とキー情報が含まれた構成ファイルを追加します。

	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。
	
	必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		
		namespace CommonDynamicEncryptAndKeyDeliverySvc
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
		            Console.WriteLine();
		    
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		
		                //The GenerateTestToken method returns the token without the word “Bearer” in front
		                //so you have to add it in front of the token string. 
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		

		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted MPEG-DASH URL: {0}/Manifest(format=mpd-time-csf) ", url);
		
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
	
				static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
				    // Create a task with the encoding details, using a string preset.
				    // In this case "H264 Multiple Bitrate 720p" preset is used.
				    ITask task = job.Tasks.AddNew("My encoding task",
				        processor,
				        "H264 Multiple Bitrate 720p",
				        TaskOptions.None);
				
				    // Specify the input asset to be encoded.
				    task.InputAssets.Add(asset);
				    // Add an output asset to contain the results of the job. 
				    // This output is specified as AssetCreationOptions.None, which 
				    // means the output asset is not encrypted. 
				    task.OutputAssets.AddNew("Output asset",
				        AssetCreationOptions.None);
				
				    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
				    job.Submit();
				    job.GetExecutionProgressTask(CancellationToken.None).Wait();
				
				    return job.OutputMediaAssets[0];
				}

		
		        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
		        {
		            // Create common encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryption);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		    {
		
		        // Create ContentKeyAuthorizationPolicy with Open restrictions 
		        // and create authorization policy          
		
		        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		        {
		            new ContentKeyAuthorizationPolicyRestriction 
		            { 
		                Name = "Open", 
		                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
		                Requirements = null
		            }
		        };
		
		        // Configure PlayReady license template.
		        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		        IContentKeyAuthorizationPolicyOption policyOption =
		            _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.PlayReadyLicense,
		                    restrictions, newLicenseTemplate);
		
		        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                    ContentKeyAuthorizationPolicies.
		                    CreateAsync("Deliver Common Content Key with no restrictions").
		                    Result;
		
		
		        contentKeyAuthorizationPolicy.Options.Add(policyOption);
		
		        // Associate the content key authorization policy with the content key.
		        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		        contentKey = contentKey.UpdateAsync().Result;
		    }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("HLS token restricted authorization policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		            {
		                new ContentKeyAuthorizationPolicyRestriction 
		                { 
		                    Name = "Token Authorization Policy", 
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                    Requirements = tokenTemplateString, 
		                }
		            };
		
		            // Configure PlayReady license template.
		            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, newLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with no restrictions").
		                        Result;
		            
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKeyAuthorizationPolicy.Options.Add(policyOption);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        } 
		
		        static private string ConfigurePlayReadyLicenseTemplate()
		        {
		            // The following code configures PlayReady License Template using .NET classes
		            // and returns the XML string.
		             
		            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
		            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
		
		            responseTemplate.LicenseTemplates.Add(licenseTemplate);
		
		            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
		        }
		
		
				static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
				{
				    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
				
				    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
				        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
				    {
				        {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
				        {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl,"http://testurl"},
				        
				    };
				
				    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
				            "AssetDeliveryPolicy",
				        AssetDeliveryPolicyType.DynamicCommonEncryption,
				        AssetDeliveryProtocol.Dash,
				        assetDeliveryPolicyConfiguration);
				
				   
				    // Add AssetDelivery Policy to the asset
				    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
				
				}
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }


				static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
				{
				    Console.WriteLine("Job state changed event:");
				    Console.WriteLine("  Previous state: " + e.PreviousState);
				    Console.WriteLine("  Current state: " + e.CurrentState);
				    switch (e.CurrentState)
				    {
				        case JobState.Finished:
				            Console.WriteLine();
				            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
				            break;
				        case JobState.Canceling:
				        case JobState.Queued:
				        case JobState.Scheduled:
				        case JobState.Processing:
				            Console.WriteLine("Please wait...\n");
				            break;
				        case JobState.Canceled:
				        case JobState.Error:
				
				            // Cast sender as a job.
				            IJob job = (IJob)sender;
				
				            // Display or log error details as needed.
				            break;
				        default:
				            break;
				    }
				}
				
				
				static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
				{
				    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
				    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
				
				    if (processor == null)
				        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
				
				    return processor;
				}
		    }
		}


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##関連項目

[Configure Widevine packaging with AMS (AMS を使用して Widevine パッケージを構成する)](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

<!---HONumber=Oct15_HO2-->