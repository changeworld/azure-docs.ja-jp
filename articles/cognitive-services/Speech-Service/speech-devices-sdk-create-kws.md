---
title: カスタム キーワードを作成する - 音声サービス
titleSuffix: Azure Cognitive Services
description: デバイスは常にキーワード (またはフレーズ) をリッスンしています。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 15a0e27f3f96eda27182e8437dc95d047f56e260
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815298"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Speech Service を使用してカスタム キーワードを作成する

デバイスは常にキーワード (またはフレーズ) をリッスンしています。 たとえば、"コルタナさん" は、Cortana アシスタントのキーワードです。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。

この記事では、デバイス用のカスタム キーワードを作成する方法について説明します。

## <a name="choose-an-effective-keyword"></a>有効なキーワードを選択する

キーワードを選択するときには、以下のガイドラインを考慮に入れます。

* キーワードは、英語の単語または語句とする必要があります。 2 秒以内に読み上げることができる必要があります。

* 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なキーワードです。 "Hey" だけではよくありません。

* キーワードは、英語の一般的な発音規則に従っている必要があります。

* 英語の一般的な発音規則に従った一意の単語や造語にすると、誤検知が減る可能性があります。 たとえば "computerama" が適切なキーワードになる可能性があります。

* 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤ったトリガーとなる可能性があります。

* 別の発音をする可能性があるキーワードは使用しないようにします。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I." は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。

* 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" および "20 + cats" は、適切なキーワードになりません。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。

> [!NOTE]
> 商標登録された単語をキーワードとして選択する場合は、自身でその商標を所有しているか、商標の所有者からその単語の使用を許可されている必要があります。 Microsoft は、キーワードの選択によって発生する可能性がある法律上のどんな問題に対しても一切の責任を負いません。

## <a name="create-your-keyword"></a>キーワードを作成する

カスタム キーワードを使用する前に、[Speech Studio](https://aka.ms/sdsdk-speechportal) の [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページを使用してキーワードを作成する必要があります。 キーワードを指定すると、デバイスにデプロイするファイルが生成されます。

1. [Speech Studio](https://aka.ms/sdsdk-speechportal)に移動して**サインイン**します。音声サブスクリプションをまだ持っていない場合は、[ **[サブスクリプションを作成する]** ](https://go.microsoft.com/fwlink/?linkid=2086754) を選択します。

1. [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページで、選択したキーワードを入力し、 **[キーワードの追加]** をクリックします。 効果的なキーワードを選択するために役立つ[ガイドライン](#choose-an-effective-keyword)がいくつかあります。 現在、サポートは en-US 言語に限定されています。

    ![キーワードを入力する](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. ポータルでは、次に、キーワードの発音候補が作成されます。 再生ボタンをクリックして各工法をリッスンし、間違った発音の横にあるチェック ボックスをオフにします。 正しい発音のチェック ボックスのみをオンにした後、 **[送信]** を選択して、キーワードの生成を開始します。 キーワードを変更する場合は、まず、マウス ポインターを行の上に置いたときに右側に表示される [削除] ボタンをクリックして、既存のキーワードを削除します。

    ![キーワードを確認する](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. モデルが生成されるまでに最大で 1 分かかる場合があります。 ファイルをダウンロードするように求められます。

    ![キーワードをダウンロードする](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. .zip ファイルをコンピューターに保存します。 デバイスにカスタム キーワードをデプロイするには、このファイルが必要です。

## <a name="next-steps"></a>次の手順

カスタム キーワードを [Speech Devices SDK クイックスタート](https://aka.ms/sdsdk-quickstart)でテストします。
