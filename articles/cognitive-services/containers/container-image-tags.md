---
title: Cognitive Services コンテナー イメージ タグ
titleSuffix: Azure Cognitive Services
description: すべての Cognitive Service コンテナー イメージ タグの包括的一覧
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 8a8c49089d5c0554c4636e98b5820ef206010207
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134167"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services コンテナー イメージ タグ

Azure Cognitive Services からはさまざまなコンテナー イメージが提供されます。 コンテナー レジストリとそれに対応するリポジトリは、コンテナー イメージによって異なります。 各コンテナー イメージ名にからは複数のタグが与えられます。 コンテナー イメージ タグは、コンテナー イメージのバージョンを管理するためのメカニズムです。 この記事は、Cognitive Services コンテナー イメージと利用できるタグをすべてリストアップする包括的リファレンスとして使用されることを意図して作成されています。

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) を使用する場合、コンテナー レジストリ、リポジトリ、コンテナー イメージ名、それらに対応するタグの大文字/小文字の違いに注意してください。これらでは**大文字と小文字が区別されます**。

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector][ad-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-anomaly-detector` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision][cv-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-read` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-read` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>Face

[Face][fa-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-face` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-face` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Form Recognizer

[Form Recognizer][fr-containers] コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-form-recognizer` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[LUIS][lu-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`luis` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/luis` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>カスタム音声変換

[カスタム音声変換][sp-cstt]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-custom-speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>カスタム テキスト読み上げ

[カスタム テキスト読み上げ][sp-ctts]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-custom-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>音声テキスト変換

[音声テキスト変換][sp-stt]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | `en-US` ロケールのコンテナー イメージ。 |
| `2.0.0-amd64-zh-tw-preview` | |
| `2.0.0-amd64-zh-hk-preview` | |
| `2.0.0-amd64-zh-cn-preview` | |
| `2.0.0-amd64-tr-tr-preview` | |
| `2.0.0-amd64-th-th-preview` | |
| `2.0.0-amd64-sv-se-preview` | |
| `2.0.0-amd64-ru-ru-preview` | |
| `2.0.0-amd64-pt-pt-preview` | |
| `2.0.0-amd64-pt-br-preview` | |
| `2.0.0-amd64-pl-pl-preview` | |
| `2.0.0-amd64-nl-nl-preview` | |
| `2.0.0-amd64-nb-no-preview` | |
| `2.0.0-amd64-ko-kr-preview` | |
| `2.0.0-amd64-ja-jp-preview` | |
| `2.0.0-amd64-it-it-preview` | |
| `2.0.0-amd64-hi-in-preview` | |
| `2.0.0-amd64-fr-fr-preview` | |
| `2.0.0-amd64-fr-ca-preview` | |
| `2.0.0-amd64-fi-fi-preview` | |
| `2.0.0-amd64-es-mx-preview` | |
| `2.0.0-amd64-es-es-preview` | |
| `2.0.0-amd64-en-us-preview` | |
| `2.0.0-amd64-en-nz-preview` | |
| `2.0.0-amd64-en-in-preview` | |
| `2.0.0-amd64-en-gb-preview` | |
| `2.0.0-amd64-en-ca-preview` | |
| `2.0.0-amd64-en-au-preview` | |
| `2.0.0-amd64-de-de-preview` | |
| `2.0.0-amd64-da-dk-preview` | |
| `2.0.0-amd64-ca-es-preview` | |
| `2.0.0-amd64-ar-eg-preview` | |
| `1.2.0-amd64-zh-cn-preview` | |
| `1.2.0-amd64-pt-br-preview` | |
| `1.2.0-amd64-preview` | |
| `1.2.0-amd64-ja-jp-preview` | |
| `1.2.0-amd64-it-it-preview` | |
| `1.2.0-amd64-fr-fr-preview` | |
| `1.2.0-amd64-fr-ca-preview` | |
| `1.2.0-amd64-es-mx-preview` | |
| `1.2.0-amd64-es-es-preview` | |
| `1.2.0-amd64-en-us-preview` | |
| `1.2.0-amd64-en-in-preview` | |
| `1.2.0-amd64-en-gb-preview` | |
| `1.2.0-amd64-en-ca-preview` | |
| `1.2.0-amd64-en-au-preview` | |
| `1.2.0-amd64-de-de-preview` | |
| `1.1.3-amd64-zh-cn-preview` | |
| `1.1.3-amd64-pt-br-preview` | |
| `1.1.3-amd64-preview` | |
| `1.1.3-amd64-ja-jp-preview` | |
| `1.1.3-amd64-it-it-preview` | |
| `1.1.3-amd64-fr-fr-preview` | |
| `1.1.3-amd64-fr-ca-preview` | |
| `1.1.3-amd64-es-mx-preview` | |
| `1.1.3-amd64-es-es-preview` | |
| `1.1.3-amd64-en-us-preview` | |
| `1.1.3-amd64-en-in-preview` | |
| `1.1.3-amd64-en-gb-preview` | |
| `1.1.3-amd64-en-ca-preview` | |
| `1.1.3-amd64-en-au-preview` | |
| `1.1.3-amd64-de-de-preview` | |
| `1.1.2-amd64-zh-cn-preview` | |
| `1.1.2-amd64-pt-br-preview` | |
| `1.1.2-amd64-preview` | |
| `1.1.2-amd64-ja-jp-preview` | |
| `1.1.2-amd64-it-it-preview` | |
| `1.1.2-amd64-fr-fr-preview` | |
| `1.1.2-amd64-fr-ca-preview` | |
| `1.1.2-amd64-es-mx-preview` | |
| `1.1.2-amd64-es-es-preview` | |
| `1.1.2-amd64-en-us-preview` | |
| `1.1.2-amd64-en-in-preview` | |
| `1.1.2-amd64-en-gb-preview` | |
| `1.1.2-amd64-en-ca-preview` | |
| `1.1.2-amd64-en-au-preview` | |
| `1.1.2-amd64-de-de-preview` | |
| `1.1.1-amd64-zh-cn-preview` | |
| `1.1.1-amd64-pt-br-preview` | |
| `1.1.1-amd64-ja-jp-preview` | |
| `1.1.1-amd64-it-it-preview` | |
| `1.1.1-amd64-fr-fr-preview` | |
| `1.1.1-amd64-fr-ca-preview` | |
| `1.1.1-amd64-es-mx-preview` | |
| `1.1.1-amd64-es-es-preview` | |
| `1.1.1-amd64-en-us-preview` | |
| `1.1.1-amd64-en-in-preview` | |
| `1.1.1-amd64-en-gb-preview` | |
| `1.1.1-amd64-en-ca-preview` | |
| `1.1.1-amd64-en-au-preview` | |
| `1.1.1-amd64-de-de-preview` | |
| `1.1.0-amd64-zh-cn-preview` | |
| `1.1.0-amd64-pt-br-preview` | |
| `1.1.0-amd64-ja-jp-preview` | |
| `1.1.0-amd64-it-it-preview` | |
| `1.1.0-amd64-fr-fr-preview` | |
| `1.1.0-amd64-fr-ca-preview` | |
| `1.1.0-amd64-es-mx-preview` | |
| `1.1.0-amd64-es-es-preview` | |
| `1.1.0-amd64-en-us-preview` | |
| `1.1.0-amd64-en-in-preview` | |
| `1.1.0-amd64-en-gb-preview` | |
| `1.1.0-amd64-en-ca-preview` | |
| `1.1.0-amd64-en-au-preview` | |
| `1.1.0-amd64-de-de-preview` | |
| `1.0.0-amd64-zh-cn-preview` | |
| `1.0.0-amd64-pt-br-preview` | |
| `1.0.0-amd64-ja-jp-preview` | |
| `1.0.0-amd64-it-it-preview` | |
| `1.0.0-amd64-fr-fr-preview` | |
| `1.0.0-amd64-fr-ca-preview` | |
| `1.0.0-amd64-es-mx-preview` | |
| `1.0.0-amd64-es-es-preview` | |
| `1.0.0-amd64-en-us-preview` | |
| `1.0.0-amd64-en-in-preview` | |
| `1.0.0-amd64-en-gb-preview` | |
| `1.0.0-amd64-en-ca-preview` | |
| `1.0.0-amd64-en-au-preview` | |
| `1.0.0-amd64-de-de-preview` | |

## <a name="text-to-speech"></a>テキスト読み上げ

[テキスト読み上げ][sp-tts]コンテナー イメージは `containerpreview.azurecr.io` コンテナー レジストリにあります。 `microsoft` リポジトリ内にあり、`cognitive-services-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | `en-US` ロケールと `JessaRUS` 音声のコンテナー イメージ。 |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | |
| `1.3.0-amd64-vi-vn-an-preview` | |
| `1.3.0-amd64-tr-tr-sedarus-preview` | |
| `1.3.0-amd64-th-th-pattara-preview` | |
| `1.3.0-amd64-te-in-chitra-preview` | |
| `1.3.0-amd64-ta-in-valluvar-preview` | |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | |
| `1.3.0-amd64-sl-si-lado-preview` | |
| `1.3.0-amd64-sk-sk-filip-preview` | |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | |
| `1.3.0-amd64-ro-ro-andrei-preview` | |
| `1.3.0-amd64-pt-pt-heliarus-preview` | |
| `1.3.0-amd64-pt-br-heloisarus-preview` | |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | |
| `1.3.0-amd64-nl-nl-hannarus-preview` | |
| `1.3.0-amd64-nb-no-huldarus-preview` | |
| `1.3.0-amd64-ms-my-rizwan-preview` | |
| `1.3.0-amd64-ko-kr-heamirus-preview` | |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.3.0-amd64-ja-jp-harukarus-preview` | |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.3.0-amd64-it-it-luciarus-preview` | |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.3.0-amd64-id-id-andika-preview` | |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | |
| `1.3.0-amd64-hr-hr-matej-preview` | |
| `1.3.0-amd64-hi-in-kalpana-preview` | |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | |
| `1.3.0-amd64-hi-in-hemant-preview` | |
| `1.3.0-amd64-he-il-asaf-preview` | |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | |
| `1.3.0-amd64-fr-ch-guillaume-preview` | |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | |
| `1.3.0-amd64-fr-ca-caroline-preview` | |
| `1.3.0-amd64-fi-fi-heidirus-preview` | |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | |
| `1.3.0-amd64-es-mx-hildarus-preview` | |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | |
| `1.3.0-amd64-es-es-laura-apollo-preview` | |
| `1.3.0-amd64-es-es-helenarus-preview` | |
| `1.3.0-amd64-en-us-zirarus-preview` | |
| `1.3.0-amd64-en-us-jessarus-preview` | |
| `1.3.0-amd64-en-us-jessa24krus-preview` | |
| `1.3.0-amd64-en-us-guy24krus-preview` | |
| `1.3.0-amd64-en-us-benjaminrus-preview` | |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | |
| `1.3.0-amd64-en-in-priyarus-preview` | |
| `1.3.0-amd64-en-in-heera-apollo-preview` | |
| `1.3.0-amd64-en-ie-sean-preview` | |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | |
| `1.3.0-amd64-en-gb-hazelrus-preview` | |
| `1.3.0-amd64-en-gb-george-apollo-preview` | |
| `1.3.0-amd64-en-ca-linda-preview` | |
| `1.3.0-amd64-en-ca-heatherrus-preview` | |
| `1.3.0-amd64-en-au-hayleyrus-preview` | |
| `1.3.0-amd64-en-au-catherine-preview` | |
| `1.3.0-amd64-el-gr-stefanos-preview` | |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | |
| `1.3.0-amd64-de-de-heddarus-preview` | |
| `1.3.0-amd64-de-de-hedda-preview` | |
| `1.3.0-amd64-de-ch-karsten-preview` | |
| `1.3.0-amd64-de-at-michael-preview` | |
| `1.3.0-amd64-da-dk-hellerus-preview` | |
| `1.3.0-amd64-cs-cz-jakub-preview` | |
| `1.3.0-amd64-ca-es-herenarus-preview` | |
| `1.3.0-amd64-bg-bg-ivan-preview` | |
| `1.3.0-amd64-ar-sa-naayf-preview` | |
| `1.3.0-amd64-ar-eg-hoda-preview` | |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | |
| `1.2.0-amd64-pt-br-heloisarus-preview` | |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.2.0-amd64-ko-kr-heamirus-preview` | |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.2.0-amd64-ja-jp-harukarus-preview` | |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.2.0-amd64-it-it-luciarus-preview` | |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | |
| `1.2.0-amd64-fr-ca-caroline-preview` | |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | |
| `1.2.0-amd64-es-mx-hildarus-preview` | |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | |
| `1.2.0-amd64-es-es-laura-apollo-preview` | |
| `1.2.0-amd64-es-es-helenarus-preview` | |
| `1.2.0-amd64-en-us-zirarus-preview` | |
| `1.2.0-amd64-en-us-jessarus-preview` | |
| `1.2.0-amd64-en-us-jessa24krus-preview` | |
| `1.2.0-amd64-en-us-guy24krus-preview` | |
| `1.2.0-amd64-en-us-benjaminrus-preview` | |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | |
| `1.2.0-amd64-en-in-priyarus-preview` | |
| `1.2.0-amd64-en-in-heera-apollo-preview` | |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | |
| `1.2.0-amd64-en-gb-hazelrus-preview` | |
| `1.2.0-amd64-en-gb-george-apollo-preview` | |
| `1.2.0-amd64-en-au-hayleyrus-preview` | |
| `1.2.0-amd64-en-au-catherine-preview` | |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | |
| `1.2.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | |
| `1.1.0-amd64-pt-br-heloisarus-preview` | |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.1.0-amd64-ko-kr-heamirus-preview` | |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.1.0-amd64-ja-jp-harukarus-preview` | |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.1.0-amd64-it-it-luciarus-preview` | |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | |
| `1.1.0-amd64-fr-ca-caroline-preview` | |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | |
| `1.1.0-amd64-es-mx-hildarus-preview` | |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | |
| `1.1.0-amd64-es-es-laura-apollo-preview` | |
| `1.1.0-amd64-es-es-helenarus-preview` | |
| `1.1.0-amd64-en-us-zirarus-preview` | |
| `1.1.0-amd64-en-us-jessarus-preview` | |
| `1.1.0-amd64-en-us-jessa24krus-preview` | |
| `1.1.0-amd64-en-us-guy24krus-preview` | |
| `1.1.0-amd64-en-us-benjaminrus-preview` | |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | |
| `1.1.0-amd64-en-in-priyarus-preview` | |
| `1.1.0-amd64-en-in-heera-apollo-preview` | |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | |
| `1.1.0-amd64-en-gb-hazelrus-preview` | |
| `1.1.0-amd64-en-gb-george-apollo-preview` | |
| `1.1.0-amd64-en-au-hayleyrus-preview` | |
| `1.1.0-amd64-en-au-catherine-preview` | |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | |
| `1.1.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-de-de-hedda-preview` | |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | |
| `1.0.0-amd64-en-us-zirarus-preview` | |
| `1.0.0-amd64-en-us-jessarus-preview` | |
| `1.0.0-amd64-en-us-jessa24krus-preview` | |
| `1.0.0-amd64-en-us-guy24krus-preview` | |
| `1.0.0-amd64-en-us-benjaminrus-preview` | |

## <a name="key-phrase-extraction"></a>キー フレーズ抽出

[キー フレーズ抽出][ta-kp]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`keyphrase` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/keyphrase` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>言語検出

[言語検出][ta-la]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`language` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/language` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>感情分析

[感情分析][ta-se]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`sentiment` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/sentiment` です。

このコンテナー イメージでは、次のタグを利用できます。

| イメージ タグ | メモ |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
