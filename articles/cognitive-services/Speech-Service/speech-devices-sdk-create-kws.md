---
title: カスタム ウェイク ワードの作成
description: Speech Devices SDK 用のカスタム ウェイク ワードの作成。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282575"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Speech サービスを使用してカスタム ウェイク ワードを作成する

デバイスは常にウェイク ワード (またはフレーズ) をリッスンしています。 たとえば、"Hey Cortana" は、Cortana アシスタントのウェイク ワードです。 ユーザーがウェイク ワードを読み上げると、デバイスによって後続のすべてのオーディオのクラウドへの送信が開始され、ユーザーが読み上げを止めるまで続けられます。 ウェイク ワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。

この記事では、デバイス用のカスタム ウェイク ワードを作成する方法について説明します。

## <a name="choosing-an-effective-wake-word"></a>効果的なウェイク ワードの選択

ウェイク ワードを選択する場合は、次のガイドラインを検討します。

* ウェイク ワードは、英語の単語または語句とする必要があります。 2 秒以内に読み上げることができる必要があります。

* 4 から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は良好なウェイク ワードですが、"Hey" だけではよくありません。

* ウェイク ワードは、英語の一般的な発音規則に従っている必要があります。

* 英語の一般的な発音規則に従う一意の単語または造語も誤検知を少なくすることが可能です。 たとえば、"computerama" の場合は、適切なウェイク ワードになります。

* 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらは、デバイスの誤トリガーとなる可能性があります。

* 別の発音を持つ可能性のあるウェイク ワードは使用しないでください。 ユーザーは、デバイスが応答するようにするために、”正しい” 発音を知っている必要があります。 たとえば、"509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" として発音される可能性があります。 "R.E.I." は、"R E I" または "Ray" として発音される可能性があります。 "Live" は [līv] または [liv] として発音される可能性があります。

* 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" および "20 + cats" の場合は、適切なウェイク ワードにはなりません。 ただし、"go sharp" または "twenty plus cats" の場合は機能します。 さらに、ブランド内の記号、およびマーケティングやドキュメントを使用して、適切な発音を再確認することができます。

> [!NOTE]
> 商標の単語をウェイク ワードとして選択する場合は、その商標を所有していること、または商標の所有者からその使用を許可されていることを確認してください。 Microsoft は、ウェイク ワードの選択により発生する可能性がある問題については法的責任を負いません。

## <a name="creating-your-wake-word"></a>ウェイク ワードの作成

カスタム ウェイク ワードをデバイスで使用するには、事前に Microsoft Custom Wake Word Generation サービスを使用して作成しておく必要があります。 ウェイク ワードを指定したら、サービスによってファイルが作成されます。そのファイルを開発キットに展開することで、デバイス上でウェイク ワードを有効にすることができます。

1. [Custom Speech Service ポータル](https://cris.ai/)に進みます。

2. Azure Active Directory の招待を受信した電子メール アドレスを使用して新しいアカウントを作成します。 

    ![新しいアカウントを作成する](media/speech-devices-sdk/wake-word-1.png)
 
3.  ログインしたら、フォームに入力し、**[Start the journey]\(開始する\)** をクリックします。

    ![正常にログインされました](media/speech-devices-sdk/wake-word-3.png)
 
4. **カスタム ウェイク ワード** ページは公開されていないので、アクセスするためのリンクは表示されません。 代わりに、次のリンクをクリックするか貼り付けます: https://cris.ai/customkws

    ![非表示ページ](media/speech-devices-sdk/wake-word-4.png)
 
6. 目的のウェイク ワードを入力し、それを**送信**します。

    ![ウェイク ワードを入力する](media/speech-devices-sdk/wake-word-5.png)
 
7. ファイルが生成されるまで、数分かかる場合があります。 ブラウザーのタブに回転する円が表示されます。しばらくすると、`.zip` ファイルをダウンロードするように求める情報バーが表示されます。

    ![.zip ファイルの受信](media/speech-devices-sdk/wake-word-6.png)

8. `.zip` ファイルをコンピューターに保存します。 このファイルは、カスタム ウェイク ワードを開発キットに展開する場合に必要です。手順については、「[Speech Devices SDK を使ってみる](speech-devices-sdk-qsg.md)」を参照してください。

9. ここで、**サインアウト**してかまいません。

## <a name="next-steps"></a>次の手順

使い始めるために、[無料の Azure アカウント](https://azure.microsoft.com/free/)を取得し、Speech Devices SDK にサインアップします。

> [!div class="nextstepaction"]
> [Speech Devices SDK にサインアップする](get-speech-devices-sdk.md)

