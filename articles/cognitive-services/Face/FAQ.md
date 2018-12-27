---
title: よく寄せられる質問 (FAQ) - Face API
titlesuffix: Azure Cognitive Services
description: Face API サービスに関してよく寄せられる質問に対する回答を紹介します。
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 9b30fa0fbbd655c03800dadb19cc2568d404204d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129558"
---
# <a name="face-api-frequently-asked-questions"></a>Face API についてよく寄せられる質問

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>この FAQ で質問に対する回答が見つからない場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) の Face API コミュニティに質問するか、[UserVoice](https://cognitive.uservoice.com/) のヘルプとサポートに連絡してください。

-----
**質問**: Face API の認識、検証、または類似検索の精度を低下させる要因は何ですか?

**回答**: 一般に、人間が以下を含むものを識別するのが難しいのと同じケースです。
* 一方または両方の目を遮っている障害物
* 強すぎる照明。たとえば強烈な逆光照明
* ヘア スタイルやひげの変化
* 加齢による変化
* 極端な表情 (叫んでいるなど)

Face API は、多くの場合、このような難しいケースに成功しますが、精度は低くなります。 認識をより堅牢にし、これらの課題に対処するには、多様な角度と光源を含む写真で人物をトレーニングします。

-----
**質問**: バイナリ画像データを渡していますが、"Invalid face image" (無効な顔画像) エラーが返されます。

**回答**: これは、画像を解析中にアルゴリズムに問題が発生したことを意味します。 原因は次のとおりです。
* サポートされている入力画像形式には、JPEG、PNG、GIF (最初のフレーム)、BMP が含まれます。
* 画像ファイル サイズは 4 MB 以内であることが必要です
* 検出可能な顔のサイズは、36 x 36 から 4096 x 4096 ピクセルまでの範囲です。 この範囲に含まれない顔は検出されません
* 技術的な課題のために、顔を検出できない場合があります。たとえば、顔の傾きが特に大きい (頭部の画像) ときや、オクルージョンが大きいときです。 顔が正面または正面に近い方向を向いているときに、最善の結果が得られます

-----
