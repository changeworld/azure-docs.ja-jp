---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: aahill
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876832"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なコンテナー イメージの一覧です。 利用できるコンテナー イメージ名とその利用できるタグの完全一覧が必要であれば、[Cognitive Services コンテナー イメージ タグ](../container-image-tags.md)に関するページを参照してください。 現在、一般提供 (GA) されている Cognitive Services コンテナーはありません。 差し当たり、新たな発表があるまでは、"*パブリックのゲートなし*" または "*パブリックのゲート付きプレビュー*" としてコンテナーが提供されます。

 - "*パブリックのゲートなし*": コンテナーは、ゲート メカニズムなしでパブリックに提供されます。
 - "*パブリックのゲート付きプレビュー*": コンテナーはパブリックに提供されますが、コンテナー レジストリにアクセスするための正式なリクエストが最初に必要となります。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>パブリックで "ゲートされていない" (コンテナー レジストリ: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) では、Cognitive Services 向けに一般公開されている "ゲートなし" コンテナーをすべて配信しています。 コンテナーは、[Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>パブリックの "ゲート付き" プレビュー (コンテナー レジストリ: `containerpreview.azurecr.io`)

Container プレビューのレジストリでは、Cognitive Services 向けに一般公開されている "ゲート付き" コンテナーをすべてホストしています。 これらのコンテナーについては、コンテナー レジストリ経由でアクセスするための正式なリクエストが必要となります。

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Read | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | カスタム音声変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | カスタム テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
