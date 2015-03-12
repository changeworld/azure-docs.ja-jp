<properties 
	pageTitle=".NET を使用してアセットの配信ポリシーを構成する" 
	description="このトピックでは、異なるアセットの配信ポリシーの構成方法について説明します。" 
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
	ms.date="02/06/2015" 
	ms.author="juliako"/>

#方法:アセットの配信ポリシーを構成する
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

この記事は、[Media Services Video on Demand workflow (メディア サービス ビデオ オン デマンドのワークフロー)](../media-services-video-on-demand-workflow) と [Media Services Live Streaming workflow (メディア サービス ライブ ストリーミングのワークフロー)](../media-services-live-streaming-workflow) シリーズの一部です。 

メディア サービスのコンテンツ配信ワークフローの手順の 1 つに、ストリーミングするアセットの配信ポリシーの構成があります。アセットの配信ポリシーは、メディア サービスにアセットの配信方法を伝えます。たとえば、どのストリーミング プロトコルにアセットを動的にパッケージ化するか (MPEG DASH、HLS、スムーズ ストリーミング、またはそのすべてなど)、アセットを動的に暗号化するかどうか、そして暗号化の方法 (エンベロープまたは一般的な暗号化) といった情報です。 

このトピックでは、アセットの配信ポリシーを構成する理由とその方法について説明します。 

>[AZURE.NOTE]動的パッケージングと動的暗号化を使用するには、少なくとも 1 つ以上のスケール ユニット (別名ストリーミング ユニット) が必要です。詳細については、「[メディア サービス アカウントでストリーミング エンドポイントを管理する方法](../media-services-manage-origins#scale_streaming_endpoints)」をご覧ください。 
>
>また、アセットにはアダプティブ ビットレート MP4 ファイル またはアダプティブ ビットレート スムーズ ストリーミング ファイルのセットが含まれている必要があります。      

同じアセットに異なるポリシーを適用できます。たとえば、PlayReady の暗号化をスムーズ ストリーミングに適用し、AES エンベロープ暗号化を MPEG DASH や HLS に適用できます。配信ポリシーで定義されていないプロトコルは (プロトコルに HLS のみを指定した 1 つのポリシーを追加した場合など)、ストリーミングからブロックされます。アセットの配信ポリシーを 1 つも定義していない場合は例外です。その場合、すべてのプロトコルがプレーン テキストで許可されます。

ストレージ暗号化されたアセットを配信する場合は、アセットの配信ポリシーを構成する必要があります。アセットをストリーミングする前に、ストリーミング サーバーがストレージ暗号化を削除し、指定した配信ポリシーを使用してコンテンツを配信します。たとえば、高度暗号化標準 (AES) エンベロープ暗号化キーで暗号化されたアセットを配信するには、ポリシーの種類を **DynamicEnvelopeEncryption** に設定します。ストレージ暗号化を削除してアセットをプレーン テキストでストリーミングするには、ポリシーの種類を **NoDynamicEncryption** に設定します。これらのポリシーの構成方法を示す例を後ほど紹介します。 

アセットの配信ポリシーをどのように構成するかによって、動的パッケージングや動的暗号化を使用したり、次のストリーミング プロトコルをストリーミングしたりできます。スムーズ ストリーミング、HLS、MPEG DASH ストリーミング  

次の一覧は、スムーズ、HLS、DASH、HDS のストリーミングに使用する形式を示しています。  

スムーズ ストリーミング:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

アセットを発行してストリーミング URL を構築する方法については、「[方法:ストリーミング コンテンツを配信する](../media-services-deliver-streaming-content)」をご覧ください。

##アセットの配信ポリシーをクリアする 

次の **ConfigureClearAssetDeliveryPolicy** メソッドでは、動的暗号化を適用せず、MPEG DASH、HLS、スムーズ ストリーミングのいずれかでストリーミングを配信するよう指定します。 
  
AssetDeliveryPolicy の作成時に指定できる値については、「[AssetDeliveryPolicy の定義に使用する種類](#types)」 セクションをご覧ください。 

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##DynamicCommonEncryption のアセット配信ポリシー 


次の **CreateAssetDeliveryPolicy** メソッドでは、動的で一般的な暗号化 (**DynamicCommonEncryption**) をスムーズ ストリーミング プロトコルに適用するよう構成された **AssetDeliveryPolicy** (他のプロトコルはストリーミングからブロック) を作成します。メソッドには、**Asset** (配信ポリシーを適用するアセット) と **IContentKey** (**CommonEncryption** タイプのコンテンツ キー。詳細については、[「Creating a content key (コンテンツ キーの作成)](../media-services-dotnet-create-contentkey#common_contentkey)」 を参照) の 2 つのパラメーターがあります。

AssetDeliveryPolicy の作成時に指定できる値については、「[AssetDeliveryPolicy の定義に使用する種類](#types)」セクションをご覧ください。 


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



##DynamicEnvelopeEncryption のアセット配信ポリシー 

次の **CreateAssetDeliveryPolicy** メソッドでは、動的エンベロープ暗号化 (**DynamicEnvelopeEncryption**) を HLS と DASH プロトコルに適用するよう構成された **AssetDeliveryPolicy** (他のプロトコルはストリーミングからブロック) を作成します。メソッドには、**Asset** (配信ポリシーを適用するアセット) と **IContentKey** (**EnvelopeEncryption** タイプのコンテンツ キー。詳細については、[「Creating a content key (コンテンツ キーの作成)](../media-services-dotnet-create-contentkey#envelope_contentkey)」 を参照) の 2 つのパラメーターがあります。


AssetDeliveryPolicy の作成時に指定できる値については、「[AssetDeliveryPolicy の定義に使用する種類](#types)」 セクションをご覧ください。   

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
<!--HONumber=45--> 
