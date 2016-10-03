<properties 
	pageTitle="Azure ポータルを使用したコンテンツ キー承認ポリシーの構成 | Microsoft Azure" 
	description="コンテンツ キー承認ポリシーを構成する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/19/2016" 
	ms.author="juliako"/>



#コンテンツ キー承認ポリシーを構成する
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Overview

Microsoft Azure Media Services を使用すると、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) または [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/) で保護された MPEG DASH、Smooth Streaming、HTTP ライブ ストリーミング (HLS) のストリームを配信できます。AMS では、Widevine DRM で暗号化された DASH ストリームを配信することもできます。PlayReady と Widevine はいずれも共通暗号化 (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。

Media Services では、クライアントが暗号化されたコンテンツを再生するために AES キーまたは PlayReady/Widevine ライセンスを取得できる**キー/ライセンス配信サービス**も提供します。

このトピックでは、**Azure クラシック ポータル**を使用して、コンテンツ キー承認ポリシーを構成する方法について説明します。キーは、動的にコンテンツを暗号化するために、後で使用できます。現時点で暗号化できるストリーミング形式は、HLS、MPEG DASH、およびスムーズ ストリーミングであることに注意してください。HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

プレーヤーが動的に暗号化するように設定されたストリームを要求すると、Media Services は構成済みのキーを使用して、AES または DRM 暗号化によってコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。


複数のコンテンツ キーを使用する場合や、Media Services キー配信サービス以外の**キー/ライセンス配信サービス** の URL を指定する場合は、Media Services .NET SDK または REST API を使用します。

[Media Services .NET SDK を使用したコンテンツ キー承認ポリシーの構成](media-services-dotnet-configure-content-key-auth-policy.md)

[Media Services REST API を使用したコンテンツ キー承認ポリシーの構成](media-services-rest-configure-content-key-auth-policy.md)

###いくつかの考慮事項が適用されます。

- 動的パッケージングや動的暗号化を使用するには、少なくとも 1 つのストリーミング予約ユニットが必要です。詳細については、「[Media Services の規模の設定方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。
- 資産には、一連のアダプティブ ビットレート MP4 または アダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。詳細については、「[資産をエンコードする](media-services-encode-asset.md)」をご覧ください。
- キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。ContentKeyAuthorizationPolicy を作成して、"Token" 制限を使用するように指定した場合に、"Token" 制限をテストしてから、ポリシーを "Open" 制限に更新すると、ポリシーが "Open" バージョンのポリシーに切り替わるまで、約 15 分かかります。


##方法: キーの承認ポリシーを構成する

キーの承認ポリシーを構成するには、**[コンテンツ保護]** ページを選択します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。コンテンツ キー承認ポリシーには、**オープン**、**トークン**、または**IP** 承認制限を設定することができます (**IP** は REST または .NET SDK を使用して構成できます)。

###オープン制限

**オープン**制限では、キーを要求するすべてのユーザーにシステムがキーを配信します。この制限は、テストに便利です。

![OpenPolicy][open_policy]

###トークン制限

トークン制限ポリシーを選択するには、**[トークン]** ボタンをクリックします。

**トークン**制限ポリシーには、**Secure Token Service** (STS) によって発行されたトークンを含める必要があります。Media Services では、**Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 形式と **JSON Web Token** (JWT) 形式のトークンがサポートされます。詳細については、「[JWT token authentication](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)」(JWT トークン認証) を参照してください。

Media Services では、**Secure Token Service** は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。Media Services のキー配信サービスは、トークンが有効で、トークン内の要求がコンテンツ キー向けに構成された要求と一致する場合、暗号化キーをクライアントに返します。詳細については、「[Use Azure ACS to issue tokens](http://mingfeiy.com/acs-with-key-services)」(Azure ACS を使用してトークンを発行する) をご覧ください。

**トークン**制限ポリシーを構成する場合は、**検証キー**、**発行者**、**対象ユーザー**の値を設定する必要があります。プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

###PlayReady

**PlayReady** を使用してコンテンツを保護する場合、PlayReady ライセンス テンプレートを定義する XML 文字列を承認ポリシーで指定する必要があります。既定では、次のポリシーが設定されます。

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"> <LicenseTemplates> <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates> </PlayReadyLicenseResponseTemplate>

**[ポリシー xml のインポート]** ボタンをクリックして、[ここ](https://msdn.microsoft.com/library/azure/dn783459.aspx)で定義された XML スキーマに準拠した別の XML を指定します。


##次のステップ

Media Services のラーニング パスを確認します。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

<!---HONumber=AcomDC_0921_2016-->