---
title: Cognitive Service の SKU と価格
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2021
ms.author: pafarley
ms.openlocfilehash: f20f73c37224abe1333d2577db8a54ef07eeed34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520785"
---
SKU と価格の情報の一覧については、以下を参照してください。 

#### <a name="multi-service"></a>マルチサービス

| サービス     | 種類    |
|-------------|------------|
| 複数のサービス。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/)に関するページを参照してください。            | `CognitiveServices`     |


#### <a name="vision"></a>視覚

| サービス    | 種類    |
|------------|---------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Prediction | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |

#### <a name="speech"></a>音声

| サービス            | 種類                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 音声認識 | `SpeakerRecognition` |

#### <a name="language"></a>言語

| サービス            | 種類                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 言語サービス   | `TextAnalytics`     |
| Text Translation   | `TextTranslation`   |

#### <a name="decision"></a>決定

| サービス           | 種類               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効にされるサービス機能。
* 事前に定義された数のトランザクションのコスト。 この数を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。

> [!NOTE]
> Cognitive Services の多くには、サービスを試すために使用できる無料レベルがあります。 無料レベルを使用するには、リソースの SKU として `F0` を使用します。