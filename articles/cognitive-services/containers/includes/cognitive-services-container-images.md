---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: ae11fe785237a02652af1f16aaba1f1bac5ecd62
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961911"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なすべてのコンテナー イメージの一覧です。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>パブリックで "ゲートされていない" (コンテナー レジストリ: `mcr.microsoft.com`)

Microsoft Container Registry では、Cognitive Services 向けに一般公開されている "ゲートなし" コンテナーをすべてホストしています。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>パブリックの "ゲート付き" プレビュー (コンテナー レジストリ: `containerpreview.azurecr.io`)

Container プレビューのレジストリでは、Cognitive Services 向けに一般公開されている "ゲート付き" コンテナーをすべてホストしています。 これらのコンテナーを使用するには、正式なアクセス依頼が必要です。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | 読み取り | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | カスタム音声変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | カスタム テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
