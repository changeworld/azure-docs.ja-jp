---
title: Emotion API とは
titlesuffix: Azure Cognitive Services
description: クラウドベースの感情認識アルゴリズムを使用して、よりパーソナライズされたアプリを構築します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: abf94e0ab6ebb3df649b1958503c086feb4fa19e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237128"
---
# <a name="what-is-the-emotion-api"></a>Emotion API とは

> [!IMPORTANT]
> Emotion API は、2019 年 2 月 15 日に非推奨となる予定です。 現在は、[Face API](https://docs.microsoft.com/azure/cognitive-services/face/) の一部として感情認識機能が一般提供されています。 

Microsoft Emotion API にようこそ。この API を使用すると、Microsoft のクラウド ベースの感情認識アルゴリズムを利用し、より個人用に設定したアプリを作成できます。

### <a name="emotion-recognition"></a>感情認識

Emotion API ベータ版は画像を入力として取り、Face API から、画像内のそれぞれの顔について、一連の感情の信頼度と、顔の境界ボックスを返します。 検出される感情は、喜び、悲しみ、驚き、怒り、恐怖、軽蔑、嫌悪感、中立です。 これらの感情は、文化を問わず普遍的に、同じ基本的な顔の表情によって伝達されます。それらの表情が、Emotion API によって識別されます。

**結果の解釈:**

Emotion API からの結果を解釈するときには、検出された感情を、最も高いスコアを持つ感情として解釈する必要があります。これは、総計が 1 になるようにスコアが正規化されるためです。 ユーザーは必要に応じて、アプリケーション内で信頼度のしきい値をより高く設定することもできます。

感情検出の詳細については、API リファレンスを参照してください。
  * 基本: ユーザーがすでに Face API を呼び出している場合は、入力として顔矩形を送信し、基本レベルを使用することができます。 [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * 標準: ユーザーが顔矩形を送信しない場合は、標準モードを使用する必要があります。  [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Emotion API を使用してストリーミング ビデオを解釈する方法のサンプルについては、「[リアルタイムにビデオを分析する方法](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion)」を参照してください。
