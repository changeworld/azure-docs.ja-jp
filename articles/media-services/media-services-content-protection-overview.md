---
title: "Azure Media Services を使用してコンテンツを保護する | Microsoft Docs"
description: "この記事では、Media Services でのコンテンツ保護の概要について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>コンテンツ保護の概要
Microsoft Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES-128)、または主要な 3 つの DRM システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) を使って、動的に暗号化したコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

次の図は、Azure Media Services でのコンテンツ保護のワークフローを示したものです。 

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

この記事では、AMS でのコンテンツ保護の理解に関する概念と用語を説明します。 また、コンテンツの保護方法が説明されている記事へのリンクも示します。 

## <a name="dynamic-encryption"></a>動的な暗号化
Azure Media Services では、AES クリア キーまたは DRM 暗号化 (Microsoft PlayReady、Google Widevine、および Apple FairPlay) を使って動的に暗号化されたコンテンツを配信できます。 現時点では、HLS、MPEG DASH、Smooth Streaming というストリーミング形式を暗号化できます。 プログレッシブ ダウンロードでの暗号化はサポートされていません。 各暗号化方法は、次のストリーミング プロトコルをサポートします。
- AES: MPEG-DASH、Smooth Streaming、HLS
- PlayReady: MPEG-DASH、Smooth Streaming、HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

資産を暗号化するには、暗号化コンテンツ キーを資産に関連付けて、キーの承認ポリシーを構成する必要があります。 コンテンツ キーは、指定することも、Media Services で自動生成することもできます。

資産の配信ポリシーを構成する必要もあります。 ストレージで暗号化された資産をストリーミングする場合は、資産の配信ポリシーを構成して、資産の配信方法を指定する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES クリア キーまたは DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーは AMS キー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 クリア キーと DRM の比較
AES 暗号化と DRM システムのどちらを使えばよいか迷うことがあります。 AES 暗号化と DRM システムの主な違いは、AES 暗号化では、コンテンツ キーが暗号化されていない形式 ("クリア形式") でクライアントに送信されることです。 その結果、コンテンツの暗号化に使われるキーを、クライアントのネットワーク トレースにおいてプレーンテキストで表示できます。 AES-128 クリア キーは、閲覧者が信頼できる相手である場合に適しています (例: 従業員が見るために社内で配信される企業ビデオの暗号化)。

PlayReady、Widevine、FairPlay はすべて、AES-128 のクリア キー暗号化より高いレベルの暗号化を提供します。 コンテンツ キーは、暗号化された形式で送信されます。 さらに、復号化はオペレーティング システム レベルのセキュリティで保護された環境で行われ、悪意のあるユーザーによる攻撃は大幅に難しくなります。 閲覧者が信頼できない可能性があり、最高レベルのセキュリティを必要とする場合は、DRM を使うことをお勧めします。

## <a name="storage-encryption"></a>ストレージ暗号化
ストレージ暗号化で AES-256 ビット暗号化を使ってクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードして、暗号化された状態でコンテンツを保存することができます。 ストレージの暗号化で保護された資産は、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに置かれます。その後、新しい出力資産として再度アップロードする前に必要に応じて再度暗号化されます。 ストレージ暗号化の主な使用事例としては、高品質の入力メディア ファイルをディスクに保存するときに強力な暗号化を使用してセキュリティを保護する場合が挙げられます。

ストレージで暗号化された資産を配信するためには、資産の配信ポリシーを構成して、コンテンツの配信方法を Media Services に指示する必要があります。 資産をストリーミングするには、事前にストリーミング サーバーで暗号化を解除し、指定された配信ポリシー (AES、共通暗号化、暗号化なしなど) を使ってコンテンツをストリーミングする必要があります。

## <a name="types-of-encryption"></a>暗号化の種類
Playready と Widevine は、共通暗号化 (AES CTR モード) を使います。 FairPlay は、AES CBC モードの暗号化を利用します。 AES-128 クリア キー暗号化は、エンベロープ暗号化を利用します。

## <a name="licenses-and-keys-delivery-service"></a>ライセンスとキーの配信サービス
Media Services は、承認されたクライアントに DRM (PlayReady、Widevine、FairPlay) ライセンスと AES キーを配信するためのキー配信サービスを提供しています。 [Azure ポータル](media-services-portal-protect-content.md)、REST API、または Media Services SDK for .NET を使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

## <a name="control-content-access"></a>コンテンツ アクセスの制御
コンテンツ キー承認ポリシーを構成することで、コンテンツにアクセスできるユーザーを制御できます。 コンテンツ キー承認ポリシーは、オープンまたはトークン制限をサポートします。

### <a name="open-authorization"></a>オープン承認
オープン承認ポリシーでは、コンテンツ キーはすべてのクライアントに (制限なしで) 送信されます。

### <a name="token-authorization"></a>トークン承認
トークン制限承認ポリシーでは、コンテンツ キーは、キー/ライセンス要求で有効な JSON Web トークン (JWT) または単純 Web トークン (SWT) を提示したクライアントのみに送信されます。 このトークンは、Secure Token Service (STS) によって発行されたものである必要があります。 Microsoft Active Directory を STS として使うことも、カスタム STS をデプロイすることもできます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 トークンが有効であり、なおかつトークンに含まれる要求とキー/ライセンスに対して構成されている要求とが一致した場合、Media Services キー配信サービスは、キー/ライセンスをクライアントに返します。

トークン制限ポリシーを構成する際は、プライマリ検証キー、発行者、対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

## <a name="streaming-urls"></a>ストリーミング URL
資産を複数の DRM を使用して暗号化した場合、ストリーミング URL で暗号化タグを使用する必要があります: (format='m3u8-aapl', encryption='xxx')。

次の考慮事項が適用されます。
* 複数の暗号化タイプを指定することはできません。
* 1 つの暗号化のみが資産に適用された場合は、暗号化タイプを URL で指定する必要はありません。
* 暗号化タイプでは大文字と小文字が区別されます。
* 指定できる暗号化タイプは次のとおりです。  
  * **cenc**: PlayReady または Widevine の場合 (共通暗号化)
  * **cbcs-aapl**: FairPlay の場合 (AES CBC 暗号化)
  * **cbc**: AES エンベロープ暗号化の場合 (エンベロープ暗号化)

## <a name="next-steps"></a>次のステップ
以下の記事では、コンテンツ保護を開始するための次の手順について説明されています。
* [ストレージ暗号化で保護する](media-services-rest-storage-encryption.md)
* [AES 暗号化で保護する](media-services-protect-with-aes128.md)
* [PlayReady や Widevine で保護する](media-services-protect-with-playready-widevine.md)
* [FairPlay で保護する](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>関連リンク
[Azure Media Services PlayReady license delivery pricing explained (Azure Media Services PlayReady のライセンス配信料金の説明)](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[How to debug for AES encrypted stream in Azure Media Services (Azure Media Services で AES 暗号化ストリームをデバッグする方法)](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT トークンの承認](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT 要求に基づいてコンテンツ キーの配信を制限する](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
