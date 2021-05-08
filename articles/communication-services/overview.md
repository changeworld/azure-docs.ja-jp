---
title: Azure Communication Services とは
description: Azure Communication Services を使用して、リアルタイム通信により充実したユーザー エクスペリエンスを実現する方法について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0efdf48e78d0cc48e288bea354f5de5f9635c760
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106842"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services とは

> [!IMPORTANT]
> Azure Communication Services を使用して作成したアプリケーションは、Microsoft Teams と通信を行うことができます。 詳細については、[Teams の相互運用](./quickstarts/voice-video-calling/get-started-teams-interop.md)に関するドキュメントを参照してください。


Azure Communication Services を使用すると、リアルタイム マルチメディアによる IP を利用した音声、ビデオ、およびテレフォニー通信機能をアプリケーションに簡単に追加できます。 また、Communication Services SDK ライブラリを使用すると、自分の通信ソリューションにチャットおよび SMS 機能を追加することもできます。

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Communication Services は、次のようなさまざまなシナリオで音声、ビデオ、テキスト、データ通信に使用できます。

- ブラウザー間、ブラウザーからアプリ、アプリ間の通信
- ユーザーのボットまたは他のサービスとの対話
- 公衆交換電話網を介したユーザーとボットの対話

混合シナリオはサポートされています。 たとえば、Communication Services アプリケーションでは、複数のユーザーがブラウザーや従来のテレフォニー デバイスから同時に話している場合があります。 また、Communication Services を Azure Bot Service と組み合わせて、ボットによる対話型音声応答 (IVR) システムを構築することもできます。

## <a name="common-scenarios"></a>一般的なシナリオ

Azure Communication Services の使用を開始するには、次のリソースがお勧めです。
<br>

| リソース                               |説明                           |
|---                                    |---                                   |
|**[Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)**|Azure Communication Services の使用を開始するには、Azure portal または Communication Services SDK を使用して、最初の Communication Services リソースをプロビジョニングします。 Communication Services リソースの接続文字列を取得したら、最初のユーザー アクセス トークンをプロビジョニングできます。|
|**[電話番号を取得する](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services を使用して、電話番号のプロビジョニングとリリースを行うことができます。 これらの電話番号を使用すると、発信通話を開始したり、SMS 通信ソリューションを構築したりできます。|

Communication Services リソースを作成した後は、音声通話やビデオ通話、テキスト チャットなどのクライアント シナリオの作成を開始できます。

| リソース                               |説明                           |
|---                                    |---                                   |
|**[最初のユーザー アクセス トークンを作成する](./quickstarts/access-tokens.md)**|ユーザー アクセス トークンは、Azure Communication Services リソースに対してサービスを認証するために使用されます。 これらのトークンは、Communication Services SDK を使用してプロビジョニングされ、再発行されます。|
|**[音声およびビデオ通話の使用を開始する](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services を使用すると、Calling SDK を使用して自分のアプリに音声およびビデオ通話を追加できます。 このライブラリは WebRTC を使用しており、お使いのアプリケーション内でピアツーピアのマルチメディアによるリアルタイム通信を確立することができます。|
|**[通話アプリを Teams の会議に参加させる](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services を使用すると、Microsoft Teams と対話するカスタム会議エクスペリエンスを作成できます。 Communication Services ソリューションのユーザーは、音声、ビデオ、チャット、画面共有を通じて、Teams の参加者と対話することができます。|
|**[チャットの使用を開始する](./quickstarts/chat/get-started.md)**|Azure Communication Services Chat SDK を使用すると、自分のアプリケーションにリアルタイム チャットを統合できます。|
|**[アプリから SMS を送信する](./quickstarts/telephony-sms/send.md)**|Azure Communication Services SMS SDK を使用すると、自分の .NET および JavaScript アプリケーションから SMS メッセージを送受信できます。|

## <a name="samples"></a>サンプル

次のサンプルは、Azure Communication Services SDK ライブラリのエンドツーエンドでの使用を示しています。 これらのサンプルを自由に使用して、独自の Communication Services ソリューションをブートストラップしてください。
<br>

| サンプル名                               | 説明                           |
|---                                    |---                                   |
|**[グループ通話のヒーロー サンプル](./samples/calling-hero-sample.md)**|Communication Services SDK ライブラリを使用してグループ通話エクスペリエンスを構築する方法を確認してください。|
|**[グループ チャットのヒーロー サンプル](./samples/chat-hero-sample.md)**|Communication Services SDK ライブラリを使用してグループ チャット エクスペリエンスを構築する方法を確認してください。|


## <a name="platforms-and-sdk-libraries"></a>プラットフォームと SDK ライブラリ

次のリソースは、Azure Communication Services SDK ライブラリについて学習するのに役立ちます。

| リソース                               | 説明                           |
|---                                    |---                                   |
|**[SDK ライブラリと REST API](./concepts/sdk-options.md)**|Azure Communication Services の機能は、概念上 6 つの分野に分類されており、それぞれが SDK で表されます。 リアルタイム通信のニーズに基づいて、どの SDK ライブラリを使用するかを決定できます。|
|**[Calling SDK の概要](./concepts/voice-video-calling/calling-sdk-features.md)**|Azure Communication Services Calling SDK の概要を確認します。|
|**[Chat SDK の概要](./concepts/chat/sdk-features.md)**|Azure Communication Services Chat SDK の概要を確認します。|
|**[SMS SDK の概要](./concepts/telephony-sms/sdk-features.md)**|Azure Communication Services SMS SDK の概要を確認します。|

## <a name="other-microsoft-communication-services"></a>その他の Microsoft Communication Services

現時点では Communication Services と直接相互運用できない、利用を検討する可能性のある Microsoft 通信製品が他にも 2 つあります。

 - [Microsoft Graph Cloud Communication API シリーズ](/graph/cloud-communications-concept-overview)を使用すると、組織は、Microsoft 365 ライセンスを持つ Azure Active Directory ユーザーに関連付けられた通信エクスペリエンスを構築できます。 これは、Azure Active Directory に関連付けられているか Microsoft Teams で生産性を拡張したいアプリケーションに最適です。 また、[Teams のエクスペリエンス](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)でアプリケーションやカスタマイズを構築するための API もあります。

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) により、低遅延のチャットやデータ通信をゲームに追加することが簡単になります。 Communication Services でゲームのチャットやネットワーク システムを強化できるのに対し、PlayFab はカスタマイズされたオプションで、Xbox では無料です。


## <a name="next-steps"></a>次の手順

 - [Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)
