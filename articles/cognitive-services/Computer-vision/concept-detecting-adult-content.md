---
title: 成人向けコンテンツとわいせつなコンテンツの検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したイメージの成人向けコンテンツとわいせつなコンテンツの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 71866149e3d2dca4b39585ce8da73aae658a4d59
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344916"
---
# <a name="detecting-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの検出

さまざまな視覚カテゴリの中には、成人向けできわどいグループがあり、成人向けマテリアルを検出し、性的なコンテンツを含むイメージの表示を制限することができます。 成人向けできわどいコンテンツを検出するためのフィルターは、ユーザーの設定に合わせて、スライディング スケールで設定することができます。

## <a name="defining-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの定義

[イメージ分析メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)でカバーされるさまざまなビジュアル機能のうち、成人向けビジュアル機能では成人向けできわどいイメージを検出することができます。 "成人向け" イメージは、性的な内容で、その多くがヌードや性的な行為を描写したものと定義されています。 "わいせつな" イメージは、性的なものを想起させるものの、"成人向け" とタグ付けされたイメージに比べると性的に明白な内容が少ないものと定義されています。 成人向けビジュアル機能は一般的に、性的なものを想起させ、明らかに性的なコンテンツが含まれているイメージの表示を制限するために使用されます。

## <a name="identifying-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの識別

イメージ分析メソッドでは成人向けコンテンツとわいせつなコンテンツのそれぞれを示すメソッドの JSON 応答で `isAdultContent` と `isRacyContent` の 2 つのプロパティを返します。 どちらのプロパティも True または False のブール値を返します。 このメソッドでは `adultScore` と `racyScore` という 2 つのプロパティも返します。それぞれ成人向けコンテンツとわいせつなコンテンツの識別に関する信頼度スコアを表しています。 成人向けコンテンツとわいせつなコンテンツを検出するための信頼度フィルターは、特定のシナリオに基づくユーザー設定に合うようにスライディング スケールで設定することができます。

## <a name="next-steps"></a>次の手順

[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)と[顔の検出](concept-detecting-faces.md)についての概念を学習します。