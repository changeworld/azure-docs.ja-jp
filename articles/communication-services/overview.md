---
title: Azure Communication Services とは
description: Azure Communication Services を使用して、リアルタイム通信により充実したユーザー エクスペリエンスを実現する方法について説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5e6716980ef8f813be49a7cd6d269d432114aeae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671946"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services とは

Azure Communication Services はクラウドベースのサービスです。用意されている REST API およびクライアント ライブラリ SDK を利用することで、通信を手軽にアプリケーションに組み込むことができます。 通信技術 (メディアのエンコードやリアルタイム ネットワークなど) の専門家でなくても、アプリケーションに通信機能を追加できます。 この機能は、Azure for Government でもサポートされています。

Azure Communication Services では、さまざまな通信形式がサポートされています。

1. 音声およびビデオによる通話
1. リッチ テキスト チャット
1. sms

カスタム クライアント エンドポイント、カスタム サービス、公衆交換電話網 (PSTN) を通信アプリケーションに接続することができます。 電話番号は、Azure Communication Services REST API、SDK、または Azure portal を通じて直接取得でき、これらの番号を SMS や通話アプリケーションに使用できます。 Azure Communication Services ダイレクト ルーティングを使用すると、SIP およびセッション ボーダー コントローラーを使用して、独自の PSTN 通信事業者に接続し、独自の電話番号を持ち込むことができます。

REST API に加えて、[Azure Communication Services クライアント ライブラリ](./concepts/sdk-options.md)は、さまざまなプラットフォームと言語で利用できます (Web ブラウザー (JavaScript)、iOS (Swift)、Java (Android)、Windows (.NET) など)。 [Web ブラウザーの UI ライブラリ](https://aka.ms/acsstorybook)を使用すると、モバイルおよびデスクトップ ブラウザーの開発を加速させることができます。 Azure Communication Services は ID に依存しないため、お客様はエンド ユーザーの本人確認と認証の方法を自分で制御することができます。

Azure Communication Services のシナリオは次のとおりです。

- **企業-消費者間 (B2C)。** 企業の従業員やサービスは、カスタム ブラウザーまたはモバイル アプリケーションから、音声、ビデオ、リッチ テキストによるチャットを使用してコンシューマーとやりとりします。 組織は、Azure を通じて取得した電話番号を使用して、携帯ショートメール (SMS) メッセージを送受信したり、[対話型音声応答 (IVR) システムを運用](https://github.com/microsoft/botframework-telephony/blob/main/EnableTelephony.md)したりすることができます。 [Microsoft Teams との統合](./quickstarts/voice-video-calling/get-started-teams-interop.md)を使用すれば、従業員によってホストされた Teams 会議にコンシューマーを接続できます。これはリモート医療やリモート バンキング、リモート製品サポートで、従業員が既に Teams に習熟しているようなシナリオに最適です。
- **コンシューマー to コンシューマー (C2C)。** 音声、ビデオ、リッチ テキストによるチャットを使用して、消費者と消費者が対話するための魅力的なソーシャル空間を構築します。 任意の種類のユーザー インターフェイスを Azure Communication Services SDK で構築することも、完全なアプリケーション サンプルとオープンソースの UI ツールキットを使用して楽にすばやく開始することもできます。

詳細については、[Microsoft Mechanics の動画](https://www.youtube.com/watch?v=apBX7ASurgM)、または以下のリンクからアクセスできるリソースをご覧ください。

## <a name="common-scenarios"></a>一般的なシナリオ

<br>

| リソース                               |説明                           |
|---                                    |---                                   |
|**[Communication Services リソースを作成する](./quickstarts/create-communication-resource.md)**|Azure Communication Services の使用を開始するには、Azure portal または Communication Services SDK を使用して、最初の Communication Services リソースをプロビジョニングします。 Communication Services リソースの接続文字列を取得したら、最初のユーザー アクセス トークンをプロビジョニングできます。|
|**[電話番号を取得する](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services を使用して、電話番号のプロビジョニングとリリースを行います。 これらの電話番号を使用して、通話を発呼または着呼したり、SMS ソリューションを構築したりすることができます。|
|**[アプリから SMS を送信する](./quickstarts/telephony-sms/send.md)**| Azure Communication Services SMS REST API および SDK を使用すると、サービス アプリケーションから SMS メッセージを送受信できます。|

Communication Services リソースを作成した後は、音声通話やビデオ通話、テキスト チャットなどのクライアント シナリオの作成を開始できます。

| リソース                               |説明                           |
|---                                    |---                                   |
|**[最初のユーザー アクセス トークンを作成する](./quickstarts/access-tokens.md)**|ユーザー アクセス トークンでは、Azure Communication Services リソースに対してクライアントを認証します。 これらのトークンは、Communication Services Identity API および SDK を使用してプロビジョニングされ、再発行されます。|
|**[音声およびビデオ通話の使用を開始する](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services を使用すると、Calling SDK を使用して自分のブラウザー アプリまたはネイティブ アプリに音声およびビデオ通話を追加できます。 |
|**[テレフォニー通話をアプリに追加する](./quickstarts/voice-video-calling/pstn-call.md)**|Azure Communication Services を使用すると、アプリケーションにテレフォニー通話機能を追加できます。|
|**[通話アプリを Teams の会議に参加させる](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services を使用すると、Microsoft Teams と対話するカスタム会議エクスペリエンスを作成できます。 Communication Services ソリューションのユーザーは、音声、ビデオ、チャット、画面共有を通じて、Teams の参加者と対話することができます。|
|**[チャットの使用を開始する](./quickstarts/chat/get-started.md)**|Azure Communication Services Chat SDK を使用して、アプリケーションにリアルタイムのリッチ テキスト チャットを追加できます。|
|**[Microsoft Bot を電話番号に接続する](https://github.com/microsoft/botframework-telephony)**|テレフォニー チャネルは Microsoft Bot Framework 内のチャネルです。これにより、電話を介してボットとユーザーのやりとりを実現することができます。 この場合、Microsoft Bot Framework の機能と Azure Communication Services および Azure Speech Services を組み合わせて活用します。  |


## <a name="samples"></a>サンプル

次のサンプルは、Azure Communication Services のエンドツーエンドでの使用を示しています。 これらのサンプルを使用して、独自の Communication Services ソリューションをブートストラップしてください。
<br>

| サンプル名                               | 説明                           |
|---                                    |---                                   |
|**[グループ通話のヒーロー サンプル](./samples/calling-hero-sample.md)**| ブラウザー、iOS、Android デバイス向けに設計された、グループ通話のアプリケーション サンプルをダウンロードします。 |
|**[グループ チャットのヒーロー サンプル](./samples/chat-hero-sample.md)**| ブラウザー向けに設計された、グループメッセージ チャットのアプリケーション サンプルをダウンロードします。 |
|**[Web 通話のサンプル](./samples/web-calling-sample.md)**| 音声、ビデオ、および PSTN 通話向けに設計された Web アプリケーション サンプルをダウンロードします。 |


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
