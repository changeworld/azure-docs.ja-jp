---
title: Azure Communication Services とは
description: Azure Communication Services を使用して、リアルタイム通信により充実したユーザー エクスペリエンスを実現する方法について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a006ece278e4ec750040bde5bd4b7a6144c9e720
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946305"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services とは

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Azure Communication Services を使用すると、リアルタイム マルチメディアによる IP を利用した音声、ビデオ、およびテレフォニー通信機能をアプリケーションに簡単に追加できます。 また、Communication Services クライアント ライブラリを使用すると、通信ソリューションにチャットおよび SMS 機能を追加することもできます。

Communication Services は、次のようなさまざまなシナリオで音声、ビデオ、テキスト、データ通信に使用できます。

- ブラウザー間、ブラウザーからアプリ、アプリ間の通信
- ユーザーのボットまたは他のサービスとの対話
- 公衆交換電話網を介したユーザーとボットの対話

混合シナリオはサポートされています。 たとえば、Communication Services アプリケーションでは、複数のユーザーがブラウザーや従来のテレフォニー デバイスから同時に話している場合があります。 また、Communication Services を Azure Bot Service と組み合わせて、ボットによる対話型音声応答 (IVR) システムを構築することもできます。

## <a name="common-scenarios"></a>一般的なシナリオ

Azure Communication Services を初めて使用する場合は、次のリソースから開始することをお勧めします。
<br>

| リソース                               |説明                           |
|---                                    |---                                   |
|**[Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)**|Azure Communication Services の使用を開始するには、Azure portal または Communication Services 管理クライアント ライブラリを使用して、最初の Communication Services リソースをプロビジョニングします。 Communication Services リソースの接続文字列を取得したら、最初のユーザー アクセス トークンをプロビジョニングできます。|
|**[最初のユーザー アクセス トークンを作成する](./quickstarts/access-tokens.md)**|ユーザー アクセス トークンは、Azure Communication Services リソースに対してサービスを認証するために使用されます。 これらのトークンは、Communication Services 管理クライアント ライブラリを使用してプロビジョニングされ、再発行されます。|
|**[電話番号を取得する](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services を使用して、電話番号のプロビジョニングとリリースを行うことができます。 これらの電話番号を使用すると、発信通話を開始したり、SMS 通信ソリューションを構築したりできます。|
|**[アプリから SMS を送信する](./quickstarts/telephony-sms/send.md)**|Azure Communication Services SMS クライアント ライブラリを使用すると、.NET および JavaScript アプリケーションから SMS メッセージを送受信できます。|
|**[音声およびビデオ通話の使用を開始する](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services を使用すると、通話クライアント ライブラリを使用してアプリに音声およびビデオ通話を追加できます。 このライブラリは WebRTC を使用しており、お使いのアプリケーション内でピアツーピアのマルチメディアによるリアルタイム通信を確立することができます。|
|**[チャットの使用を開始する](./quickstarts/chat/get-started.md)**|Azure Communication Services チャット クライアント ライブラリを使用すると、アプリケーションにリアルタイム チャットを統合できます。|


## <a name="samples"></a>サンプル

次のサンプルは、Azure Communication Services クライアント ライブラリの使用について詳しく示しています。 これらのサンプルを自由に使用して、独自の Communication Services ソリューションをブートストラップしてください。
<br>

| サンプル名                               | 説明                           |
|---                                    |---                                   |
|**[グループ通話のヒーロー サンプル](./samples/calling-hero-sample.md)**|Communication Services クライアント ライブラリを使用してグループ通話エクスペリエンスを構築する方法を確認してください。|
|**[グループ チャットのヒーロー サンプル](./samples/chat-hero-sample.md)**|Communication Services クライアント ライブラリを使用してグループ チャット エクスペリエンスを構築する方法を確認してください。|


## <a name="platforms-and-client-libraries"></a>プラットフォームとクライアント ライブラリ

次のリソースは、Azure Communication Services クライアント ライブラリについて学習するのに役立ちます。

| リソース                               | 説明                           |
|---                                    |---                                   |
|**[クライアント ライブラリと REST API](./concepts/sdk-options.md)**|Azure Communication Services の機能は、概念的に 6 つの分野で構成されており、それぞれがクライアント ライブラリで表されます。 リアルタイム通信のニーズに基づいて、どのクライアント ライブラリを使用するかを決定できます。|
|**[通話クライアント ライブラリの概要](./concepts/voice-video-calling/calling-sdk-features.md)**|Azure Communication Services 通話クライアント ライブラリの概要を確認します。|
|**[チャット クライアント ライブラリの概要](./concepts/chat/sdk-features.md)**|Azure Communication Services チャット クライアント ライブラリの概要を確認します。|
|**[SMS クライアント ライブラリの概要](./concepts/telephony-sms/sdk-features.md)**|Azure Communication Services SMS クライアント ライブラリの概要を確認します。|

## <a name="compare-azure-communication-services"></a>Azure Communication Services の比較

現時点では Communication Services と直接相互運用できない、利用を検討する可能性のある Microsoft 通信製品が他にも 2 つあります。

 - [Microsoft Graph Cloud Communication API シリーズ](https://docs.microsoft.com/graph/cloud-communications-concept-overview)を使用すると、組織は、M365 ライセンスを持つ Azure Active Directory ユーザーに関連付けられた通信エクスペリエンスを構築できます。 これは、Azure Active Directory に関連付けられているか Microsoft Teams で生産性を拡張したいアプリケーションに最適です。 また、[Teams のエクスペリエンス](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)でアプリケーションやカスタマイズを構築するための API もあります。

 - [Azure PlayFab Party](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) により、低遅延のチャットやデータ通信をゲームに追加することが簡単になります。 Communication Services でゲームのチャットやネットワーク システムを強化できるのに対し、PlayFab はカスタマイズされたオプションで、Xbox では無料です。


## <a name="next-steps"></a>次の手順

 - [Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)
