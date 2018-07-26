---
title: Emotion API に関する FAQ | Microsoft Docs
description: Cognitive Services の Emotion API についてよく寄せられる質問とその回答を紹介しています。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372997"
---
# <a name="emotion-api-frequently-asked-questions"></a>Emotion API についてよく寄せられる質問
 
> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日をもって終了します。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

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

**質問**: *バイナリ画像データを渡していますが、[Invalid face image]\(顔の画像が無効です\) というメッセージを受け取ります。* \*"

**回答**: これは、画像を解析中にアルゴリズムに問題が発生したことを意味します。  
* サポートされている入力画像形式には、JPEG、PNG、GIF (最初のフレーム)、BMP が含まれます。 
* 画像ファイル サイズは 4 MB 以内であることが必要です。
* 検出可能な顔のサイズは、36 x 36 から 4096 x 4096 ピクセルまでの範囲です。 この範囲に含まれない顔は検出されません。
* 技術的な課題のために、顔を検出できない場合があります。たとえば、顔の傾きが特に大きい (頭部の画像) ときや、オクルージョンが大きいときです。 顔が正面または正面に近い方向を向いているときに、最善の結果が得られます。

-----
