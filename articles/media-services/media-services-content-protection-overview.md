<properties 
	pageTitle="コンテンツ保護の概要" 
	description="この記事では、Media Services でのコンテンツ保護のの概要について説明します。" 
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
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="juliako"/>

# コンテンツ保護の概要

Microsoft Azure Media Services では、高度暗号化標準 (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。Media Services では、承認されたクライアントにキーと PlayReady ライセンスを配信するためのサービスも提供しています。 

![content-protection][content-protection]

動的暗号化を使用するには、暗号化されたコンテンツのストリーミング元となるストリーミング エンドポイントに、少なくとも 1 つのストリーミング占有ユニットが必要です。

## 概念

### 動的な暗号化

Microsoft Azure Media Services では、高度暗号化標準 (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。 

現時点では、HLS、MPEG DASH、スムーズ ストリーミングを暗号化できます。HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

Media Services で資産を暗号化する場合は、暗号化キー (CommonEncryption か EnvelopeEncryption) を資産に関連付ける必要があります。また、キーの承認ポリシーを構成する必要があります。

資産の配信ポリシーを構成する必要もあります。ストレージで暗号化された資産をストリーミングする場合は、資産の配信ポリシーを構成して、資産の配信方法を指定する必要があります。  

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES か PlayReady でコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

### PlayReady DRM ライセンスと AES クリア キー配信サービス

Media Services は、承認されたクライアントに PlayReady ライセンスと AES クリア キーを配信するためのサービスを提供しています。Azure 管理ポータル、REST API、または Media Services SDK for .NET を使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

ポータルを使用している場合、構成できるのは 1 つの AES ポリシー (AES で暗号化されたすべてのコンテンツに適用されます) および 1 つの PlayReady ポリシー (PlayReady で暗号化されたすべてのコンテンツに適用されます) であることに注意してください。さらに詳細に構成を制御したい場合は、Media Services SDK for .NET を使用してください。

### PlayReady ライセンス テンプレート

Media Services には PlayReady ライセンスを提供するサービスが用意されています。エンド ユーザーのプレーヤー (Silverlight など) は、PlayReady で保護されたコンテンツを再生しようとする際に、ライセンス配信サービスにライセンス取得要求を送信します。ライセンス サービスはその要求を承認した後、ライセンスを発行します。このライセンスはクライアントに送信され、指定されたコンテンツの暗号化解除と再生に用いられます。

ライセンスには、保護されたコンテンツをユーザーが再生しようとしたときに PlayReady DRM ランタイムが適用する権限や制限が含まれます。Media Services は、PlayReady ライセンスの構成用 API を備えています。詳細については、「[Media Services PlayReady ライセンス テンプレートの概要](https://msdn.microsoft.com/library/azure/dn783459.aspx)」をご覧ください。

### トークン制限

コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、IP 制限) を指定できます。トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。Media Services では、Simple Web Token (SWT) 形式と JSON Web Token (JWT) 形式のトークンがサポートされます。Media Services では、Secure Token Services は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。トークンが有効であり、なおかつトークンに含まれる要求とキー (またはライセンス) に対して構成されている要求とが一致した場合、Media Services キー配信サービスは、キー (またはライセンス) をクライアントに返します。
トークン制限ポリシーを構成する際は、プライマリ検証キー、発行者、対象ユーザーの各パラメーターを指定する必要があります。プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

## 一般的なシナリオ

### ストレージ内のコンテンツを保護し、動的に暗号化されたストリーミング メディアを配信する  

1. 高品質な中間ファイルをアセットに取り出します。ストレージ暗号化オプションをアセットに適用します。
2. ストリーミング エンドポイントを構成します。
1. アダプティブ ビットレート MP4 セットにエンコードします。ストレージ暗号化オプションを出力アセットに適用します。
1. 再生中に動的に暗号化するアセットの、暗号化コンテンツ キーを作成します。
2. コンテンツ キー承認ポリシーを構成します。
1. アセット配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
1. オンデマンド ロケーターを作成してアセットを発行します。
1. 公開済みコンテンツをストリーミングします。

### Media Service キーとライセンス配信サービスを自分の暗号化とストリーミング サービスで使用する

詳細については、「[How to integrate Azure PlayReady License service with your own encryptor/streaming server (Azure PlayReady ライセンス サービスと個人の暗号化/ストリーミング サーバーをを統合する方法)](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)」をご覧ください。

## 一般的なコンテンツ暗号化タスク

>[AZURE.NOTE]コンテンツが暗号化されたストレージの場合は、アセット配信ポリシーを構成してください。

### ストリーミング エンドポイントの構成

ストリーミング エンドポイントの概要とその管理方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md)」をご覧ください。

### コンテンツ キーの作成

**.NET** または **REST API** を使用して、アセットを暗号化するコンテンツ キーを作成します。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

### コンテンツ キー承認ポリシーの構成 

**.NET** または **REST API** を使用してコンテンツ保護やキー承認ポリシーを構成するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

### アセット配信ポリシーの構成

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


## 関連リンク

[Announcing PlayReady as a service and AES dynamic encryption with Azure Media Services (Azure Media Services でのサービスと AES 動的暗号化としての PlayReady の発表)](http://mingfeiy.com/playready)

[Azure Media Services PlayReady license delivery pricing explained (Azure Media Services PlayReady のライセンス配信料金の説明)](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[How to debug for AES encrypted stream in Azure Media Services (Azure Media Services で AES 暗号化ストリームをデバッグする方法)](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT トークンの承認](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT クレームに基づいてコンテンツ キーの配信を制限する](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Azure ACS を使用してトークンを発行する](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png


<!--HONumber=52--> 