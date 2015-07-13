<properties 
	pageTitle=".NET を使用してアセットの配信ポリシーを構成する" 
	description="このトピックでは、さまざまなアセットの配信ポリシーを構成する方法を示します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#方法: アセットの配信ポリシーを構成する
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」や「[Media Services のライブ ストリーミングのワークフロー](media-services-live-streaming-workflow.md)」シリーズの一部です。

Media Services におけるコンテンツ配信ワークフローの手順の 1 つは、ストリームするアセットの配信ポリシーを構成することです。アセットの配信ポリシーは、アセットを配信する方法、つまりどのストリーミング プロトコルでアセットを動的パッケージングするか (例 : MPEG DASH、HLS、スムーズ ストリーミング、またはすべて)、アセットを動的に暗号化するかどうか、どの暗号化方法を使用するか (エンベロープ暗号化または共通暗号化) を Media Services に示します。

このトピックでは、アセットの配信ポリシーを作成して構成する理由と方法をご説明します。

>[AZURE.NOTE]動的パッケージ化と動的暗号化を使用するには、少なくとも 1 つのスケール単位 (ストリーミング ユニットとも呼ばれる) が存在している必要があります。詳細については、「[Media Services の規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」をご覧ください。
>
>また、アセットには、一連のアダプティブ ビットレート MP4、またはアダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。

1 つのアセットにはさまざまなポリシーを適用できます。たとえば、スムーズ ストリーミングに PlayReady 暗号化を適用し、MPEG DASH と HLS に AES エンベロープ暗号化を適用できます。配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。ただし、配信ポリシーをまったく定義していない場合は例外となります。この場合、すべてのプロトコルが平文で許可されます。

ストレージ暗号化アセットを配信する場合は、アセットの配信ポリシーを構成する必要があることにご注意ください。アセットをストリームするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシーを使用してコンテンツをストリームする必要があります。たとえば、Advanced Encryption Standard (AES) エンベロープ暗号化キーを使用して暗号化されたアセットを配信するには、ポリシーの種類を **DynamicEnvelopeEncryption** に設定します。ストレージ暗号化を解除してアセットを平文でストリームするには、ポリシーの種類を **NoDynamicEncryption** に設定します。これらのポリシーの種類を構成する例を次に示します。

アセットの配信ポリシーの構成方法に応じて、動的パッケージングと動的暗号化を実行し、次のストリーミング プロトコル (スムーズ ストリーミング、HLS、MPEG DASH、HDS ストリーム) を使用してストリームを行うことができます。

次の一覧に、スムース、HLS、DASH、HDS でストリームを行うために使用するフォーマットを示します。

スムーズ ストリーミング:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

アセットを発行し、ストリーミング URL を構築する手順については、「[Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

##アセットの配信ポリシーを解除する 

次の **ConfigureClearAssetDeliveryPolicy** メソッドでは、動的暗号化を適用せず、MPEG DASH、HLS、スムーズ ストリーミングのいずれかのプロトコルでストリームを配信することを指定します。
  
AssetDeliveryPolicy を作成する際に指定できる値については、[AssetDeliveryPolicy を定義するときに使用される種類](#types)セクションをご覧ください。

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##DynamicCommonEncryption アセットの配信ポリシー 


次の **CreateAssetDeliveryPolicy** メソッドでは、動的で一般的な暗号化 (DynamicCommonEncryption) をスムーズ ストリーミング プロトコルに適用するよう構成された **AssetDeliveryPolicy** (他のプロトコルはストリーミングからブロック) を作成します。メソッドには、**Asset** (配信ポリシーを適用するアセット) と **IContentKey** (**CommonEncryption** タイプのコンテンツ キーの 2 つのパラメーターがあります。詳細については、「[Creating a content key (コンテンツ キーの作成)](media-services-dotnet-create-contentkey.md#common_contentkey)」をご覧ください)。

AssetDeliveryPolicy を作成する際に指定できる値については、[AssetDeliveryPolicy を定義するときに使用される種類](#types)セクションをご覧ください。


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }



##DynamicEnvelopeEncryption アセットの配信ポリシー 

次の **CreateAssetDeliveryPolicy** メソッドでは、動的エンベロープ暗号化 (**DynamicEnvelopeEncryption**) を HLS と DASH プロトコルに適用するよう構成された **AssetDeliveryPolicy** (他のプロトコルはストリーミングからブロック) を作成します。メソッドには、**Asset** (配信ポリシーを適用するアセット) と **IContentKey** (**EnvelopeEncryption** タイプのコンテンツ キーの 2 つのパラメーターがあります。詳細については、「[Creating a content key (コンテンツ キーの作成)](media-services-dotnet-create-contentkey.md#envelope_contentkey)」をご覧ください)。


AssetDeliveryPolicy を作成する際に指定できる値については、[AssetDeliveryPolicy を定義するときに使用される種類](#types)セクションをご覧ください。

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>AssetDeliveryPolicy の定義に使用する種類

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    } 

<!---HONumber=July15_HO1-->