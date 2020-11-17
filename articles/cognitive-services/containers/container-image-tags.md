---
title: Cognitive Services コンテナー イメージ タグ
titleSuffix: Azure Cognitive Services
description: すべての Cognitive Service コンテナー イメージ タグの包括的一覧
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412546"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services コンテナー イメージ タグとリリース ノート

Azure Cognitive Services からはさまざまなコンテナー イメージが提供されます。 コンテナー レジストリとそれに対応するリポジトリは、コンテナー イメージによって異なります。 各コンテナー イメージ名にからは複数のタグが与えられます。 コンテナー イメージ タグは、コンテナー イメージのバージョンを管理するためのメカニズムです。 この記事は、Cognitive Services コンテナー イメージと利用できるタグをすべてリストアップする包括的リファレンスとして使用されることを意図して作成されています。

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) を使用する場合、コンテナー レジストリ、リポジトリ、コンテナー イメージ名、それらに対応するタグの大文字/小文字の違いに注意してください。これらでは **大文字と小文字が区別されます**。

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector][ad-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートで見つけることができます。 `azure-cognitive-services/decision` リポジトリ内にあり、`anomaly-detector` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` です。

このコンテナー イメージでは、次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-version"></a>[最新バージョン](#tab/current)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[以前のバージョン](#tab/previous)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Read OCR (光学式文字認識) 

[Computer Vision][cv-containers] Read OCR コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services` リポジトリ内にあり、`read` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/vision/read` です。

このコンテナー イメージでは、次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-version"></a>[最新バージョン](#tab/current)

`v2.0.013250001-amd64-preview` のリリース ノート:

* コンテナーのメモリ使用量をさらに減らします。
* マルチポッド セットアップには外部キャッシュが必要です。 たとえば、キャッシュ用に Redis を設定します。
* Redis キャッシュが設定されており、`ResultExpirationPeriod` が 0 に設定されている場合に結果が欠落する問題を修正しました。
* 要求本文サイズの 26MB の制限を取り除きます。 コンテナーでは 26MB を超えるファイルを受け取れるようになります。
* タイム スタンプとビルド バージョンをコンソール ログに追加します。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[以前のバージョン](#tab/previous)

`1.1.013050001-amd64-preview` のリリース ノート

* システムで認識結果を消去するタイミングを指定するために、`ReadEngineConfig:ResultExpirationPeriod` コンテナー初期化構成を追加しました。 
    * 設定は時間単位であり、既定値は 48 時間です。
    * この設定により、結果格納時のメモリ使用量を減らすことができます。特に、コンテナーのメモリ内ストレージが使用されるときに効果があります。
    * 例 1. ReadEngineConfig:ResultExpirationPeriod=1、プロセスから 1 時間後に認識結果が消去されます。
    * この構成が 0 に設定されている場合、結果が取得された後にシステムで認識結果が消去されます。

* 無効なイメージ形式がシステムに渡されたときの 500 内部サーバー エラーを修正しました。 400 エラーが返されるようになりました。

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

[Form Recognizer][fr-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/custom-form` リポジトリ内にあり、`labeltool` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` です。

このコンテナー イメージでは、次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-version"></a>[最新バージョン](#tab/current)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[以前のバージョン](#tab/previous)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[LUIS][lu-containers] コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/language` リポジトリ内にあり、`luis` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/language/luis` です。

このコンテナー イメージでは、次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-version"></a>[最新バージョン](#tab/current)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>カスタム音声変換

[カスタム音声変換][sp-cstt]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`custom-speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` です。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)の完全な一覧を確認することもできます。


# <a name="latest-version"></a>[最新バージョン](#tab/current)

`2.6.0-amd64` のリリース ノート:

**機能**
* Phraselist v2 のサポート 
* フレーズ リストは次のロケールでサポートされています。
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * ja-JP
    * zh-cn
* カスタムベース モデルのダウンロードのサポート。 
    * `docker run` コマンドで `BaseModelLocale=<locale>` を使用する
* .NET 3.1 に完全に移行しました

**修正**
* 信頼スコアがダイアライゼーション モードで常に 1 になるという問題を修正しました
* TextAnalytics 3.0 API に移行しました

フレーズ リストが含まれているため、このコンテナー イメージのサイズが大きくなっていることに注意してください。

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

`2.5.0-amd64` のリリース ノート:

**機能**
* カスタム モデルでのカスタム発音のサポート
* Azure および Azure US Government Cloud のサポート

**修正**
* ダイアライゼーション モードで非 root ユーザーとして実行する場合の問題を修正しました

| イメージ タグ                    | Notes               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   最初の GA バージョン    |

---

## <a name="custom-text-to-speech"></a>カスタム テキスト読み上げ

[カスタム テキスト読み上げ][sp-ctts]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`custom-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` です。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)の完全な一覧を確認することもできます。


# <a name="latest-version"></a>[最新バージョン](#tab/current)

`1.8.0-amd64` のリリース ノート:

**機能**
* .NET 3.1 に完全に移行しました

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

`1.7.0-amd64` のリリース ノート:

**機能**
* .NET 3.1 に部分的に移行されました

| イメージ タグ                    | Notes               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   最初の GA バージョン    |

---

## <a name="speech-to-text"></a>音声テキスト変換

[音声テキスト変換][sp-stt]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`speech-to-text` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` です。 [MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)の完全な一覧を確認することができます。

音声テキスト変換 v2.5.0 以降、イメージは "*米国政府バージニア*" リージョンでサポートされています。 このリージョンを使う場合は、"*米国政府バージニア*" の課金エンドポイントと API キーを使用してください。

# <a name="latest-version"></a>[最新バージョン](#tab/current)

`2.6.0-amd64-<locale>` のリリース ノート:

**機能**
* 最新のモデルにアップグレードし、.NET 3.1 に完全に移行しました
* Phraselist v2 のサポート
* フレーズ リストは次のロケールでサポートされています。
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * ja-JP
    * zh-cn
* 新しいロケール `cs-CZ` のサポート 
    * 大文字と小文字の区別と句読点は現在サポートされていません。

**修正**
* 信頼スコアがダイアライゼーション モードで常に 1 になるという問題を修正しました
* TextAnalytics 3.0 API を使用するように移行しました

フレーズ リストが含まれているため、このコンテナー イメージのサイズが大きくなっていることに注意してください。 

| イメージ タグ                    | Notes                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | `en-US` ロケールのコンテナー イメージ。                                                             |
| `2.6.0-amd64-<locale>`        | `<locale>` を、以下に一覧表示されている使用可能なロケールのいずれかに置き換えます。 たとえば、「 `2.6.0-amd64-en-us` 」のように指定します。 |

このコンテナー イメージには次のロケールを使用できます。

| v2.6.0 のロケール           | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | `ar-AE` ロケールのコンテナー イメージ。 |
| `ar-eg`                     | `ar-EG` ロケールのコンテナー イメージ。 |
| `ar-kw`                     | `ar-KW` ロケールのコンテナー イメージ。 |
| `ar-qa`                     | `ar-QA` ロケールのコンテナー イメージ。 |
| `ar-sa`                     | `ar-SA` ロケールのコンテナー イメージ。 |
| `ca-es`                     | `ca-ES` ロケールのコンテナー イメージ。 |
| `cs-cz`                     | `cs-CZ` ロケールのコンテナー イメージ。 |
| `da-dk`                     | `da-DK` ロケールのコンテナー イメージ。 |
| `de-de`                     | `de-DE` ロケールのコンテナー イメージ。 |
| `en-au`                     | `en-AU` ロケールのコンテナー イメージ。 |
| `en-ca`                     | `en-CA` ロケールのコンテナー イメージ。 |
| `en-gb`                     | `en-GB` ロケールのコンテナー イメージ。 |
| `en-in`                     | `en-IN` ロケールのコンテナー イメージ。 |
| `en-nz`                     | `en-NZ` ロケールのコンテナー イメージ。 |
| `en-us`                     | `en-US` ロケールのコンテナー イメージ。 |
| `es-es`                     | `es-ES` ロケールのコンテナー イメージ。 |
| `es-mx`                     | `es-MX` ロケールのコンテナー イメージ。 |
| `fi-fi`                     | `fi-FI` ロケールのコンテナー イメージ。 |
| `fr-ca`                     | `fr-CA` ロケールのコンテナー イメージ。 |
| `fr-fr`                     | `fr-FR` ロケールのコンテナー イメージ。 |
| `gu-in`                     | `gu-IN` ロケールのコンテナー イメージ。 |
| `hi-in`                     | `hi-IN` ロケールのコンテナー イメージ。 |
| `it-it`                     | `it-IT` ロケールのコンテナー イメージ。 |
| `ja-jp`                     | `ja-JP` ロケールのコンテナー イメージ。 |
| `ko-kr`                     | `ko-KR` ロケールのコンテナー イメージ。 |
| `mr-in`                     | `mr-IN` ロケールのコンテナー イメージ。 |
| `nb-no`                     | `nb-NO` ロケールのコンテナー イメージ。 |
| `nl-nl`                     | `nl-NL` ロケールのコンテナー イメージ。 |
| `pl-pl`                     | `pl-PL` ロケールのコンテナー イメージ。 |
| `pt-br`                     | `pt-BR` ロケールのコンテナー イメージ。 |
| `pt-pt`                     | `pt-PT` ロケールのコンテナー イメージ。 |
| `ru-ru`                     | `ru-RU` ロケールのコンテナー イメージ。 |
| `sv-se`                     | `sv-SE` ロケールのコンテナー イメージ。 |
| `ta-in`                     | `ta-IN` ロケールのコンテナー イメージ。 |
| `te-in`                     | `te-IN` ロケールのコンテナー イメージ。 |
| `th-th`                     | `th-TH` ロケールのコンテナー イメージ。 |
| `tr-tr`                     | `tr-TR` ロケールのコンテナー イメージ。 |
| `zh-cn`                     | `zh-CN` ロケールのコンテナー イメージ。 |
| `zh-hk`                     | `zh-HK` ロケールのコンテナー イメージ。 |
| `zh-tw`                     | `zh-TW` ロケールのコンテナー イメージ。 |


# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

`2.5.0-amd64-<locale>` のリリース ノート:

**機能**
* Azure US Government Cloud のサポート

**修正**
* ダイアライゼーション モードで非 root ユーザーとして実行する場合の問題を修正しました

| イメージ タグ                  | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | `<locale>` を、以下に一覧表示されている使用可能なロケールのいずれかに置き換えます。 たとえば、「 `2.5.0-amd64-en-us` 」のように指定します。  |

このコンテナー イメージには次のロケールを使用できます。

| v2.5.0 のロケール           | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | `ar-AE` ロケールのコンテナー イメージ。 |
| `ar-eg`                     | `ar-EG` ロケールのコンテナー イメージ。 |
| `ar-kw`                     | `ar-KW` ロケールのコンテナー イメージ。 |
| `ar-qa`                     | `ar-QA` ロケールのコンテナー イメージ。 |
| `ar-sa`                     | `ar-SA` ロケールのコンテナー イメージ。 |
| `ca-es`                     | `ca-ES` ロケールのコンテナー イメージ。 |
| `da-dk`                     | `da-DK` ロケールのコンテナー イメージ。 |
| `de-de`                     | `de-DE` ロケールのコンテナー イメージ。 |
| `en-au`                     | `en-AU` ロケールのコンテナー イメージ。 |
| `en-ca`                     | `en-CA` ロケールのコンテナー イメージ。 |
| `en-gb`                     | `en-GB` ロケールのコンテナー イメージ。 |
| `en-in`                     | `en-IN` ロケールのコンテナー イメージ。 |
| `en-nz`                     | `en-NZ` ロケールのコンテナー イメージ。 |
| `en-us`                     | `en-US` ロケールのコンテナー イメージ。 |
| `es-es`                     | `es-ES` ロケールのコンテナー イメージ。 |
| `es-mx`                     | `es-MX` ロケールのコンテナー イメージ。 |
| `fi-fi`                     | `fi-FI` ロケールのコンテナー イメージ。 |
| `fr-ca`                     | `fr-CA` ロケールのコンテナー イメージ。 |
| `fr-fr`                     | `fr-FR` ロケールのコンテナー イメージ。 |
| `gu-in`                     | `gu-IN` ロケールのコンテナー イメージ。 |
| `hi-in`                     | `hi-IN` ロケールのコンテナー イメージ。 |
| `it-it`                     | `it-IT` ロケールのコンテナー イメージ。 |
| `ja-jp`                     | `ja-JP` ロケールのコンテナー イメージ。 |
| `ko-kr`                     | `ko-KR` ロケールのコンテナー イメージ。 |
| `mr-in`                     | `mr-IN` ロケールのコンテナー イメージ。 |
| `nb-no`                     | `nb-NO` ロケールのコンテナー イメージ。 |
| `nl-nl`                     | `nl-NL` ロケールのコンテナー イメージ。 |
| `pl-pl`                     | `pl-PL` ロケールのコンテナー イメージ。 |
| `pt-br`                     | `pt-BR` ロケールのコンテナー イメージ。 |
| `pt-pt`                     | `pt-PT` ロケールのコンテナー イメージ。 |
| `ru-ru`                     | `ru-RU` ロケールのコンテナー イメージ。 |
| `sv-se`                     | `sv-SE` ロケールのコンテナー イメージ。 |
| `ta-in`                     | `ta-IN` ロケールのコンテナー イメージ。 |
| `te-in`                     | `te-IN` ロケールのコンテナー イメージ。 |
| `th-th`                     | `th-TH` ロケールのコンテナー イメージ。 |
| `tr-tr`                     | `tr-TR` ロケールのコンテナー イメージ。 |
| `zh-cn`                     | `zh-CN` ロケールのコンテナー イメージ。 |
| `zh-hk`                     | `zh-HK` ロケールのコンテナー イメージ。 |
| `zh-tw`                     | `zh-TW` ロケールのコンテナー イメージ。 |

---

## <a name="text-to-speech"></a>テキスト読み上げ

[テキスト読み上げ][sp-tts]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` です。

このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)の完全な一覧を確認することもできます。


# <a name="latest-version"></a>[最新バージョン](#tab/current)

`1.8.0-amd64-<locale-and-voice>` のリリース ノート:

**機能**
* .NET 3.1 に完全に移行しました

| イメージ タグ                                  | Notes                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | `en-US` ロケールと `en-US-AriaRUS` 音声のコンテナー イメージ。                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | `<locale>` を、以下に一覧表示されている使用可能なロケールのいずれかに置き換えます。 たとえば、「 `1.8.0-amd64-en-us-ariarus` 」のように指定します。  |


| v1.8.0 のロケール                          | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | `ar-EG` ロケールと `ar-EG-Hoda` 音声のコンテナー イメージ。            |
| `ar-sa-naayf`                               | `ar-SA` ロケールと `ar-SA-Naayf` 音声のコンテナー イメージ。           |
| `bg-bg-ivan`                                | `bg-BG` ロケールと `bg-BG-Ivan` 音声のコンテナー イメージ。            |
| `ca-es-herenarus`                           | `ca-ES` ロケールと `ca-ES-HerenaRUS` 音声のコンテナー イメージ。       |
| `cs-cz-jakub`                               | `cs-CZ` ロケールと `cs-CZ-Jakub` 音声のコンテナー イメージ。           |
| `da-dk-hellerus`                            | `da-DK` ロケールと `da-DK-HelleRUS` 音声のコンテナー イメージ。        |
| `de-at-michael`                             | `de-AT` ロケールと `de-AT-Michael` 音声のコンテナー イメージ。         |
| `de-ch-karsten`                             | `de-CH` ロケールと `de-CH-Karsten` 音声のコンテナー イメージ。         |
| `de-de-hedda`                               | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `de-de-heddarus`                            | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `de-de-stefan-apollo`                       | `de-DE` ロケールと `de-DE-Stefan-Apollo` 音声のコンテナー イメージ。   |
| `el-gr-stefanos`                            | `el-GR` ロケールと `el-GR-Stefanos` 音声のコンテナー イメージ。        |
| `en-au-catherine`                           | `en-AU` ロケールと `en-AU-Catherine` 音声のコンテナー イメージ。       |
| `en-au-hayleyrus`                           | `en-AU` ロケールと `en-AU-HayleyRUS` 音声のコンテナー イメージ。       |
| `en-ca-heatherrus`                          | `en-CA` ロケールと `en-CA-HeatherRUS` 音声のコンテナー イメージ。      |
| `en-ca-linda`                               | `en-CA` ロケールと `en-CA-Linda` 音声のコンテナー イメージ。           |
| `en-gb-george-apollo`                       | `en-GB` ロケールと `en-GB-George-Apollo` 音声のコンテナー イメージ。   |
| `en-gb-hazelrus`                            | `en-GB` ロケールと `en-GB-HazelRUS` 音声のコンテナー イメージ。        |
| `en-gb-susan-apollo`                        | `en-GB` ロケールと `en-GB-Susan-Apollo` 音声のコンテナー イメージ。    |
| `en-ie-sean`                                | `en-IE` ロケールと `en-IE-Sean` 音声のコンテナー イメージ。            |
| `en-in-heera-apollo`                        | `en-IN` ロケールと `en-IN-Heera-Apollo` 音声のコンテナー イメージ。    |
| `en-in-priyarus`                            | `en-IN` ロケールと `en-IN-PriyaRUS` 音声のコンテナー イメージ。        |
| `en-in-ravi-apollo`                         | `en-IN` ロケールと `en-IN-Ravi-Apollo` 音声のコンテナー イメージ。     |
| `en-us-benjaminrus`                         | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `en-us-guy24krus`                           | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `en-us-aria24krus`                          | `en-US` ロケールと `en-US-Aria24kRUS` 音声のコンテナー イメージ。      |
| `en-us-ariarus`                             | `en-US` ロケールと `en-US-AriaRUS` 音声のコンテナー イメージ。         |
| `en-us-zirarus`                             | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `es-es-helenarus`                           | `es-ES` ロケールと `es-ES-HelenaRUS` 音声のコンテナー イメージ。       |
| `es-es-laura-apollo`                        | `es-ES` ロケールと `es-ES-Laura-Apollo` 音声のコンテナー イメージ。    |
| `es-es-pablo-apollo`                        | `es-ES` ロケールと `es-ES-Pablo-Apollo` 音声のコンテナー イメージ。    |
| `es-mx-hildarus`                            | `es-MX` ロケールと `es-MX-HildaRUS` 音声のコンテナー イメージ。        |
| `es-mx-raul-apollo`                         | `es-MX` ロケールと `es-MX-Raul-Apollo` 音声のコンテナー イメージ。     |
| `fi-fi-heidirus`                            | `fi-FI` ロケールと `fi-FI-HeidiRUS` 音声のコンテナー イメージ。        |
| `fr-ca-caroline`                            | `fr-CA` ロケールと `fr-CA-Caroline` 音声のコンテナー イメージ。        |
| `fr-ca-harmonierus`                         | `fr-CA` ロケールと `fr-CA-HarmonieRUS` 音声のコンテナー イメージ。     |
| `fr-ch-guillaume`                           | `fr-CH` ロケールと `fr-CH-Guillaume` 音声のコンテナー イメージ。       |
| `fr-fr-hortenserus`                         | `fr-FR` ロケールと `fr-FR-HortenseRUS` 音声のコンテナー イメージ。     |
| `fr-fr-julie-apollo`                        | `fr-FR` ロケールと `fr-FR-Julie-Apollo` 音声のコンテナー イメージ。    |
| `fr-fr-paul-apollo`                         | `fr-FR` ロケールと `fr-FR-Paul-Apollo` 音声のコンテナー イメージ。     |
| `he-il-asaf`                                | `he-IL` ロケールと `he-IL-Asaf` 音声のコンテナー イメージ。            |
| `hi-in-hemant`                              | `hi-IN` ロケールと `hi-IN-Hemant` 音声のコンテナー イメージ。          |
| `hi-in-kalpana-apollo`                      | `hi-IN` ロケールと `hi-IN-Kalpana-Apollo` 音声のコンテナー イメージ。  |
| `hi-in-kalpana`                             | `hi-IN` ロケールと `hi-IN-Kalpana` 音声のコンテナー イメージ。         |
| `hr-hr-matej`                               | `hr-HR` ロケールと `hr-HR-Matej` 音声のコンテナー イメージ。           |
| `hu-hu-szabolcs`                            | `hu-HU` ロケールと `hu-HU-Szabolcs` 音声のコンテナー イメージ。        |
| `id-id-andika`                              | `id-ID` ロケールと `id-ID-Andika` 音声のコンテナー イメージ。          |
| `it-it-cosimo-apollo`                       | `it-IT` ロケールと `it-IT-Cosimo-Apollo` 音声のコンテナー イメージ。   |
| `it-it-luciarus`                            | `it-IT` ロケールと `it-IT-LuciaRUS` 音声のコンテナー イメージ。        |
| `ja-jp-ayumi-apollo`                        | `ja-JP` ロケールと `ja-JP-Ayumi-Apollo` 音声のコンテナー イメージ。    |
| `ja-jp-harukarus`                           | `ja-JP` ロケールと `ja-JP-HarukaRUS` 音声のコンテナー イメージ。       |
| `ja-jp-ichiro-apollo`                       | `ja-JP` ロケールと `ja-JP-Ichiro-Apollo` 音声のコンテナー イメージ。   |
| `ko-kr-heamirus`                            | `ko-KR` ロケールと `ko-KR-HeamiRUS` 音声のコンテナー イメージ。        |
| `ms-my-rizwan`                              | `ms-MY` ロケールと `ms-MY-Rizwan` 音声のコンテナー イメージ。          |
| `nb-no-huldarus`                            | `nb-NO` ロケールと `nb-NO-HuldaRUS` 音声のコンテナー イメージ。        |
| `nl-nl-hannarus`                            | `nl-NL` ロケールと `nl-NL-HannaRUS` 音声のコンテナー イメージ。        |
| `pl-pl-paulinarus`                          | `pl-PL` ロケールと `pl-PL-PaulinaRUS` 音声のコンテナー イメージ。      |
| `pt-br-daniel-apollo`                       | `pt-BR` ロケールと `pt-BR-Daniel-Apollo` 音声のコンテナー イメージ。   |
| `pt-br-heloisarus`                          | `pt-BR` ロケールと `pt-BR-HeloisaRUS` 音声のコンテナー イメージ。      |
| `pt-pt-heliarus`                            | `pt-PT` ロケールと `pt-PT-HeliaRUS` 音声のコンテナー イメージ。        |
| `ro-ro-andrei`                              | `ro-RO` ロケールと `ro-RO-Andrei` 音声のコンテナー イメージ。          |
| `ru-ru-ekaterinarus`                        | `ru-RU` ロケールと `ru-RU-EkaterinaRUS` 音声のコンテナー イメージ。    |
| `ru-ru-irina-apollo`                        | `ru-RU` ロケールと `ru-RU-Irina-Apollo` 音声のコンテナー イメージ。    |
| `ru-ru-pavel-apollo`                        | `ru-RU` ロケールと `ru-RU-Pavel-Apollo` 音声のコンテナー イメージ。    |
| `sk-sk-filip`                               | `sk-SK` ロケールと `sk-SK-Filip` 音声のコンテナー イメージ。           |
| `sl-si-lado`                                | `sl-SI` ロケールと `sl-SI-Lado` 音声のコンテナー イメージ。            |
| `sv-se-hedvigrus`                           | `sv-SE` ロケールと `sv-SE-HedvigRUS` 音声のコンテナー イメージ。       |
| `ta-in-valluvar`                            | `ta-IN` ロケールと `ta-IN-Valluvar` 音声のコンテナー イメージ。        |
| `te-in-chitra`                              | `te-IN` ロケールと `te-IN-Chitra` 音声のコンテナー イメージ。          |
| `th-th-pattara`                             | `th-TH` ロケールと `th-TH-Pattara` 音声のコンテナー イメージ。         |
| `tr-tr-sedarus`                             | `tr-TR` ロケールと `tr-TR-SedaRUS` 音声のコンテナー イメージ。         |
| `vi-vn-an`                                  | `vi-VN` ロケールと `vi-VN-An` 音声のコンテナー イメージ。              |
| `zh-cn-huihuirus`                           | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `zh-cn-kangkang-apollo`                     | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |
| `zh-hk-danny-apollo`                        | `zh-HK` ロケールと `zh-HK-Danny-Apollo` 音声のコンテナー イメージ。    |
| `zh-hk-tracy-apollo`                        | `zh-HK` ロケールと `zh-HK-Tracy-Apollo` 音声のコンテナー イメージ。    |
| `zh-hk-tracyrus`                            | `zh-HK` ロケールと `zh-HK-TracyRUS` 音声のコンテナー イメージ。        |
| `zh-tw-hanhanrus`                           | `zh-TW` ロケールと `zh-TW-HanHanRUS` 音声のコンテナー イメージ。       |
| `zh-tw-yating-apollo`                       | `zh-TW` ロケールと `zh-TW-Yating-Apollo` 音声のコンテナー イメージ。   |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` ロケールと `zh-TW-Zhiwei-Apollo` 音声のコンテナー イメージ。   |


# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

`1.7.0-amd64-<locale-and-voice>` のリリース ノート:

**機能**
* コンポーネントを .NET 3.1 にアップグレードしました

| イメージ タグ                                  | Notes                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 最初の GA バージョン。 `<locale>` を、以下に一覧表示されている使用可能なロケールのいずれかに置き換えます。 たとえば、「 `1.7.0-amd64-en-us-ariarus` 」のように指定します。  |


| V 1.7.0 のロケール                          | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | `ar-EG` ロケールと `ar-EG-Hoda` 音声のコンテナー イメージ。            |
| `ar-sa-naayf`                               | `ar-SA` ロケールと `ar-SA-Naayf` 音声のコンテナー イメージ。           |
| `bg-bg-ivan`                                | `bg-BG` ロケールと `bg-BG-Ivan` 音声のコンテナー イメージ。            |
| `ca-es-herenarus`                           | `ca-ES` ロケールと `ca-ES-HerenaRUS` 音声のコンテナー イメージ。       |
| `cs-cz-jakub`                               | `cs-CZ` ロケールと `cs-CZ-Jakub` 音声のコンテナー イメージ。           |
| `da-dk-hellerus`                            | `da-DK` ロケールと `da-DK-HelleRUS` 音声のコンテナー イメージ。        |
| `de-at-michael`                             | `de-AT` ロケールと `de-AT-Michael` 音声のコンテナー イメージ。         |
| `de-ch-karsten`                             | `de-CH` ロケールと `de-CH-Karsten` 音声のコンテナー イメージ。         |
| `de-de-hedda`                               | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `de-de-heddarus`                            | `de-DE` ロケールと `de-DE-Hedda` 音声のコンテナー イメージ。           |
| `de-de-stefan-apollo`                       | `de-DE` ロケールと `de-DE-Stefan-Apollo` 音声のコンテナー イメージ。   |
| `el-gr-stefanos`                            | `el-GR` ロケールと `el-GR-Stefanos` 音声のコンテナー イメージ。        |
| `en-au-catherine`                           | `en-AU` ロケールと `en-AU-Catherine` 音声のコンテナー イメージ。       |
| `en-au-hayleyrus`                           | `en-AU` ロケールと `en-AU-HayleyRUS` 音声のコンテナー イメージ。       |
| `en-ca-heatherrus`                          | `en-CA` ロケールと `en-CA-HeatherRUS` 音声のコンテナー イメージ。      |
| `en-ca-linda`                               | `en-CA` ロケールと `en-CA-Linda` 音声のコンテナー イメージ。           |
| `en-gb-george-apollo`                       | `en-GB` ロケールと `en-GB-George-Apollo` 音声のコンテナー イメージ。   |
| `en-gb-hazelrus`                            | `en-GB` ロケールと `en-GB-HazelRUS` 音声のコンテナー イメージ。        |
| `en-gb-susan-apollo`                        | `en-GB` ロケールと `en-GB-Susan-Apollo` 音声のコンテナー イメージ。    |
| `en-ie-sean`                                | `en-IE` ロケールと `en-IE-Sean` 音声のコンテナー イメージ。            |
| `en-in-heera-apollo`                        | `en-IN` ロケールと `en-IN-Heera-Apollo` 音声のコンテナー イメージ。    |
| `en-in-priyarus`                            | `en-IN` ロケールと `en-IN-PriyaRUS` 音声のコンテナー イメージ。        |
| `en-in-ravi-apollo`                         | `en-IN` ロケールと `en-IN-Ravi-Apollo` 音声のコンテナー イメージ。     |
| `en-us-benjaminrus`                         | `en-US` ロケールと `en-US-BenjaminRUS` 音声のコンテナー イメージ。     |
| `en-us-guy24krus`                           | `en-US` ロケールと `en-US-Guy24kRUS` 音声のコンテナー イメージ。       |
| `en-us-aria24krus`                          | `en-US` ロケールと `en-US-Aria24kRUS` 音声のコンテナー イメージ。      |
| `en-us-ariarus`                             | `en-US` ロケールと `en-US-AriaRUS` 音声のコンテナー イメージ。         |
| `en-us-zirarus`                             | `en-US` ロケールと `en-US-ZiraRUS` 音声のコンテナー イメージ。         |
| `es-es-helenarus`                           | `es-ES` ロケールと `es-ES-HelenaRUS` 音声のコンテナー イメージ。       |
| `es-es-laura-apollo`                        | `es-ES` ロケールと `es-ES-Laura-Apollo` 音声のコンテナー イメージ。    |
| `es-es-pablo-apollo`                        | `es-ES` ロケールと `es-ES-Pablo-Apollo` 音声のコンテナー イメージ。    |
| `es-mx-hildarus`                            | `es-MX` ロケールと `es-MX-HildaRUS` 音声のコンテナー イメージ。        |
| `es-mx-raul-apollo`                         | `es-MX` ロケールと `es-MX-Raul-Apollo` 音声のコンテナー イメージ。     |
| `fi-fi-heidirus`                            | `fi-FI` ロケールと `fi-FI-HeidiRUS` 音声のコンテナー イメージ。        |
| `fr-ca-caroline`                            | `fr-CA` ロケールと `fr-CA-Caroline` 音声のコンテナー イメージ。        |
| `fr-ca-harmonierus`                         | `fr-CA` ロケールと `fr-CA-HarmonieRUS` 音声のコンテナー イメージ。     |
| `fr-ch-guillaume`                           | `fr-CH` ロケールと `fr-CH-Guillaume` 音声のコンテナー イメージ。       |
| `fr-fr-hortenserus`                         | `fr-FR` ロケールと `fr-FR-HortenseRUS` 音声のコンテナー イメージ。     |
| `fr-fr-julie-apollo`                        | `fr-FR` ロケールと `fr-FR-Julie-Apollo` 音声のコンテナー イメージ。    |
| `fr-fr-paul-apollo`                         | `fr-FR` ロケールと `fr-FR-Paul-Apollo` 音声のコンテナー イメージ。     |
| `he-il-asaf`                                | `he-IL` ロケールと `he-IL-Asaf` 音声のコンテナー イメージ。            |
| `hi-in-hemant`                              | `hi-IN` ロケールと `hi-IN-Hemant` 音声のコンテナー イメージ。          |
| `hi-in-kalpana-apollo`                      | `hi-IN` ロケールと `hi-IN-Kalpana-Apollo` 音声のコンテナー イメージ。  |
| `hi-in-kalpana`                             | `hi-IN` ロケールと `hi-IN-Kalpana` 音声のコンテナー イメージ。         |
| `hr-hr-matej`                               | `hr-HR` ロケールと `hr-HR-Matej` 音声のコンテナー イメージ。           |
| `hu-hu-szabolcs`                            | `hu-HU` ロケールと `hu-HU-Szabolcs` 音声のコンテナー イメージ。        |
| `id-id-andika`                              | `id-ID` ロケールと `id-ID-Andika` 音声のコンテナー イメージ。          |
| `it-it-cosimo-apollo`                       | `it-IT` ロケールと `it-IT-Cosimo-Apollo` 音声のコンテナー イメージ。   |
| `it-it-luciarus`                            | `it-IT` ロケールと `it-IT-LuciaRUS` 音声のコンテナー イメージ。        |
| `ja-jp-ayumi-apollo`                        | `ja-JP` ロケールと `ja-JP-Ayumi-Apollo` 音声のコンテナー イメージ。    |
| `ja-jp-harukarus`                           | `ja-JP` ロケールと `ja-JP-HarukaRUS` 音声のコンテナー イメージ。       |
| `ja-jp-ichiro-apollo`                       | `ja-JP` ロケールと `ja-JP-Ichiro-Apollo` 音声のコンテナー イメージ。   |
| `ko-kr-heamirus`                            | `ko-KR` ロケールと `ko-KR-HeamiRUS` 音声のコンテナー イメージ。        |
| `ms-my-rizwan`                              | `ms-MY` ロケールと `ms-MY-Rizwan` 音声のコンテナー イメージ。          |
| `nb-no-huldarus`                            | `nb-NO` ロケールと `nb-NO-HuldaRUS` 音声のコンテナー イメージ。        |
| `nl-nl-hannarus`                            | `nl-NL` ロケールと `nl-NL-HannaRUS` 音声のコンテナー イメージ。        |
| `pl-pl-paulinarus`                          | `pl-PL` ロケールと `pl-PL-PaulinaRUS` 音声のコンテナー イメージ。      |
| `pt-br-daniel-apollo`                       | `pt-BR` ロケールと `pt-BR-Daniel-Apollo` 音声のコンテナー イメージ。   |
| `pt-br-heloisarus`                          | `pt-BR` ロケールと `pt-BR-HeloisaRUS` 音声のコンテナー イメージ。      |
| `pt-pt-heliarus`                            | `pt-PT` ロケールと `pt-PT-HeliaRUS` 音声のコンテナー イメージ。        |
| `ro-ro-andrei`                              | `ro-RO` ロケールと `ro-RO-Andrei` 音声のコンテナー イメージ。          |
| `ru-ru-ekaterinarus`                        | `ru-RU` ロケールと `ru-RU-EkaterinaRUS` 音声のコンテナー イメージ。    |
| `ru-ru-irina-apollo`                        | `ru-RU` ロケールと `ru-RU-Irina-Apollo` 音声のコンテナー イメージ。    |
| `ru-ru-pavel-apollo`                        | `ru-RU` ロケールと `ru-RU-Pavel-Apollo` 音声のコンテナー イメージ。    |
| `sk-sk-filip`                               | `sk-SK` ロケールと `sk-SK-Filip` 音声のコンテナー イメージ。           |
| `sl-si-lado`                                | `sl-SI` ロケールと `sl-SI-Lado` 音声のコンテナー イメージ。            |
| `sv-se-hedvigrus`                           | `sv-SE` ロケールと `sv-SE-HedvigRUS` 音声のコンテナー イメージ。       |
| `ta-in-valluvar`                            | `ta-IN` ロケールと `ta-IN-Valluvar` 音声のコンテナー イメージ。        |
| `te-in-chitra`                              | `te-IN` ロケールと `te-IN-Chitra` 音声のコンテナー イメージ。          |
| `th-th-pattara`                             | `th-TH` ロケールと `th-TH-Pattara` 音声のコンテナー イメージ。         |
| `tr-tr-sedarus`                             | `tr-TR` ロケールと `tr-TR-SedaRUS` 音声のコンテナー イメージ。         |
| `vi-vn-an`                                  | `vi-VN` ロケールと `vi-VN-An` 音声のコンテナー イメージ。              |
| `zh-cn-huihuirus`                           | `zh-CN` ロケールと `zh-CN-HuihuiRUS` 音声のコンテナー イメージ。       |
| `zh-cn-kangkang-apollo`                     | `zh-CN` ロケールと `zh-CN-Kangkang-Apollo` 音声のコンテナー イメージ。 |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` ロケールと `zh-CN-Yaoyao-Apollo` 音声のコンテナー イメージ。   |
| `zh-hk-danny-apollo`                        | `zh-HK` ロケールと `zh-HK-Danny-Apollo` 音声のコンテナー イメージ。    |
| `zh-hk-tracy-apollo`                        | `zh-HK` ロケールと `zh-HK-Tracy-Apollo` 音声のコンテナー イメージ。    |
| `zh-hk-tracyrus`                            | `zh-HK` ロケールと `zh-HK-TracyRUS` 音声のコンテナー イメージ。        |
| `zh-tw-hanhanrus`                           | `zh-TW` ロケールと `zh-TW-HanHanRUS` 音声のコンテナー イメージ。       |
| `zh-tw-yating-apollo`                       | `zh-TW` ロケールと `zh-TW-Yating-Apollo` 音声のコンテナー イメージ。   |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` ロケールと `zh-TW-Zhiwei-Apollo` 音声のコンテナー イメージ。   |

---

## <a name="neural-text-to-speech"></a>ニューラル テキスト読み上げ

[ニューラル テキスト読み上げ][sp-ntts]コンテナー イメージは、`mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`neural-text-to-speech` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` です。

このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)の完全な一覧を確認することもできます。

| イメージ タグ                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` ロケールと `en-US-AriaNeural` 音声のコンテナー イメージ。      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | `<locale>` を、以下に一覧表示されている使用可能なロケールのいずれかに置き換えます。 たとえば、「 `1.2.0-amd64-en-us-arianeural-preview` 」のように指定します。 |


| v 1.2.0 Preview のロケールと音声           | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` ロケールと `en-US-AriaNeural` 音声のコンテナー イメージ。      |
| `de-de-katjaneural-preview`                 | `de-DE` ロケールと `de-DE-KatjaNeural` 音声のコンテナー イメージ。     |
| `en-au-natashaneural-preview`               | `en-AU` ロケールと `en-AU-NatashaNeural` 音声のコンテナー イメージ。   |
| `en-ca-claraneural-preview`                 | `en-CA` ロケールと `en-CA-ClaraNeural` 音声のコンテナー イメージ。     |
| `en-gb-libbyneural-preview`                 | `en-GB` ロケールと `en-GB-LibbyNeural` 音声のコンテナー イメージ。     |
| `en-gb-mianeural-preview`                   | `en-GB` ロケールと `en-GB-MiaNeural` 音声のコンテナー イメージ。       |
| `en-us-arianeural-preview`                  | `en-US` ロケールと `en-US-AriaNeural` 音声のコンテナー イメージ。      |
| `en-us-guyneural-preview`                   | `en-US` ロケールと `en-US-GuyNeural` 音声のコンテナー イメージ。       |
| `es-es-elviraneural-preview`                | `es-ES` ロケールと `es-ES-ElviraNeural` 音声のコンテナー イメージ。    |
| `es-mx-dalianeural-preview`                 | `es-MX` ロケールと `es-MX-DaliaNeural` 音声のコンテナー イメージ。     |
| `fr-ca-sylvieneural-preview`                | `fr-CA` ロケールと `fr-CA-SylvieNeural` 音声のコンテナー イメージ。    |
| `fr-fr-deniseneural-preview`                | `fr-FR` ロケールと `fr-FR-DeniseNeural` 音声のコンテナー イメージ。    |
| `it-it-elsaneural-preview`                  | `it-IT` ロケールと `it-IT-ElsaNeural` 音声のコンテナー イメージ。      |
| `ja-jp-nanamineural-preview`                | `ja-JP` ロケールと `ja-JP-NanamiNeural` 音声のコンテナー イメージ。    |
| `ko-kr-sunhineural-preview`                 | `ko-KR` ロケールと `ko-KR-SunHiNeural` 音声のコンテナー イメージ。     |
| `pt-br-franciscaneural-preview`             | `pt-BR` ロケールと `pt-BR-FranciscaNeural` 音声のコンテナー イメージ。 |
| `zh-cn-xiaoxiaoneural-preview`              | `zh-CN` ロケールと `zh-CN-XiaoxiaoNeural` 音声のコンテナー イメージ。  |

## <a name="speech-language-detection"></a>音声言語検出

[音声言語検出][sp-lid]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/speechservices/` リポジトリ内にあり、`language-detection` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` です。

このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)の完全な一覧を確認することもできます。

| イメージ タグ                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>キー フレーズ抽出

コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/textanalytics/` リポジトリ内にあり、`keyphrase` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` です。

このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-version"></a>[最新バージョン](#tab/current)


| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[以前のバージョン](#tab/previous)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>テキスト言語の検出

[言語検出][ta-la]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/textanalytics/` リポジトリ内にあり、`language` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` です


このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)の完全な一覧を確認することもできます。

# <a name="latest-versions"></a>[最新バージョン](#tab/current)

| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[以前のバージョン](#tab/previous)


| イメージ タグ                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>センチメント分析

[感情分析][ta-se]コンテナー イメージは `mcr.microsoft.com` コンテナー レジストリ シンジケートにあります。 `azure-cognitive-services/textanalytics/` リポジトリ内にあり、`sentiment` という名前が付いています。 完全修飾コンテナー イメージ名は `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment` です

このコンテナー イメージには次のタグを利用できます。 また、[MCR でタグ](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)の完全な一覧を確認することもできます。

| イメージ タグ | Notes                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 感情分析 v3 (英語)               |
| `3.0-es`   | 感情分析 v3 (スペイン語)               |
| `3.0-fr`   | 感情分析 v3 (フランス語)                |
| `3.0-it`   | 感情分析 v3 (イタリア語)               |
| `3.0-de`   | 感情分析 v3 (ドイツ語)                |
| `3.0-zh`   | 感情分析 v3 (簡体字中国語)  |
| `3.0-zht`  | 感情分析 v3 (繁体字中国語) |
| `3.0-ja`   | 感情分析 v3 (日本語)              |
| `3.0-pt`   | 感情分析 v3 (ポルトガル語)            |
| `3.0-nl`   | 感情分析 v3 (オランダ語)                 |
| `2.1`    | 感情分析 v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
