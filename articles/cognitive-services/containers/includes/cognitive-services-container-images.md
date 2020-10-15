---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: aahill
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907000"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なコンテナー イメージの一覧です。 利用できるコンテナー イメージ名とその利用できるタグの完全一覧が必要であれば、[Cognitive Services コンテナー イメージ タグ](../container-image-tags.md)に関するページを参照してください。 

#### <a name="generally-available"></a>一般公開 

Microsoft コンテナー レジストリ (MCR) からは、Cognitive Services 用に一般提供されているすべてのコンテナーが配信されています。 コンテナーは、[Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

**LUIS**

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

詳細については、[LUIS コンテナーの実行およびインストール方法](../../LUIS/luis-container-howto.md)に関するページを参照してください。

**Text Analytics**

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| 感情分析 v3 (英語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| 感情分析 v3 (スペイン語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| 感情分析 v3 (フランス語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| 感情分析 v3 (イタリア語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| 感情分析 v3 (ドイツ語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| 感情分析 v3 (簡体字中国語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| 感情分析 v3 (繁体字中国語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| 感情分析 v3 (日本語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| 感情分析 v3 (ポルトガル語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| 感情分析 v3 (オランダ語) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

詳細については、[Text Analytics コンテナーの実行およびインストール方法](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md)に関するページを参照してください。

**Anomaly Detector** 

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

詳細については、[Anomaly Detector コンテナーの実行とインストールの方法](../../anomaly-detector/anomaly-detector-container-howto.md)に関する記事をご覧ください。

**Speech Service**

> [!NOTE]
> Speech コンテナーを使用するには、[オンライン要求フォーム](https://aka.ms/csgate)に入力する必要があります。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| [音声テキスト変換](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [カスタム音声変換](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [テキスト読み上げ](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"非限定的な" プレビュー 

次のプレビュー コンテナーは、一般公開されています。 Microsoft コンテナー レジストリ (MCR) では、Cognitive Services 向けに一般提供されているゲートなしコンテナーをすべて配信しています。 コンテナーは、[Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"限定的な" プレビュー

これまで、限定的なプレビュー コンテナーは `containerpreview.azurecr.io` リポジトリでホストされていました。 2020年 9 月 22 日以降、これらのコンテナー (Text Analytics for Health を除く) は Microsoft Container Registry (MCR) でホストされます。それらをダウンロードする際に、docker login コマンドを使用する必要はありません。 コンテナーを使用するには、次のことを行う必要があります。

1. [要求フォーム](https://aka.ms/csgate)に Azure サブスクリプション ID とユーザー シナリオを入力します。 
2. 承認されたら、MCR からコンテナーをダウンロードします。 
3. 適切な Azure リソースのキーとエンドポイントを使用して、実行時にコンテナーを認証します。 

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | v3.0 の読み取り | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | v3.1 の読み取り | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | 空間分析 | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | カスタム テキスト読み上げ | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=lid) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ntts) | Neural Text-to-speech | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Text Analytics for Health](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Text Analytics for Health | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

