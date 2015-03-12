<properties 
	pageTitle="動的暗号化:.NET を使用してコンテンツ キー承認ポリシーを構成します。" 
	description="コンテンツ キー承認ポリシーを構成する方法について説明します。" 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>



#動的暗号化:コンテンツ キー承認ポリシーを構成する 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

この記事は、「[Media Services Video on Demand workflow (メディア サービスのビデオ オンデマンド ワークフロー)](../media-services-video-on-demand-workflow)」シリーズと「[Media Services Live Streaming workflow (メディア サービスのライブ ストリーミング ワークフロー)](../media-services-live-streaming-workflow)」シリーズの一部です。 

##概要

Microsoft Azure メディア サービスでは、高度暗号化標準 (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。メディア サービスでは、承認されたクライアントにキーと PlayReady ライセンスを配信するためのサービスも提供しています。 

現時点では、HLS、MPEG DASH、スムーズ ストリーミングを暗号化できます。HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

メディア サービスでアセットを暗号化する場合は、[こちら](../media-services-dotnet-create-contentkey/)の説明に従って暗号化キー (**CommonEncryption** か **EnvelopeEncryption**) をアセットに関連付ける必要があります。また、このトピックでの説明に従って、キーの承認ポリシーを構成する必要があります。 

プレーヤーがストリームを要求すると、メディア サービスは指定されたキーを使用して、AES か PlayReady でコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。キーの取得が許可されているユーザーかどうか判断するために、サービスはそのキー用に指定された認証ポリシーを評価します。

メディア サービスでは、キーの要求を行うユーザーを認証する複数の方法がサポートされています。コンテンツ キー承認ポリシーには、**オープン**、**トークン**制限、**IP** 制限のうち 1 つか複数の承認制限を指定できます。トークン制限ポリシーには、Secure Token Service (STS) で発行されたトークンを付加する必要があります。メディア サービスでは、**Simple Web Token** ([SWT](https://msdn.microsoft.com/ja-jp/library/gg185950.aspx#BKMK_2)) 形式と **JSON Web Token** (JWT) 形式のトークンがサポートされています。  

メディア サービスでは、Secure Token Services は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、指定されたキーで署名されたトークンを作成し、トークン制限構成で指定したクレームを発行するよう構成する必要があります (この記事の説明を参照)。トークンが有効で、トークンのクレームとコンテンツ キー用に構成されたクレームと一致する場合、メディア サービスのキー配信サービスはクライアントに暗号化キーを返します。

詳細については、以下のページをご覧ください。 

[JWT token authenitcation (JWT トークン認証)](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrate Azure Media Services OWIN MVC based app with Azure Active Directory and restrict content key delivery based on JWT claims (Azure Active Directory に Azure メディア サービスの OWIN MVC ベースのアプリを統合し、JWT クレームに基づいてコンテンツ キーの配信を制限する)](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Use Azure ACS to issue tokens (Azure ACS を使用してトークンを発行する)](http://mingfeiy.com/acs-with-key-services)

###以下の考慮事項があります。

- 動的パッケージ化と動的暗号化を行うには、1 つ以上のスケール ユニット (ストリーミング ユニットとも呼ばれる) が必要です。詳細については、「[メディア サービスの規模の設定方法](../media-services-manage-origins#scale_streaming_endpoints)」をご覧ください。 
- アセットは、マルチビット レート MP4 ファイルかマルチビットレートのスムーズ ストリーミング ファイルを含む必要があります。詳細については、「[アセットをエンコードする](../media-services-encode-asset/)」をご覧ください。  
- **AssetCreationOptions.StorageEncrypted** オプションを使用して、アセットをアップロードしてエンコードします。
- 複数のコンテンツ キーで同じポリシー構成を必要とする場合は、1 つの承認ポリシーを作成して、複数のコンテンツ キーに利用することを強くお勧めします。
- キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。ContentKeyAuthorizationPolicy を作成して、"Token" 制限を使用するように指定した場合に、"Token" 制限をテストしてから、ポリシーを "Open" 制限に更新すると、ポリシーが "Open" バージョンのポリシーに切り替わるまで、約 15 分かかります。
- アセットの配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。


##AES-128 動的暗号化 

###オープン制限

オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、コンテンツ キーに追加しています。
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###トークン制限

このセクションでは、コンテンツ キー承認ポリシーを作成し、コンテンツ キーに関連付ける方法について説明します。承認ポリシーには、ユーザーにキーの受け取り権限があるかどうかを判定するために、満たす必要がある承認要件 (たとえば、"確認キー" 一覧に、トークンに署名をしたキーが含まれているか) が記述されます。

トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。トークン制限を構成する XML は、次の XML スキーマに準拠する必要があります。

####<a id="schema"></a>トークン制限のスキーマ
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

**トークン**制限ポリシーを構成する際には、プライマリ**認証キー**と**発行者**と**対象ユーザー**の各パラメーターを指定する必要があります。**プライマリ認証キー**には、トークンの署名に使用したキーを含めます。**発行者**には、トークンを発行する STS を指定します。**対象ユーザー** (**スコープ**と呼ばれることもあります) には、トークンの目的やトークンがアクセスを承認するリソースを指定します。メディア サービス キー配信サービスは、トークン内のこれらの値が、テンプレートの値と一致することを検証します。 

**Media Services SDK for .NET** を使用する場合、**TokenRestrictionTemplate** クラスを使用して制限トークンを生成できます。
次の例では、トークン制限を含む承認ポリシーを作成しています。この例では、クライアントが、署名キー (VerificationKey)、トークン発行者、必要なクレームを含むトークンを提示する必要があります。
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>テスト トークン

キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、次の処理を行います。
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word "Bearer" in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##PlayReady 動的暗号化 

メディア サービスでは、権限と制限を構成して、保護されたコンテンツをユーザーが再生しようとしたときに PlayReady DRM ランタイムが適用されるようにすることができます。 

PlayReady でコンテンツを保護する場合、承認ポリシーで、[PlayReady ライセンス テンプレート](https://msdn.microsoft.com/ja-jp/library/azure/dn783459.aspx)を定義する XML 文字列を指定する必要があります。Media Services SDK for .NET では、**PlayReadyLicenseResponseTemplate** クラスと **PlayReadyLicenseTemplate** クラスを利用して、PlayReady ライセンス テンプレートを定義できます。 

###オープン制限
	
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、コンテンツ キーに追加しています。

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

###トークン制限

トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。トークン制限を構成する XML は、[こちら]の(#schema) セクションの XML スキーマに準拠する必要があります。
	
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
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	
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

キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、[こちら]の(#test) セクションをご覧ください。 

##<a id="types"></a>ContentKeyAuthorizationPolicy を定義するときに使用される型

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##次のステップ
これでコンテンツ キーの承認ポリシーを構成できました。次は、「[How to configure asset delivery policy (アセット配信ポリシーを構成する方法)](../media-services-dotnet-configure-asset-delivery-policy/)」トピックに進みます。

<!--HONumber=45--> 
