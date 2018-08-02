---
title: Speech Devices SDK を取得する
description: Speech Devices SDK にアクセスする方法を説明します。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: f70b41cd7e3a7a6eddf32ae6ad024fa9ac040f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281784"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK を取得する

## <a name="requesting-access"></a>アクセス権の要求

Speech Devices SDK は制限付きのプレビュー段階にあるため、プログラムに登録する必要があります。 現在、Microsoft では、この製品にアクセスする候補として、大規模の企業を優先しています。

Speech Devices SDK にアクセスするには、次の手順に従います。

1. Microsoft Speech Devices SDKの[サインアップ フォーム](https://aka.ms/sdsdk-signup)に移動します。
1. [使用許諾契約書](speech-devices-sdk-license.md)を読みます。
1. この使用許諾契約の条項に同意する場合は、"同意する" を選択します。
1. フォームの質問に回答します。
1. フォームを送信します。 
1. メール アドレスを Azure Active Directory にまだ登録していない場合は、承認されると、以下のような招待メールを受信します。 メール アドレスを Azure Active Directory に既に登録している場合は、承認されると、Microsoft の音声チームからメール メッセージが送信され、先に進んで [Speech Devices SDK をダウンロード](#download-the-speech-devices-sdk)できます。

## <a name="approval-e-mail"></a>承認のメール

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![メール メッセージ](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>アクセスを受け入れる
次の手順を実行して、登録時に指定したメール アドレスで Azure Active Directory に参加します。 このプロセスによって、Speech Devices SDK の[ダウンロード サイト](https://shares.datatransfer.microsoft.com/)へのアクセスが許可されます。

1. 受信した電子メール メッセージにある **[Get Started]\(作業の開始\)** をクリックします。 お客様の組織で既に Office 365 を使用している場合は、ログインするように求められ、手順 8. に進むことができます。

2. 起動されたブラウザー ウィンドウで **[次へ]** をクリックします。

    ![認証ウィンドウ](media/speech-devices-sdk/get-sdk-2.png)

3. Microsoft アカウントをまだお持ちでない場合は、作成します。 上記の手順 6. で招待メールを受信したのと同じメール アドレスを入力します。

    ![Microsoft アカウントを作成する](media/speech-devices-sdk/get-sdk-3.png)

4. **[次へ]** をクリックしてパスワードを作成します。

5. メールを確認するように求められたら、メールの受信トレイに戻り、自分宛てに送信された確認コードを取得します。
 
7. ダイアログで、メール メッセージのセキュリティ コードを貼り付けるか、入力します。 この例では "8406" です。 その後、 **[次へ]** をクリックします。

    ![メールを確認する](media/speech-devices-sdk/get-sdk-6.png)
 
8. ブラウザー ウィンドウにアクセス パネル アプリケーションが表示される場合、(手順 6. の) メール アドレスが Azure Active Directory の一部であることを確認したことになります。 これで、Speech Devices SDK ダウンロード サイトにアクセスできるようになりました。

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK をダウンロードする

[Speech Devices SDK ダウンロード サイト](https://shares.datatransfer.microsoft.com/)に移動して、先ほど作成した Microsoft アカウントでログインします。 次の手順に従って、Speech Devices SDK、関連するサンプル コード、および参考資料をダウンロードできます。

![SDK ダウンロード サイト](media/speech-devices-sdk/get-sdk-7.png)

1. ブラウザーから Aspera Connect ツールをダウンロードしてインストール操作を求められたら、従います。

    ![Aspera Connect をダウンロードする](media/speech-devices-sdk/get-sdk-8.png)
 
1. **[はい]** をクリックして Aspera Connect に切り替えます。

    ![Aspera Connect に切り替える](media/speech-devices-sdk/get-sdk-9.png)
 
1. **[許可]** をクリックして、Aspera Connect を使用してファイルをダウンロードすることを確認します。

    ![Aspera Connect を使用してダウンロードする](media/speech-devices-sdk/get-sdk-10.png)
 
1. ファイルをダウンロードした後に、Aspera Connect の転送ウィンドウを閉じます。

    ![Aspera Connect の転送ウィンドウ](media/speech-devices-sdk/get-sdk-11.png)
 
既定では、ファイルは **Downloads** フォルダーにダウンロードされます。 このサイトからすぐにログアウトできます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Devices SDK を使ってみる](speech-devices-sdk-qsg.md)
