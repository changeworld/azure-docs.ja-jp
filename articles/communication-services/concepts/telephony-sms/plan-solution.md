---
title: Azure Communication Services のテレフォニーと SMS ソリューションを計画する
titleSuffix: An Azure Communication Services concept document
description: 電話番号とテレフォニーの使用を効果的に計画する方法について説明します。
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945311"
---
# <a name="plan-your-telephony-and-sms-solution"></a>テレフォニーと SMS ソリューションを計画する

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

このドキュメントでは、Azure Communication Services で提供されるさまざまなテレフォニー プランと番号の種類について説明します。 ここでは、Communication Services を通じて利用できる音声サービス プロバイダー、電話番号の種類、プラン、および機能を選択する際に役立つ意思決定フローについて説明します。

## <a name="about-phone-numbers-in-azure-communications-services"></a>Azure Communications Services での電話番号について

Azure Communication Services を使用すると、電話番号を使用してテレフォニー通話および SMS メッセージを送受信することができます。 これらの電話番号を使用して、サービスによって行われる発信通話で発信者 ID を構成できます。
  
Communication Services ソリューションにインポートする既存の電話番号がない場合、最も簡単な開始方法は、Azure Communication Services から数分で新しい電話番号を取得することです。

ソリューションで引き続き使用したい既存の電話番号 (たとえば、1-800–COMPANY) がある場合は、既存のプロバイダーから Communication Services にその番号を移植できます。

次の図は、使用可能なオプション間を移動するのに役立ちます。

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="電話番号に関する決定を行う方法を示す図。":::

次に、Communication Services で使用できる電話番号の種類と機能を確認しましょう。 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Microsoft Direct の電話番号と機能のオファー

Azure Communication Services は、開発者に対して優れた柔軟性を提供します。 ほとんどの電話番号で、"アラカルト" のプラン セットを構成できます。 開発者の中には、着信通話プランのみを必要とする人もいれば、着信通話と送信 SMS のプランを選択する人もいます。 これらのプランは、Communication Services 内で電話番号をリースしたり移植したりするときに選択できます。

利用可能なプランは、運用を行っている国と電話番号の種類によって異なります。次の図は、意思決定フローを示しています。  利用可能なプランは、運用を行っている国と電話番号の種類によって異なります。

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

電話番号の種類を選択する前に、国際電話番号計画を確認してみましょう。

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>任意の参考資料。 国際公衆電気通信番号計画 (E.164)

> [!NOTE]
> E.164 電話番号計画をよく知っている場合でも、Azure Communication Services で提供されている番号の種類と機能をよく理解するために、この情報を確認することをお勧めします。

国際公衆電気通信番号計画は、国際電気通信連合 (ITU) の E.164 勧告に定義されています。 適合する番号は、最大 15 桁に制限されています。

電話番号は、次で構成されます。

-   プレフィックス "+"
-   国際電話のプレフィックスまたは国/地域コード (1、2、または 3 桁) 
-   " *(省略可能)* " 国内の宛先コードまたは番号計画。通常、市外局番と呼ばれます。 このコードの長さは、国によって異なります。 米国では、3 桁です。 オーストラリアとニュージーランドでは、1 桁です。 ドイツ、日本、メキシコ、およびその他のいくつかの国では、市外局番の長さは変動します。 たとえば、ドイツでは市外局番は 2 から 5 桁ですが、日本では 1 から 5 桁になります。
-   加入者番号

> [!NOTE]
> 上記の分類は、ITU E.164 規格に完全に準拠しているわけではなく、簡単な説明を提供することを目的としています。 たとえば、加入者番号は、規格内でさらに細かく分割されます。 国際番号計画について詳しく知りたい場合は、まず、[ITU E.164 規格](https://www.itu.int/rec/T-REC-E.164)のページにアクセスすることをお勧めします。  

次に、番号計画をより深く理解するのに役立つ例をいくつか示します。

米国の地域電話番号:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="米国の地域電話番号の例":::

カナダの地域電話番号:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="カナダの地域電話番号の例":::

北米地域のフリーダイヤル番号:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="北米のフリーダイヤル番号の例":::

英国の携帯電話番号:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="英国の携帯電話番号の例":::

次に、Azure Communication Services で利用可能な特定の電話番号の種類を確認しましょう。

## <a name="phone-number-types-in-azure-communication-services"></a>Azure Communication Services での電話番号の種類

Microsoft では、該当する国で、ショート コードと携帯電話番号に対する地域およびフリーダイヤル プランを提供しています。

次の表は、これらの電話番号の種類をまとめたものです。 

| 電話番号の種類 | 例                              | 利用可能な国    | 一般的なユース ケース                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 地域          | +1 (地理的市外局番) XXX XX XX  | 米国、カナダ、プエルトリコ | アプリケーション内でユーザーに電話番号を割り当てる、または音声自動応答 (IVR) システム/ボットに割り当てる |
| フリーダイヤル         | +1 (フリーダイヤルの市外 "*局番*") XXX XX XX | 米国、カナダ、プエルトリコ | 音声自動応答 (IVR) システム/ボット、SMS アプリケーションに割り当てる                                        |

## <a name="plans"></a>プラン 

電話番号に対して有効にできる機能を見てみましょう。 これらの機能は、規制要件のため、国によって異なります。 Azure Communication Services には、次の機能が用意されています。

- **一方向の送信 SMS**。通知と 2 要素認証のシナリオに役立ちます。
- **双方向の受信および送信 SMS**。1 つのプランの一部として SMS を送受信できるようにする、事前定義されたパッケージです。
- **PSTN 通話**。着信通話を選択できます。また、発信者 ID を使用して発信通話を実行できます。

## <a name="next-steps"></a>次の手順

### <a name="quickstarts"></a>クイックスタート

- [電話番号を取得する](../../quickstarts/telephony-sms/get-phone-number.md)
- [通話を行う](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS を送信する](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>概念説明のドキュメント

- [音声とビデオの概念](../voice-video-calling/about-call-types.md)
- [通話フローと SMS フロー](../call-flows.md)
- [料金](../pricing.md)
