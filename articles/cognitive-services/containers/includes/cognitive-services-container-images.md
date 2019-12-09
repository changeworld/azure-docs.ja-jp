---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142205"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なコンテナー イメージの一覧です。 利用できるコンテナー イメージ名とその利用できるタグの完全一覧が必要であれば、[Cognitive Services コンテナー イメージ タグ](../container-image-tags.md)に関するページを参照してください。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>パブリックで "ゲートされていない" (コンテナー レジストリ: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) では、Cognitive Services 向けに一般公開されている "ゲートなし" コンテナーをすべて配信しています。 [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

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
