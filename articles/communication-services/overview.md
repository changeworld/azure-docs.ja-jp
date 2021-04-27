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
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588107"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services とは

Azure Communication Services を使用すると、リアルタイムの音声、ビデオ、テレフォニー通信をアプリケーションに簡単に追加できます。 また、Communication Services SDK を使用して、自分の通信ソリューションに SMS 機能を追加することもできます。 Azure Communication Services はユーザー情報を選ばないため、エンド ユーザーの本人確認と認証の方法を完全に自分で制御することができます。 通信のデータ プレーンまたはサービス (ボット) に対して人を結び付けることができます。

アプリケーションとして、次のものがあります。

- **企業-消費者間 (B2C)。** 企業の従業員やサービスは、カスタム ブラウザーまたはモバイル アプリケーションから、音声、ビデオ、リッチ テキストによるチャットを使用して消費者と対話します。 組織は、Azure を通じて取得した電話番号を使用して、携帯ショートメール (SMS) メッセージを送受信したり、対話型音声応答 (IVR) システムを運用したりすることができます。 [Microsoft Teams と統合](./quickstarts/voice-video-calling/get-started-teams-interop.md)すれば、従業員によってホストされた Teams 会議に消費者が参加できます。これはリモート医療やリモート バンキング、リモート製品サポートで、従業員が既に Teams に習熟しているようなシナリオに最適です。
- **消費者-消費者間。** 音声、ビデオ、リッチ テキストによるチャットを使用して、消費者と消費者が対話するための魅力的なソーシャル空間を構築します。 Azure Communication Services SDK では、あらゆるタイプのユーザー インターフェイスを構築できますが、すぐに始めることができるよう、完成したアプリケーション サンプルおよび UI アセットが用意されています。

詳細については、[Microsoft Mechanics の動画](https://www.youtube.com/watch?v=apBX7ASurgM)、または以下のリンクからアクセスできるリソースをご覧ください。

## <a name="common-scenarios"></a>一般的なシナリオ

<br>

| リソース                               |説明                           |
|---                                    |---                                   |
|**[Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)**|Azure Communication Services の使用を開始するには、Azure portal または Communication Services SDK を使用して、最初の Communication Services リソースをプロビジョニングします。 Communication Services リソースの接続文字列を取得したら、最初のユーザー アクセス トークンをプロビジョニングできます。|
|**[電話番号を取得する](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services を使用して、電話番号のプロビジョニングとリリースを行うことができます。 これらの電話番号を使用して、通話を発呼または着呼したり、SMS ソリューションを構築したりすることができます。|
|**[アプリから SMS を送信する](./quickstarts/telephony-sms/send.md)**|Azure Communication Services SMS SDK を使用すると、サービス アプリケーションから SMS メッセージを送受信できます。|

Communication Services リソースを作成した後は、音声通話やビデオ通話、テキスト チャットなどのクライアント シナリオの作成を開始できます。

| リソース                               |説明                           |
|---                                    |---                                   |
|**[最初のユーザー アクセス トークンを作成する](./quickstarts/access-tokens.md)**|ユーザー アクセス トークンは、Azure Communication Services リソースに対してクライアントを認証するために使用されます。 これらのトークンは、Communication Services SDK を使用してプロビジョニングされ、再発行されます。|
|**[音声およびビデオ通話の使用を開始する](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services を使用すると、Calling SDK を使用して自分のブラウザー アプリまたはネイティブ アプリに音声およびビデオ通話を追加できます。 |
|**[通話アプリを Teams の会議に参加させる](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services を使用すると、Microsoft Teams と対話するカスタム会議エクスペリエンスを作成できます。 Communication Services ソリューションのユーザーは、音声、ビデオ、チャット、画面共有を通じて、Teams の参加者と対話することができます。|
|**[チャットの使用を開始する](./quickstarts/chat/get-started.md)**|Azure Communication Services Chat SDK を使用して、アプリケーションにリアルタイムのリッチ テキスト チャットを追加できます。|

## <a name="samples"></a>サンプル

次のサンプルは、Azure Communication Services のエンドツーエンドでの使用を示しています。 これらのサンプルを使用して、独自の Communication Services ソリューションをブートストラップしてください。
<br>

| サンプル名                               | 説明                           |
|---                                    |---                                   |
|**[グループ通話のヒーロー サンプル](./samples/calling-hero-sample.md)**| ブラウザー、iOS、Android デバイス向けに設計された、グループ通話のアプリケーション サンプルをダウンロードします。 |
|**[グループ チャットのヒーロー サンプル](./samples/chat-hero-sample.md)**| ブラウザー向けに設計された、グループメッセージ チャットのアプリケーション サンプルをダウンロードします。 |


## <a name="platforms-and-sdk-libraries"></a>プラットフォームと SDK ライブラリ

以下のリソースでは、Azure Communication Services SDK について詳しく説明しています。 独自のクライアントを作成したい方や、インターネット経由でサービスにアクセスしたい方のために、ほとんどの機能には REST API が用意されています。

| リソース                               | 説明                           |
|---                                    |---                                   |
|**[SDK ライブラリと REST API](./concepts/sdk-options.md)**|Azure Communication Services の機能は、概念上 6 つの分野に分類されており、それぞれが SDK で表されます。 リアルタイム通信のニーズに基づいて、どの SDK ライブラリを使用するかを決定できます。|
|**[Calling SDK の概要](./concepts/voice-video-calling/calling-sdk-features.md)**|Azure Communication Services Calling SDK の概要を確認します。|
|**[Chat SDK の概要](./concepts/chat/sdk-features.md)**|Azure Communication Services Chat SDK の概要を確認します。|
|**[SMS SDK の概要](./concepts/telephony-sms/sdk-features.md)**|Azure Communication Services SMS SDK の概要を確認します。|

## <a name="other-microsoft-communication-services"></a>その他の Microsoft Communication Services

現時点では Communication Services と直接相互運用できない、使用を検討する可能性のある Microsoft 通信製品が他にも 2 つあります。

 - [Microsoft Graph Cloud Communication API シリーズ](/graph/cloud-communications-concept-overview)を使用すると、組織は、Microsoft 365 ライセンスを持つ Azure Active Directory ユーザーに関連付けられた通信エクスペリエンスを構築できます。 これは、Azure Active Directory に関連付けられているか Microsoft Teams で生産性を拡張したいアプリケーションに最適です。 また、[Teams のエクスペリエンス](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)でアプリケーションやカスタマイズを構築するための API もあります。

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) により、低遅延のチャットやデータ通信をゲームに追加することが簡単になります。 Communication Services でゲームのチャットやネットワーク システムを強化できるのに対し、PlayFab はカスタマイズされたオプションで、Xbox では無料です。


## <a name="next-steps"></a>次の手順

 - [Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)
