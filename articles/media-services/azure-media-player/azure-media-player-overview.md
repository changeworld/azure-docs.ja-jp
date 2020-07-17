---
title: Azure Media Player の概要
description: Azure Media Player について詳しく説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727559"
---
# <a name="azure-media-player-overview"></a>Azure Media Player の概要 #

Azure Media Player は、さまざまなブラウザーやデバイス上で [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) からメディア コンテンツを再生できる組み込みの Web ビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。  デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用する再生テクノロジにかかわらず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。  これにより、Azure Media Services で提供されるコンテンツを、さまざまなデバイスやブラウザーで追加作業なしで再生できるようになります。

Microsoft Azure Media Services では、DASH、スムーズ ストリーミング、HLS ストリーミングなどの形式でコンテンツを提供して再生できます。 Azure Media Player はこれらの多用な形式を考慮し、プラットフォームやブラウザーの性能に基づいて最適なリンクを自動的に再生します。 また、Microsoft Azure Media Services では Common Encryption (PlayReady または Widevine) や AES-128 ビットエンベロープ暗号化を使用した資産の動的暗号化も可能です。 Azure Media Player では、コンテンツが適切に構成されていれば、PlayReady 暗号化や AES-128 ビットエンベロープ暗号化されたコンテンツの暗号化を解除できます。  その構成方法については、「[保護されたコンテンツ](azure-media-player-protected-content.md)」セクションを参照してください。

新機能のリクエスト、アイデアの提供、またはフィードバックは、[Azure Media Player の UserVoice](https://aka.ms/ampuservoice) までお送りください。 特定の問題や質問がある場合、またはバグを発見した場合は、ampinfo@microsoft.com にご連絡ください。

リリース情報を見逃さないよう[サインアップ](https://aka.ms/ampsignup)をお願いいたします。Azure Media Player の最新情報をお届けします。

> [!NOTE]
> Azure Media Player でサポートされるのは、Azure Media Services からのメディア ストリームのみであることに注意してください。

## <a name="license"></a>ライセンス ##

Azure Media Player は、Azure Media Player の Microsoft ソフトウェア ライセンス条項に記載された規定に従うことを条件としてライセンスされます。 [ライセンス ファイル](azure-media-player-license.md)で全条項をご覧いただけます。 その他の情報については、[プライバシーに関する声明](https://www.microsoft.com/en-us/privacystatement/default.aspx)を参照してください。

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)