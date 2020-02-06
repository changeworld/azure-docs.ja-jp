---
title: カスタム キーワードを作成する - Speech サービス
titleSuffix: Azure Cognitive Services
description: デバイスは常にキーワード (またはフレーズ) をリッスンしています。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717010"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>カスタム キーワードを作成する - Speech Studio

デバイスは常にキーワード (またはフレーズ) をリッスンしています。 たとえば、"コルタナさん" は、Cortana アシスタントのキーワードです。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。

この記事では、デバイス用のカスタム キーワードを作成する方法について説明します。

## <a name="create-your-keyword"></a>キーワードを作成する

カスタム キーワードを使用する前に、[Speech Studio](https://aka.ms/sdsdk-speechportal) の [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページを使用してキーワードを作成する必要があります。 キーワードを指定すると、デバイスにデプロイするファイルが生成されます。

1. [Speech Studio](https://aka.ms/sdsdk-speechportal)に移動して**サインイン**します。音声サブスクリプションをまだ持っていない場合は、[ **[サブスクリプションを作成する]** ](https://go.microsoft.com/fwlink/?linkid=2086754) を選択します。

1. [[カスタム キーワード]](https://aka.ms/sdsdk-wakewordportal) ページで **[新しいプロジェクト]** を作成します。 

1. **[名前]** と任意で **[説明]** を入力し、言語を選択します。 プロジェクトは言語あたり 1 つ必要になります。現在のところ、サポートは en-US 言語に限定されています。

    ![キーワード プロジェクトについて説明する](media/custom-keyword/custom-kws-portal-new-project.png)

1. 一覧からプロジェクトを選択します。 

    ![キーワード プロジェクトを選択する](media/custom-keyword/custom-kws-portal-project-list.png)

1. 新しいキーワード モデルを開始するには、 **[モデルのトレーニング]** をクリックします。

1. キーワード モデルの **[名前]** と任意で **[説明]** を入力し、選択した **[キーワード]** に入力し、 **[次へ]** をクリックします。 効果的なキーワードを選択するために役立つ[ガイドライン](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)がいくつかあります。

    ![キーワードを入力する](media/custom-keyword/custom-kws-portal-new-model.png)

1. ポータルでは、次に、キーワードの発音候補が作成されます。 再生ボタンをクリックして各工法をリッスンし、間違った発音の横にあるチェック ボックスをオフにします。 正しい発音のチェック ボックスのみをオンにした後、 **[トレーニング]** をクリックし、キーワードの生成を開始します。 

    ![キーワードを確認する](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. モデルが生成されるまでに最大で 30 分かかる場合があります。 モデルが完了すると、キーワードの一覧が **[処理中]** から **[成功]** に変わります。 これでファイルをダウンロードできます。

    ![キーワードを確認する](media/custom-keyword/custom-kws-portal-download-model.png)

1. .zip ファイルをコンピューターに保存します。 デバイスにカスタム キーワードをデプロイするには、このファイルが必要です。

## <a name="next-steps"></a>次のステップ

カスタム キーワードを [Speech Devices SDK クイックスタート](https://aka.ms/sdsdk-quickstart)でテストします。
