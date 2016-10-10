<properties 
	pageTitle="Azure ポータルを使用したコンテンツ保護ポリシーの構成 | Microsoft Azure" 
	description="この記事では、Azure ポータルを使用してコンテンツ保護ポリシーを構成する方法を説明します。また、資産に対して動的な暗号化を有効にする方法についても説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016"    
	ms.author="juliako"/>

# Azure ポータルを使用したコンテンツ保護ポリシーの構成

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

## Overview

Microsoft Azure Media Services (AMS) を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。Media Services では、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) と、PlayReady、Widevine DRM、Apple FairPlay を使用する共通暗号化 (CENC) 使用して、動的に暗号化したコンテンツを配信できます。

AMS は、承認されたクライアントに DRM ライセンスと AES クリア キーを配信するためのサービスを提供しています。Azure ポータルでは、すべてのタイプの暗号化に対して 1 つの**キー/ライセンス承認ポリシー**を作成できます。

この記事では、Azure ポータルでコンテンツ保護ポリシーを構成する方法を説明します。また、動的な暗号化を資産に適用する方法についても説明します。

> [AZURE.NOTE]  コンテンツ保護ポリシーをの作成に Azure クラシック ポータルを使用した場合、ポリシーが [Azure ポータル](https://portal.azure.com/)に表示されない場合がありますが、すべての以前のポリシーが存在しています。これを調べるには、Azure Media Services .NET SDK または [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) ツールを使用します (ポリシーを表示するには、資産を右クリックし、情報を表示し (F4)、[コンテンツ キー] タグをクリックして、キーをクリックします)。
> 
> 新しいポリシーを使用して資産を暗号化する場合は、Azure ポータルを使用してこれらを構成し、[保存] をクリックして、動的な暗号化を再適用します。

## コンテンツ保護の構成を開始する

ポータルを使用して AMS アカウントに対してグローバルなコンテンツ保護の構成を開始するには、次の操作を実行します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. **[設定]**、**[コンテンツ保護]** を選択します。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## キー/ライセンス承認ポリシー

AMS では、キーまたはライセンスを要求するユーザーを承認する複数の方法がサポートされています。キー/ライセンスがクライアントに配信されるには、自身で、またはクライアントがこのコンテンツ キー承認ポリシーを構成する必要があります。コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (**オープン**または**トークン**制限) を指定できます。

Azure ポータルでは、すべてのタイプの暗号化に対して 1 つの**キー/ライセンス承認ポリシー**を作成できます。

###オープン 

オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。この制限は、テストに便利です。

### トークン

トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。Media Services では、Simple Web Tokens (SWT) 形式と JSON Web Token (JWT) 形式のトークンがサポートされます。Media Services では、Secure Token Services は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。トークンが有効であり、なおかつトークンに含まれる要求とキー (またはライセンス) に対して構成されている要求とが一致した場合、Media Services キー配信サービスは、キー (またはライセンス) をクライアントに返します。

トークン制限ポリシーを構成する際は、プライマリ検証キー、発行者、対象ユーザーの各パラメーターを指定する必要があります。プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## PlayReady 権利テンプレート

PlayReady 権利テンプレートについて詳しくは、「[Media Services PlayReady ライセンス テンプレートの概要](media-services-playready-license-template-overview.md)」をご覧ください。

### 非永続的

ライセンスを非永続的として構成した場合にのみ、プレーヤーがライセンスを使用するときにこれがメモリに保持されます。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### 永続的

ライセンスを永続的として構成した場合、これは、クライアントの永続的ストレージに保存されます。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## Widevine 権利テンプレート

Widevine 権利テンプレートについて詳しくは、「[Widevine ライセンス テンプレートの概要](media-services-widevine-license-template-overview.md)」をご覧ください。

### 基本

**[Basic]** を選択すると、テンプレートはすべて既定値で作成されます。

### 詳細

Widevine 構成の詳細オプションについて詳しくは、[この](media-services-widevine-license-template-overview.md)トピックをご覧ください。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## FairPlay 構成

FairPlay 暗号化を有効にするには、FairPlay 構成オプションを使用してアプリ証明書およびアプリケーション シークレット キー (ASK) を指定する必要があります。FairPlay 構成と要件について詳しくは、[この](media-services-protect-hls-with-fairplay.md)記事を参照してください。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## 資産への動的な暗号化の適用

動的な暗号化機能を利用するには、次の作業が必要になります。

- 一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードします。
- コンテンツ配信元となるストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[オンデマンド ストリーミング予約ユニットのスケールの変更方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

### 暗号化する資産を選択する

すべての資産を表示するには、**[設定]**、**[資産]** の順にクリックします。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### AES または DRM による暗号化

資産で **[暗号化]** を押すと、**AES** または **DRM** の 2 つの選択肢が表示されます。

#### AES

AES クリア キーによる暗号化はすべてのストリーミング プロトコル (Smooth Streaming、HLS、MPEG-DASH) で有効になります。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### DRM

[DRM] タブを選択した場合、(この時点までに構成した) 複数のコンテンツ保護ポリシーと、一連のストリーミング プロトコルが表示されます。

- **MPEG-DASH との PlayReady および Widevine** - PlayReady および Widevine DRM により MPEG-DASH ストリームが動的に暗号化されます。
- **MPEG-DASH との PlayReady および Widevine + HLS との FairPlay** - PlayReady および Widevine DRM により MPEG-DASH ストリームが動的に暗号化されます。また、FairPlay で HLS ストリームが暗号化されます。
- ** Smooth Streaming、HLS、および MPEG-DASH とのみの PlayReady** - PlayReady DRM により Smooth Streaming、HLS、MPEG-DASH ストリームが動的に暗号化されます。
- **MPEG-DASH とのみの Widevine** - Widevine DRM により MPEG-DASH が動的に暗号化されます。
- **HLS とのみの FairPlay** - FairPlay により HLS ストリームが動的に暗号化されます。

FairPlay 暗号化を有効にするには、[コンテンツ保護] 設定ブレードの FairPlay 構成オプションを使用してアプリ証明書およびアプリケーション シークレット キー (ASK) を指定する必要があります。

![コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection009.png)

暗号化を選択したら、**[適用]** を押します。

##次のステップ

Media Services のラーニング パスを確認します。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0928_2016-->