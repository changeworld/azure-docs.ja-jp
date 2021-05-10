---
title: Azure Media Player の概要
description: Azure Media Player について詳しく説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: ec36f6bd19728d47b73a0186fb1fb192ba57f4cf
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449819"
---
# <a name="azure-media-player-overview"></a>Azure Media Player の概要 #

Azure Media Player は、さまざまなブラウザーやデバイス上で [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) からメディア コンテンツを再生する Web ビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。  デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用する再生テクノロジにかかわらず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできるため、Azure Media Services で提供されるコンテンツは、特別な手間を掛けなくても、さまざまなデバイスやブラウザーで再生できるようになります。

Microsoft Azure Media Services では、DASH、スムーズ ストリーミング、HLS ストリーミングなどの形式でコンテンツを提供して再生できます。 Azure Media Player はこれらの多用な形式を考慮し、プラットフォームやブラウザーの性能に基づいて最適なリンクを自動的に再生します。 また、Microsoft Azure Media Services では Common Encryption (PlayReady または Widevine) や AES-128 ビットエンベロープ暗号化を使用した資産の動的暗号化も可能です。 Azure Media Player では、コンテンツが適切に構成されていれば、PlayReady 暗号化や AES-128 ビットエンベロープ暗号化されたコンテンツの暗号化を解除できます。  プレーヤーの構成方法については、「[保護されたコンテンツ](azure-media-player-protected-content.md)」セクションを参照してください。

新機能のリクエスト、アイデアの提供、またはフィードバックは、[Azure Media Player の UserVoice](https://aka.ms/ampuservoice) までお送りください。 特定の問題や質問がある場合、またはバグを発見した場合は、ampinfo@microsoft.com にご連絡ください。

> [!NOTE]
> Azure Media Player でサポートされるのは、Azure Media Services からのメディア ストリームのみであることに注意してください。

## <a name="license"></a>ライセンス ##

Azure Media Player は、Azure Media Player の Microsoft ソフトウェア ライセンス条項に記載された規定に従うことを条件としてライセンスされます。 [ライセンス ファイル](/legal/azure-media-player/azure-media-player-license)で全条項をご覧いただけます。 詳細については、[プライバシーに関する声明](https://www.microsoft.com/en-us/privacystatement/default.aspx)を参照してください。

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
