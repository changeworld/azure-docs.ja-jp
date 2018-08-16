---
title: Azure Media Services を使用してコンテンツを保護する | Microsoft Docs
description: この記事では、Media Services でのコンテンツ保護の概要について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: juliako
ms.openlocfilehash: 2a3e94d37a926bd36b780b45eb3d6cb29fb73597
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521756"
---
# <a name="content-protection-overview"></a>コンテンツ保護の概要

Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

次の図は、Media Services でのコンテンツ保護のワークフローを示しています。 

![コンテンツの保護](./media/content-protection/content-protection.png)

&#42; *動的暗号化は、AES-128 "クリア キー"、CBCS、および CENC をサポートします。詳細については、[こちら](#streaming-protocols-and-encryption-types)のサポート マトリックスを参照してください。*

この記事では、Media Services でのコンテンツ保護の理解に関連する概念と用語について説明します。 この記事には「[よく寄せられる質問](#faq)」セクションがあります。また、コンテンツの保護方法が説明されている記事へのリンクも示します。 

## <a name="main-components-of-the-content-protection-system"></a>コンテンツ保護システムの主要コンポーネント

"コンテンツ保護" システム/アプリケーションの設計を適切に完了するには、作業の範囲をよく理解する必要があります。 以下の一覧に、実装する必要のある 3 つの部分の概要を示します。 

1. Azure Media Services コード
  
  * PlayReady、Widevine、FairPlay のライセンス テンプレート。 テンプレートを使用すると、使用する各 DRM の権利とアクセス許可を構成できます
  * JWT の要求に基づいて承認チェックのロジックを指定するライセンス配信の承認
  * コンテンツ キー、ストリーミング プロトコル、DRM 暗号化を定義することによる対応する DRM の適用

  > [!NOTE]
  > 複数の暗号化の種類 (AES-128、PlayReady、Widevine、FairPlay) を使用して各資産を暗号化することができます。 合理的な組み合わせについては、「[ストリーミング プロトコルと暗号化の種類](#streaming-protocols-and-encryption-types)」を参照してください。
  
  AES または DRM を使用してコンテンツを暗号化する手順については、次の記事を参照してください。 
  
  * [AES 暗号化で保護する](protect-with-aes128.md)
  * [DRM での保護](protect-with-drm.md)

2. AES または DRM クライアントを使用するプレーヤー。 プレーヤー SDK (ネイティブまたはブラウザー ベース) に基づくビデオ プレーヤー アプリは、次の要件を満たす必要があります。
  * プレーヤー SDK は必要な DRM クライアントをサポートしている
  * プレーヤ SDK は、Smooth、DASH、または HLS の必要なストリーミング プロトコルをサポートしている
  * プレーヤー SDK は、ライセンス取得要求で JWT トークンを渡す処理に対応できる必要がある
  
    [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/) を使ってプレーヤーを作成できます。 異なる DRM プラットフォームで使う DRM テクノロジを指定するには、[Azure Media Player の ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) を使います。

    AES または CENC (Widevine および PlayReady) で暗号化されたコンテンツをテストするには、[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) を使用できます。 必ず [詳細オプション] をクリックし、暗号化オプションを確認します。

    FairPlay で暗号化されたコンテンツをテストする場合は、[このテストプレイヤー](http://aka.ms/amtest)を使用してください。 プレーヤーは、Widevine、PlayReady、FairPlay DRM、および AES-128 クリア キー暗号化をサポートしています。 Chrome/Opera/Firefox for Widevine、MS Edge/IE11 for PlayReady、Safari for macOS for FairPlay などのさまざまな DRM をテストするには、適切なブラウザーを選択する必要があります。

3. バックエンド リソース アクセス用のアクセス トークンとして JSON Web トークン (JWT) を発行するセキュア トークン サービス (STS)。 バックエンド リソースとして AMS ライセンス配信サービスを使用することができます。 STS は以下を定義する必要があります。

  * 発行者と対象ユーザー (または範囲)
  * 要求 (コンテンツ保護におけるビジネス要件によって変わります)
  * 署名の検証のための対称または非対称検証
  * キー ロールオーバーのサポート (必要な場合)

    [この STS ツール](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) を使用して、3 種類の検証キー (対称、非対称、またはキー ロールオーバーありの AAD) をすべてサポートする STS をテストできます。 

> [!NOTE]
> 次のパートに移動する前に、各パート (前述) に集中して完全にテストすることを強くお勧めします。 "コンテンツ保護" システムをテストするには、前述の一覧で指定されているツールを使用してください。  

## <a name="streaming-protocols-and-encryption-types"></a>ストリーミング プロトコルと暗号化の種類

Media Services を使用すると、AES クリアキーまたは DRM 暗号化 (PlayReady、Widevine、または FairPlay を使用) によって動的に暗号化されたコンテンツを配信できます。 現時点では、暗号化できる形式は、HTTP ライブ ストリーミング (HLS)、MPEG DASH、および Smooth Streaming です。 各プロトコルは、次の暗号化方式をサポートしています。

|プロトコル|コンテナー形式|暗号化スキーム|
|---|---|---|---|
|MPEG-DASH|All|AES|
||CSF (fmp4) |CENC (Widevine + PlayReady) |
||CMAF (fmp4)|CENC (Widevine + PlayReady)|
|HLS|All|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF (fmp4) |CENC (PlayReady) |
|スムーズ ストリーミング|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>動的な暗号化

Media Services v3 では、コンテンツ キーが StreamingLocator に関連付けられています ([この例](protect-with-aes128.md)を参照)。 Media Services キー配信サービスを使用している場合は、コンテンツ キーを自動生成する必要があります。 独自のキー配信サービスを使用している場合、または 2 つのデータセンターに同じコンテンツ キーが必要な高可用性シナリオに対応する必要がある場合は、コンテンツ キーを手動で生成する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES クリア キーまたは DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するために、プレーヤーは Media Services のキー配信サービスまたは指定したキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定したコンテンツ キー ポリシーを評価します。

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 クリア キーと DRM

AES 暗号化と DRM システムのどちらを使えばよいか迷うことがあります。 2 つのシステムの主な違いは、AES 暗号化ではコンテンツ キーが暗号化されない形式 ("平文") でクライアントに送信されることです。 その結果、コンテンツの暗号化に使用されるキーを、クライアントのネットワーク トレースでプレーンテキストとして表示できます。 AES-128 クリア キー暗号化は、閲覧者が信頼できる相手である場合 (従業員が見るために社内で配信される企業ビデオの暗号化など) に適しています。

PlayReady、Widevine、FairPlay はすべて、AES-128 のクリア キー暗号化より高いレベルの暗号化を提供します。 コンテンツ キーは、暗号化された形式で送信されます。 さらに、解読はオペレーティング システム レベルのセキュリティで保護された環境で行われるため、悪意のあるユーザーによる攻撃はより難しくなります。 閲覧者が信頼できない可能性があり、最高レベルのセキュリティを必要とするユース ケースでは、DRM をお勧めします。

## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services v3 でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化| AES-256 暗号化、Media Services によって管理されるキー|サポートされていません<sup>(1)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|

<sup>1</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="licenses-and-keys-delivery-service"></a>ライセンスとキーの配信サービス

Media Services は、承認されたクライアントに DRM (PlayReady、Widevine、FairPlay) ライセンスと AES キーを配信するためのキー配信サービスを提供しています。 REST API、または Media Services クライアント ライブラリを使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

## <a name="control-content-access"></a>コンテンツ アクセスの制御

コンテンツ キー ポリシーを構成することで、コンテンツにアクセスできるユーザーを制御できます。 Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー ポリシーを構成する必要があります。 キーをクライアント (プレーヤー) に配信するには、クライアントがポリシーの要件を満たしている必要があります。 コンテンツ キー ポリシーは、**オープン**または**トークン**の制限を持つことができます。 

トークン制限コンテンツ キー ポリシーでは、コンテンツ キーは、キー/ライセンス要求で有効な JSON Web トークン (JWT) または単純 Web トークン (SWT) を提示したクライアントにのみ送信されます。 このトークンは、セキュリティ トークン サービス (STS) によって発行されたものでなければなりません。 Azure Active Directory を STS として使用することも、カスタム STS をデプロイすることもできます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 トークンが有効であり、なおかつトークンに含まれるクレームとキー/ライセンスに対して構成されている要求とが一致した場合、Media Services キー配信サービスは要求されたキー/ライセンスをクライアントに返します。

トークン制限ポリシーを構成する際には、プライマリ検証キー、発行者、および対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれています。 発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>よく寄せられる質問

### <a name="question"></a>質問

Azure Media Services (AMS) v3 を使用してマルチ DRM (PlayReady、Widevine、FairPlay) システムを実装する方法と、AMS ライセンス/キー配信サービスを使用方法について教えてください。

### <a name="answer"></a>Answer

エンドツーエンドのシナリオについては、[次のコード例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)を参照してください。 

この例では、次のことを行っています。

1. ContentKeyPolicies を作成して構成します。

  このサンプルには、[PlayReady](playready-license-template-overview.md)、[Widevine](widevine-license-template-overview.md)、および [FairPlay](fairplay-license-overview.md) ライセンスを構成する機能が含まれています。

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. 暗号化された資産をストリーミングするように構成された StreamingLocator を作成します。 

  この例の場合、**StreamingPolicyName** を **PredefinedStreamingPolicy.SecureStreaming** に設定し、エンベロープと cenc の暗号化をサポートし、StreamingLocator に 2 つのコンテンツ キーを設定します。 

  また、FairPlay を使用して暗号化する場合は、**StreamingPolicyName** を **PredefinedStreamingPolicy.SecureStreamingWithFairPlay** に設定します。

3. テスト トークンを作成します。

  **GetTokenAsync** メソッドは、テスト トークンの作成方法を示しています。
  
4. ストリーミング URL を構築します。

  **GetDASHStreamingUrlAsync** メソッドは、ストリーミング URL を構築する方法を示しています。 この場合、URL は **DASH** コンテンツをストリームします。

### <a name="question"></a>質問

JWT トークンを使用してライセンスまたはキーを要求する前に、JWT トークンを入手する方法と入手できる場所を教えてください。

### <a name="answer"></a>Answer

1. 運用環境では、HTTPS 要求時に JWT トークンを発行する Secure Token Services (STS) (Web サービス) が必要です。 テスト環境では、[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) で定義されている **GetTokenAsync** メソッドに示されているコードを使用できます。
2. プレーヤーは、ユーザーが認証された後、そのようなトークンの要求を STS に対して作成し、それをトークンの値として割り当てる必要があります。 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使用できます。

* 対称キーと非対称キーのどちらかを使用して STS を実行する例については、[http://aka.ms/jwt](http://aka.ms/jwt) を参照してください。 
* このような JWT トークンを使用する Azure Media Player に基づくプレーヤーの例については、[http://aka.ms/amtest](http://aka.ms/amtest) を参照してください ("player_settings" リンクを展開すると、トークンの入力が表示されます)。

### <a name="question"></a>質問

AES 暗号化を使用してビデオをストリームする要求を承認する方法を教えてください。

### <a name="answer"></a>Answer

正しいアプローチは、STS (セキュリティ トークン サービス) を活用することです。

STS では、ユーザー プロファイルに応じて、異なる要求 ("Premium ユーザー"、"Basic ユーザー"、"無料試用ユーザー" など) を追加します。 JWT の要求に応じて、ユーザーには異なるコンテンツが表示されます。 当然ながら、異なるコンテンツ/資産の場合、ContentKeyPolicyRestriction には対応する RequiredClaims があります。

Azure Media Services API を使用して、ライセンス/キー配信の構成と資産の暗号化を行います ([このサンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)を参照してください)。

## <a name="next-steps"></a>次の手順

次の記事を参照してください。

  * [AES 暗号化で保護する](protect-with-aes128.md)
  * [DRM での保護](protect-with-drm.md)

詳細については、[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)に関するページを参照してください。


