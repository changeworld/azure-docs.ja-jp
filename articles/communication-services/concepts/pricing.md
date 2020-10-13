---
title: 通話 (音声またはビデオ) およびチャットの価格シナリオ
titleSuffix: An Azure Communication Services concept document
description: Communication Services の価格モデルについて説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4eec258cf642688c87b363ff467f1f368727a013
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761228"
---
# <a name="pricing-scenarios"></a>価格シナリオ

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Azure Communication Services の価格は、前払い料金のない従量課金制モデルに基づきます。 本サービスの消費と使用に対してのみ課金されます。

## <a name="voicevideo-calling-and-screen-sharing"></a>音声またはビデオによる通話と画面共有

Azure Communication Services を使用すると、音声またはビデオによる通話と画面共有をアプリケーションに追加できます。 JavaScript、Objective-C (Apple)、Java (Android)、または .NET のクライアント ライブラリを使用して、アプリケーションにエクスペリエンスを埋め込むことができます。 [使用可能なクライアント ライブラリの完全な一覧](./sdk-options.md)を参照してください。

### <a name="pricing"></a>価格

通話および画面共有のサービスは、グループ通話に対して参加者あたり $0.004/分で、参加者ごとに分単位で課金されます。 可能な各種の呼び出しフローを理解するには、[こちらのページ](./call-flows.md)を参照してください。

通話の各参加者は、通話に接続されている 1 分ごとに課金されます。 これは、ユーザーがビデオ通話、音声通話、画面共有のいずれを行っているかに関係なく当てはまります。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>価格の例:JS および iOS クライアント ライブラリを使用したオーディオまたはビデオによるグループ通話

Alice が、仕事仲間の Bob と Charlie とグループ通話を行いました。 Alice と Bob は JS クライアント ライブラリを使用し、Charlie は iOS クライアント ライブラリを使用しました。

- この通話は合計 60 分間続きました。
- Alice と Bob は通話全体に参加しました。 Alice は、自分のビデオを 5 分間オンにし、自分の画面を 23 分間共有しました。 Bob は、通話全体 (60 分間) にわたってビデオを表示し、自分の画面を 12 分間共有しました。
- Charlie は 43 分後に通話を終了しました。 Charlie は、参加している間中 (43 分間)、オーディオとビデオを使用しました。

**コストの計算**

- 2 人の参加者 x 60 分 x 参加者あたり $0.004/分 = $0.48 [ビデオとオーディオの両方が同じ料金で課金されます]
- 1 人の参加者 x 43 分 x 参加者あたり $0.004/分 = $0.172 [ビデオとオーディオの両方が同じ料金で課金されます]

**グループ通話の総コスト**: $0.48 + $0.172 = $0.652

## <a name="chat"></a>チャット

Communication Services を使用すると、2 人以上のユーザー間でチャット メッセージを送受信する機能でアプリケーションを強化できます。 チャット クライアント ライブラリは、JavaScript、.NET、Python、および Java で使用できます。 クライアント ライブラリの詳細については、[こちらのページ](./sdk-options.md)を参照してください

### <a name="price"></a>Price

送信されたチャット メッセージごとに $0.0008 が課金されます。

### <a name="pricing-example-chat-between-two-users"></a>価格の例:2 人のユーザー間のチャット 

Geeta が、最新情報を共有するために Emily とチャット スレッドを開始し、5 件のメッセージを送信します。 チャットは 10 分間続き、その間に Geeta と Emily はさらに 15 件のメッセージをそれぞれ送信します。

**コストの計算** 
- 送信されたメッセージの数 (5 + 15 + 15) x $0.0008 = $0.028

### <a name="pricing-example-group-chat-with-multiple-users"></a>価格の例:複数のユーザーとのグループ チャット 

Charlie が、旅行を計画するために、友人の Casey と Jasmine とチャット スレッドを開始します。 彼らはしばらくチャットを行い、その間に Charlie、Casey、Jasmine はそれぞれ 20 件、30 件、18 件のメッセージを送信します。 友人の Rose も旅行への参加に興味があるかもしれないと気付いたので、彼女をチャット スレッドに追加し、すべてのメッセージ履歴を彼女と共有します。 

Rose はメッセージを表示し、チャットを開始します。 その間に、Casey は電話を受け、後で会話に追いつくことにします。 Charlie、Jasmine、Rose は旅行日を決定し、それぞれ 30 件、25 件、35 件のメッセージを送信します。

**コストの計算** 

- 送信されたメッセージの数 (20 + 30 + 18 + 30 + 25 + 35) x $0.0008 = $0.1264


## <a name="telephony-and-sms"></a>テレフォニーと SMS

## <a name="price"></a>Price 

テレフォニー サービスは分単位の価格となるのに対し、SMS はメッセージ単位の価格となります。 価格は、ご使用の番号の種類と場所、さらに通話と SMS メッセージの宛先によって決まります。

### <a name="telephone-calling"></a>電話による通話

従来の電話による通話 (公衆交換電話網での通話) は、米国内の電話番号については従量課金制価格で提供されます。 価格は、使用されている番号の種類と通話の宛先に基づく分単位の料金となっています。 以下の表は、最も一般的な通話の宛先について価格の詳細をまとめたものです。 全宛先の一覧については、[詳細な価格リスト](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)を参照してください。


#### <a name="united-states-calling-prices"></a>米国の通話価格

次の価格には、通信に必要な税と料金が含まれています (2021 年 6 月 30 日まで)。

|数値型   |電話をかける   |電話を受ける|
|--------------|-----------|------------|
|ローカル     |$0.013/分から       |$0.0085/分        |
|無料電話番号 |$0.013/分   |$0.0220/分 |

#### <a name="other-calling-destinations"></a>その他の通話の宛先

次の価格には、通信に必要な税と料金が含まれています (2021 年 6 月 30 日まで)。

|発信先   |1 分あたりの価格|
|-----------|------------|
|Canada     |$0.013/分から   |
|イギリス     |$0.015/分から   |
|ドイツ     |$0.015/分から   |
|フランス     |$0.016/分から   |


### <a name="sms"></a>SMS

SMS の価格は従量課金制です。 価格は、メッセージの宛先に基づく、メッセージごとの料金となっています。 メッセージは、無料電話番号から米国内の電話番号に送信することができます。 SMS メッセージの送信にローカル (固定) 電話番号は使用できないので注意してください。

次の価格には、通信に必要な税と料金が含まれています (2021 年 6 月 30 日まで)。

|国   |メッセージを送信する|メッセージを受信する|
|-----------|------------|------------|
|USA (無料電話番号)    |$0.0075/メッセージ   | $0.0075/メッセージ |