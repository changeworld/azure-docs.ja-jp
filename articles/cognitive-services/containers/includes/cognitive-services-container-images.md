---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051241"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なすべてのコンテナー イメージの一覧です。

#### <a name="public-container-registry-mcrmicrosoftcom"></a>パブリック (コンテナー レジストリ: `mcr.microsoft.com`)

Microsoft Container Registry では、Cognitive Services 向けのあらゆる一般提供 (GA) コンテナーをホストされています。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>パブリック プレビュー (コンテナー レジストリ: `containerpreview.azurecr.io`)

Container Preview レジストリでは、一般提供 (GA) にまだ進んでいない、Cognitive Services 向け "パブリック プレビュー" コンテナーがすべてホストされています。 これらのコンテナーを使用するには、正式なアクセス依頼が必要です。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | テキスト認識 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
