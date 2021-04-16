---
title: Azure Communication Services での音声とビデオの概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services の通話の種類について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8a25f69019e194650bb6aa2f5b8ae19dd37fbc48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729169"
---
# <a name="voice-and-video-concepts"></a>音声とビデオの概念

Azure Communication Services を使用すると、一対一またはグループでの音声通話やビデオ通話をかけたり受けたりすることができます。 通話は、インターネットに接続されている他のデバイスや、普通の従来の電話に対しても行うことができます。 Communication Services の JavaScript、Android、または iOS SDK を使用して、ユーザーが個人的な会話やグループ ディスカッションで相互に対話できるアプリケーションを構築できます。 Azure Communication Services は、サービスやボットとの通話をサポートしています。

## <a name="call-types-in-azure-communication-services"></a>Azure Communication Services での通話の種類

Azure Communication Services では、複数の種類の通話を行うことができます。 行う通話の種類によって、信号スキーマ、メディア トラフィック フロー、価格モデルが決まります。

### <a name="voice-over-ip-voip"></a>ボイス オーバー IP 通話 (VoIP)

お使いのアプリケーションのユーザーがインターネットまたはデータ接続を使用してそのアプリケーションの別のユーザーを呼び出すと、ボイス オーバー IP 通話 (VoIP) を介して通話が行われます。 この場合、信号とメディアの両方がインターネットを介して流れます。

### <a name="public-switched-telephone-network-pstn"></a>公衆交換電話網 (PSTN)

ユーザーが従来の電話番号を使用して対話すると、通話は常に PSTN (公衆交換電話網) 音声通話によって行われます。 PSTN 通話を発信および受信するには、Azure Communication Services リソースにテレフォニー機能を追加する必要があります。 この場合、信号とメディアでは、ユーザーを接続するために、IP ベースおよび PSTN ベースのテクノロジが組み合わされて使用されます。

### <a name="one-to-one-call"></a>一対一の通話

Azure Communication Services での一対一の通話は、いずれかのユーザーがいずれかの SDK を使用して別のユーザーに接続したときに行われます。 この通話は、VoIP または PSTN のいずれかです。

### <a name="group-call"></a>グループ通話

Azure Communication Services でのグループ通話は、3 名以上の参加者が相互に接続したときに行われます。 VoIP および PSTN で接続されたユーザーの任意の組み合わせで、グループ通話に参加することができます。 一対一の通話をグループ通話に変換するには、通話に参加者を追加します。 これらの参加者のいずれかをボットにすることができます。

### <a name="supported-video-standards"></a>サポートされるビデオ標準
H.264 (MPEG-4) がサポートされます。

### <a name="video-quality"></a>ビデオ品質
ネイティブ (iOS、Android) SDK では、フル HD 1080p までサポートされます。 Web (JS) SDK では、標準 HD 720p がサポートされます。 品質は、使用可能な帯域幅によって異なります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話を開始する](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

詳細については、次の記事を参照してください。
- 一般的な[通話のフロー](../call-flows.md)を理解する
- [電話番号の種類](../telephony-sms/plan-solution.md)
- [Calling SDK の機能](../voice-video-calling/calling-sdk-features.md) について確認する
