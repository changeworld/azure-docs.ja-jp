---
title: よく寄せられる質問 - Emotion API
titlesuffix: Azure Cognitive Services
description: Emotion API についてよく寄せられる質問とその回答を紹介します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238539"
---
# <a name="emotion-api-frequently-asked-questions"></a>Emotion API についてよく寄せられる質問

> [!IMPORTANT]
> Emotion API は、2019 年 2 月 15 日に非推奨となる予定です。 現在は、[Face API](https://docs.microsoft.com/azure/cognitive-services/face/) の一部として感情認識機能が一般提供されています。

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>この FAQ で質問に対する回答が見つからない場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) の Emotion API コミュニティに質問するか、[UserVoice](https://cognitive.uservoice.com/) のヘルプとサポートに連絡してください。  

-----

**質問**: "*Emotion API で最もよい結果を得られるのはどのような画像ですか。*"

**回答**: 最もよい結果を得るためには、顔の正面がすべて見える画像を使用してください。 顔の正面が部分的にしか見えていない場合には信頼性が低下します。また、Emotion API では、顔が 45 度以上横を向いている場合、画像の感情を認識できないことがあります。

-----

**質問**: "*Emotion API で識別できる感情はいくつありますか。*"

**回答**: Emotion API では普遍的に受け入れられている 8 種類の感情が認識されます。
* 喜び
* 悲しみ
* 驚き
* 怒り
* 恐怖
* 軽蔑
* 嫌悪感
* 中立

-----

**質問**: "*ライブ ビデオ ストリームを API に渡して同時に結果を取得する方法はありますか。*"

**回答**: 画像に基づく Emotion API を使用して、フレームごとに呼び出します。あるいは、パフォーマンスを高めるにはフレームをスキップします。  ビデオをフレームごとに分析したサンプルが用意されています。

-----

**質問**: *バイナリ画像データを渡していますが、"Invalid face image" (顔の画像が無効です) というメッセージを受け取ります。**

**回答**: このメッセージは、画像を解析中にアルゴリズムに問題が発生したことを意味します。  
* サポートされている入力画像形式には、JPEG、PNG、GIF (最初のフレーム)、BMP が含まれます。
* 画像ファイル サイズは 4 MB 以内であることが必要です
* 検出可能な顔のサイズの範囲は、36 x 36 ～ 4096 x 4096 ピクセルです。 この範囲に含まれない顔は検出されません
* 技術的な課題のために、顔を検出できない場合があります。たとえば、顔の傾きが特に大きい (頭部の画像) ときや、オクルージョンが大きいときです。 顔が正面または正面に近い方向を向いているときに、最善の結果が得られます

-----
