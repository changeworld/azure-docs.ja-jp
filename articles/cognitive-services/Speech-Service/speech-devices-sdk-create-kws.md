---
title: カスタム ウェイク ワードを作成する - Speech Services
titleSuffix: Azure Cognitive Services
description: デバイスは常にウェイク ワード (またはフレーズ) をリッスンしています。 ユーザーがウェイク ワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 ウェイク ワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a9a316eccc0b5e237d5a48c833f53ea3d80aae1e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082936"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Speech サービスを使用してカスタム ウェイク ワードを作成する

デバイスは常にウェイク ワード (またはフレーズ) をリッスンしています。 たとえば、"Hey Cortana" は、Cortana アシスタントのウェイク ワードです。 ユーザーがウェイク ワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 ウェイク ワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。

この記事では、デバイス用のカスタム ウェイク ワードを作成する方法について説明します。

## <a name="choose-an-effective-wake-word"></a>効果的なウェイク ワードを選択する

ウェイク ワードを選択するときには、以下のガイドラインを考慮に入れます。

* ウェイク ワードは、英語の単語または語句とする必要があります。 2 秒以内に読み上げることができる必要があります。

* 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なウェイク ワードです。 "Hey" だけではよくありません。

* ウェイク ワードは、英語の一般的な発音規則に従っている必要があります。

* 英語の一般的な発音規則に従った一意の単語や造語にすると、誤検知が減る可能性があります。 たとえば "computerama" は、適切なウェイク ワードになります。

* 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤ったトリガーとなる可能性があります。

* 別の発音を持つことがあるウェイク ワードは使わないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I."  は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。

* 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" および "20 + cats" の場合は、適切なウェイク ワードにはなりません。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標の単語をウェイク ワードとして選択する場合は、あなたがその商標を所有しているか、商標の所有者からその単語の使用を許可されていることを確認してください。 Microsoft は、ウェイク ワードの選択により発生する可能性がある問題については法的責任を負いません。

## <a name="create-your-wake-word"></a>ウェイク ワードを作成する

カスタム ウェイク ワードをデバイスで使用する前に、Microsoft Custom Wake Word Generation サービスを使用してカスタム ウェイク ワードを作成する必要があります。 ウェイク ワードを指定すると、このサービスによってファイルが作成されます。このファイルを開発キットに展開することで、デバイスでウェイク ワードを有効にできます。

1. [Custom Speech のサービス ポータル](https://cris.ai/)に移動します。

1. Azure Active Directory の招待を受信したメール アドレスを使用して新しいアカウントを作成します。

    ![新しいアカウントを作成する](media/speech-devices-sdk/wake-word-1.png)

1. **[Custom Wake Word]\(カスタム ウェイク ワード)** ページは公開されていないので、このページに直接移動するリンクはありません。 Custom Speech 機能を利用するには Azure のサブスクリプションが必要ですが、カスタム ウェイク ワード機能では不要です。 "**サブスクリプションが見つかりません。**" というエラー メッセージが表示される場合は、 URL 内の **"Subscriptions?errorMessage=No%20Subscriptions%20found"** を "**customkws**" にただ置き換えて、Enter キーを押します。 URL は、ご利用のリージョンに応じて、 https://westus.cris.ai/customkws、 https://eastasia.cris.ai/customkws、または https://northeurope.cris.ai/customkws のいずれかである必要があります。

    ![[Custom Wake Word]\(カスタム ウェイク ワード) ページは非表示](media/speech-devices-sdk/wake-word-4.png)

1. 選択したウェイク ワードを入力して、**[Submit the word]\(ワードを送信する)** を選択します。

    ![ウェイク ワードを入力する](media/speech-devices-sdk/wake-word-5.png)

1. ファイルが生成されるまで、数分かかる場合があります。 ブラウザー ウィンドウに、回転する円が表示されます。 しばらくすると、.zip ファイルをダウンロードするよう指示する情報バーが表示されます。

    ![.zip ファイルの受信](media/speech-devices-sdk/wake-word-6.png)

1. .zip ファイルをコンピューターに保存します。 開発キットにカスタム ウェイク ワードを展開するには、このファイルが必要です。 カスタム ウェイク ワードを展開するには、「[Speech Devices SDK を使ってみる](speech-devices-sdk-qsg.md)」の手順に従います。

1. **[サインアウト]** を選択します。

## <a name="next-steps"></a>次の手順

使い始めるために、[無料の Azure アカウント](https://azure.microsoft.com/free/)を取得し、Speech Devices SDK にサインアップします。

> [!div class="nextstepaction"]
> [Speech Devices SDK にサインアップする](get-speech-devices-sdk.md)
