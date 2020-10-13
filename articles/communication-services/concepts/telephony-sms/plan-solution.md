---
title: Azure Communication Services のテレフォニーと SMS ソリューションを計画する
titleSuffix: An Azure Communication Services concept document
description: 電話番号とテレフォニーの使用を効果的に計画する方法について説明します。
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 6a63df282cadf86668e69d2422a6c791e86010b6
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767136"
---
# <a name="plan-your-telephony-and-sms-solution"></a>テレフォニーと SMS ソリューションを計画する

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Azure Communication Services では、公衆交換電話網 (PSTN) で、電話番号を使用して音声通話を行ったり携帯ショートメール (SMS) メッセージを送信したりすることができます。 このドキュメントでは、Communication Services を使用したテレフォニーと SMS ソリューションを計画している方のために、電話番号の種類、プラン、利用可能なリージョンについて見ていきます。

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]


## <a name="phone-number-types-in-azure-communication-services"></a>Azure Communication Services での電話番号の種類
 
Communication Services には、**ローカル**と**無料電話番号**の 2 種類の電話番号が用意されています。 

### <a name="local-numbers"></a>ローカル番号
ローカル (固定) 番号は、米国内のローカルの市外局番から成る 10 桁の電話番号です。 たとえば `+1 (206) XXX-XXXX` は、`206` を市外局番とするローカル番号です。 この市外局番は、シアトル市に割り当てられています。 一般に、これらの電話番号は個人や地元企業によって使用されます。 Azure Communication Services は、米国内のローカル番号を提供しています。 これらの番号を使用して電話をかけることはできますが、携帯ショートメール (SMS) メッセージを送信することはできません。 

### <a name="toll-free-numbers"></a>無料電話番号
無料電話番号は、特殊な市外局番を含んだ 10 桁の電話番号です。あらゆる電話番号から無料で通話することができます。 たとえば `+1 (800) XXX-XXXX` は、北米地域の無料電話番号です。 通常、これらの電話番号は顧客サービスの目的で使用されます。 Azure Communication Services は、米国内の無料電話番号を提供しています。 これらの番号を使用して電話をかけたり、携帯ショートメール (SMS) メッセージを送信したりすることができます。 無料電話番号はアプリケーションにのみ割り当てることができ、人が使うことはできません。

#### <a name="choosing-a-phone-number-type"></a>電話番号の種類を選択する

ご利用の電話番号がアプリケーションによって使用される場合 (サービスに必要な電話をかける、メッセージを送信するなど)、無料電話番号またはローカル (固定) 番号を選択することができます。 アプリケーションから SMS メッセージを送信したり電話をかけたりする場合は、無料電話番号を選択できます。

ご利用の電話番号が人 (呼び出し元のアプリケーションのユーザーなど) によって使用される場合、ローカル (固定) 電話番号を使用する必要があります。 

次の表は、これらの電話番号の種類をまとめたものです。 

| 電話番号の種類 | 例                              | 利用可能な国    | 電話番号の機能 |一般的なユース ケース                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| ローカル (固定)        | +1 (ローカルの市外局番) XXX XX XX  | US                      | 通話 (発信) | アプリケーションのユーザーに電話番号を割り当てる  |
| フリーダイヤル         | +1 (フリーダイヤルの市外 "*局番*") XXX XX XX | US                      | 通話 (発信)、SMS (受信または送信)| 音声自動応答 (IVR) システム (ボット) や SMS アプリケーションに電話番号を割り当てる                                        |


## <a name="phone-number-plans-in-azure-communication-services"></a>Azure Communication Services における電話番号プラン 

ほとんどの電話番号で、"アラカルト" のプラン セットを構成できます。 開発者の中には、発信通話プランのみを必要とする人もいれば、発信通話と送信 SMS のプランを選択する人もいます。 これらのプランは、Azure Communication Services 内で電話番号をリースするときに選択できます。

利用できるプランは、運用拠点となる国やユース ケース、選択した電話番号の種類によって異なります。 これらのプランは、規制要件のために、国によって異なります。 Azure Communication Services には、次のプランが用意されています。

- **一方向の送信 SMS** このプランでは、携帯ショートメール (SMS) メッセージをユーザーに送信できます。 このプランは、通知や 2 要素認証のアラートなどのシナリオで活用できます。 
- **双方向の受信および送信 SMS** このプランでは、電話番号を使用しているユーザーとの間でメッセージを送受信できます。 このプランは、顧客サービスのシナリオで活用できます。
- **一方向の発信通話** このプランでは、ユーザーに電話をかけたり、サービスからの発信通話に使用される発信者番号を構成したりできます。 このプランは、顧客サービスや音声通知のシナリオで活用できます。

## <a name="countryregion-availability"></a>国/リージョンの可用性

次の表は、各種電話番号の入手先のほか、電話番号の種類に関連付けられている着信通話と発信通話、SMS 機能を示しています。

|番号の種類| 番号の入手先 | 発信先                                        | 着信元                                    |メッセージの送信先       | メッセージの受信元 |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| ローカル (固定)  | US                 | 米国、カナダ、イギリス、ドイツ、フランスなど* +more*| 米国、カナダ、イギリス、ドイツ、フランスなど* +more* |使用不可| 使用不可 |
| フリーダイヤル | US                 | US                                                   | US                                                    |US                | US |

\* 通話の宛先と価格について詳しくは、[価格のページ](../pricing.md)を参照してください。

## <a name="azure-subscriptions-eligibility"></a>対象となる Azure サブスクリプション

電話番号を取得するには、有料の Azure サブスクリプションが必要です。 試用アカウントで電話番号を取得することはできません。 

## <a name="next-steps"></a>次の手順

### <a name="quickstarts"></a>クイックスタート

- [電話番号を取得する](../../quickstarts/telephony-sms/get-phone-number.md)
- [通話を行う](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS を送信する](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>概念説明のドキュメント

- [音声とビデオの概念](../voice-video-calling/about-call-types.md)
- [通話フロー](../call-flows.md)
- [料金](../pricing.md)
