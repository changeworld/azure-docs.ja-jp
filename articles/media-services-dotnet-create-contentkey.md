<properties 
	pageTitle=".NET を使用したコンテンツ キーの作成" 
	description="アセットに安全にアクセスできる ContentKey を作成する方法について学習します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#.NET を使用したコンテンツ キーの作成

この記事は、「[Media Services Video on Demand workflow (メディア サービスのビデオ オンデマンド ワークフロー)](../media-services-video-on-demand-workflow)」シリーズと「[Media Services Live Streaming workflow (メディア サービスのライブ ストリーミング ワークフロー)](../media-services-live-streaming-workflow)」シリーズの一部です。  

メディア サービスでは、暗号化されたアセットを新しく作成して送信できます。**ContentKey** で**アセット**に安全にアクセスできます。 

新しいアセットを作成するときは (たとえば[ファイルをアップロードする](../media-services-dotnet-upload-files/)前)、**StorageEncrypted**、**CommonEncryptionProtected**、**EnvelopeEncryptionProtected** などの暗号化オプションを指定できます。 

クライアントにアセットを配信するときには、[アセットを動的に暗号化するように構成](../media-services-dotnet-configure-asset-delivery-policy)できます。その際、**DynamicEnvelopeEncryption**、**DynamicCommonEncryption** のいずれか 1 つを使用します。

暗号化されたアセットには、**ContentKey** を関連付ける必要があります。この記事では、コンテンツ キーを作成する方法について説明します。

>[AZURE.NOTE] Media Services .NET SDK を使用して **StorageEncrypted** のアセットを作成する場合、**ContentKey** が自動的に作成され、アセットにリンクします。

##ContentKeyType

コンテンツ キーの作成時には、タイプを設定する必要があります。次のいずれかの値を選択します。 

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>エンベロープ タイプの ContentKey の作成

次のコード スニペットでは、エンベロープ暗号化タイプのコンテンツ キーを作成します。その後、キーを指定されたアセットに関連付けます。

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>共通タイプの ContentKey の作成    

次のコード スニペットでは、共通暗号化タイプのコンテンツ キーを作成します。その後、キーを指定されたアセットに関連付けます。

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
call

	IContentKey key = CreateCommonTypeContentKey(encryptedsset);
<!--HONumber=45--> 
