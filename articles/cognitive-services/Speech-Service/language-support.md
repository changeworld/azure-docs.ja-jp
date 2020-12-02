---
title: 言語サポート - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスでは、音声翻訳に加え、音声からテキストへの変換とテキストから音声への変換のためのさまざまな言語がサポートされます。 この記事では、サービス機能によってサポートされている言語の包括的な一覧を示します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 27cf5fef027b1e4786c0ff77f9f673bafea15f98
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487271"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>音声サービスの言語と音声のサポート

言語サポートは、音声サービスの機能によって異なります。 次の表は、[音声テキスト変換](#speech-to-text)、[テキスト読み上げ](#text-to-speech)、および [音声翻訳](#speech-translation)サービスの言語サポートをまとめたものです。

## <a name="speech-to-text"></a>音声テキスト変換

Microsoft Speech SDK と REST API は、どちらも以下の言語 (ロケール) をサポートしています。 

精度を高めるために、 **"オーディオ + 人間" というラベルが付いたトランスクリプト** または **関連テキスト (文)** をアップロードすることにより、言語のサブセットに対してカスタマイズが提供されます。 カスタマイズの詳細については、[Custom Speech の利用の開始](./custom-speech-overview.md)に関するページを参照してください。

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Language                          | ロケール (BCP-47) | カスタマイズ                                   |
|-----------------------------------|--------|--------------------------------------------------|
|アラビア語 (バーレーン)、現代標準  |`ar-BH` | 言語モデル                                   |
|アラビア語 (エジプト)                     |`ar-EG` | 言語モデル                                   |
|アラビア語 (イラク)                      |`ar-IQ` | 言語モデル                                   |
|アラビア語 (ヨルダン)                    |`ar-JO` | 言語モデル                                   |
|アラビア語 (クウェート)                    |`ar-KW` | 言語モデル                                   |
|アラビア語 (レバノン)                   |`ar-LB` | 言語モデル                                   |
|アラビア語 (オマーン)                      |`ar-OM` | 言語モデル                                   |
|アラビア語 (カタール)                     |`ar-QA` | 言語モデル                                   |
|アラビア語 (サウジアラビア)              |`ar-SA` | 言語モデル                                   |
|アラビア語 (シリア)                     |`ar-SY` | 言語モデル                                   |
|アラビア語 (アラブ首長国連邦)      |`ar-AE` | 言語モデル                                   |
|ブルガリア語 (ブルガリア)               |`bg-BG` | 言語モデル                                   |
|カタルニア語 (スペイン)                    |`ca-ES` | 言語モデル                                   |
|中国語 (繁体字)   |`zh-HK` | 言語モデル                                   |
|中国語 (標準、簡体字)     |`zh-CN` | 音響モデル<br>言語モデル                 |
|中国語 (台湾標準中国語)       |`zh-TW` | 言語モデル                                   |
|クロアチア語 (クロアチア)                 |`hr-HR` | 言語モデル                                   |
|チェコ語 (チェコ共和国)             |`cs-CZ` | 言語モデル                                   |
|デンマーク語 (デンマーク)                   |`da-DK` | 言語モデル                                   |
|オランダ語 (オランダ)                |`nl-NL` | 言語モデル                                   |
|英語 (オーストラリア)                |`en-AU` | 音響モデル<br>言語モデル                 |
|英語 (カナダ)                   |`en-CA` | 音響モデル<br>言語モデル                 |
|英語 (香港)                |`en-HK` | 言語モデル                                   |
|英語 (インド)                    |`en-IN` | 音響モデル<br>言語モデル                 |
|英語 (アイルランド)                  |`en-IE` | 言語モデル                                   |
|英語 (ニュージーランド)              |`en-NZ` | 音響モデル<br>言語モデル                 |
|英語 (フィリピン)              |`en-PH` | 言語モデル                                   |
|英語 (シンガポール)                |`en-SG` | 言語モデル                                   |
|英語 (南アフリカ)             |`en-ZA` | 言語モデル                                   |
|英語 (イギリス)           |`en-GB` | 音響モデル<br>言語モデル<br>発音|
|英語 (米国)            |`en-US` | 音響モデル<br>言語モデル<br>発音|
|エストニア語 (エストニア)                  |`et-EE` | 言語モデル                                   |
|フィンランド語 (フィンランド)                  |`fi-FI` | 言語モデル                                   |
|フランス語 (カナダ)                    |`fr-CA` | 音響モデル<br>言語モデル                 |
|フランス語 (フランス)                    |`fr-FR` | 音響モデル<br>言語モデル<br>発音|
|ドイツ語 (ドイツ)                   |`de-DE` | 音響モデル<br>言語モデル<br>発音|
|ギリシャ語 (ギリシャ)                     |`el-GR` | 言語モデル                                   |
|グジャラート語 (インド)                  |`gu-IN` | 言語モデル                                   |
|ヒンディー語 (インド)                      |`hi-IN` | 音響モデル<br>言語モデル                 |
|ハンガリー語 (ハンガリー)                |`hu-HU` | 言語モデル                                   |
|アイルランド語 (アイルランド)                     |`ga-IE` | 言語モデル                                   |
|イタリア語 (イタリア)                    |`it-IT` | 音響モデル<br>言語モデル<br>発音|
|日本語 (日本)                   |`ja-JP` | 言語モデル                                   |
|韓国語 (韓国)                     |`ko-KR` | 言語モデル                                   |
|ラトビア語 (ラトビア)                   |`lv-LV` | 言語モデル                                   |
|リトアニア語 (リトアニア)             |`lt-LT` | 言語モデル                                   |
|マルタ語 (マルタ)                     |`mt-MT` | 言語モデル                                   |
|マラーティー語 (インド)                    |`mr-IN` | 言語モデル                                   |
|ノルウェー語 (ブークモール、ノルウェー)         |`nb-NO` | 言語モデル                                   |
|ポーランド語 (ポーランド)                    |`pl-PL` | 言語モデル                                   |
|ポルトガル語 (ブラジル)                |`pt-BR` | 音響モデル<br>言語モデル<br>発音|
|ポルトガル語 (ポルトガル)              |`pt-PT` | 言語モデル                                   |
|ルーマニア語 (ルーマニア)                 |`ro-RO` | 言語モデル                                   |
|ロシア語 (ロシア)                   |`ru-RU` | 音響モデル<br>言語モデル                 |
|スロバキア語 (スロバキア)                  |`sk-SK` | 言語モデル                                   |
|スロベニア語 (スロベニア)               |`sl-SI` | 言語モデル                                   |
|スペイン語 (アルゼンチン)                |`es-AR` | 言語モデル                                   |
|スペイン語 (ボリビア)                  |`es-BO` | 言語モデル                                   |
|スペイン語 (チリ)                    |`es-CL` | 言語モデル                                   |
|スペイン語 (コロンビア)                 |`es-CO` | 言語モデル                                   |
|スペイン語 (コスタリカ)               |`es-CR` | 言語モデル                                   |
|スペイン語 (キューバ)                     |`es-CU` | 言語モデル                                   |
|スペイン語 (ドミニカ共和国)       |`es-DO` | 言語モデル                                   |
|スペイン語 (エクアドル)                  |`es-EC` | 言語モデル                                   |
|スペイン語 (エルサルバドル)              |`es-SV` | 言語モデル                                   |
|スペイン語 (グアテマラ)                |`es-GT` | 言語モデル                                   |
|スペイン語 (ホンジュラス)                 |`es-HN` | 言語モデル                                   |
|スペイン語 (メキシコ)                   |`es-MX` | 音響モデル<br>言語モデル                 |
|スペイン語 (ニカラグア)                |`es-NI` | 言語モデル                                   |
|スペイン語 (パナマ)                   |`es-PA` | 言語モデル                                   |
|スペイン語 (パラグアイ)                 |`es-PY` | 言語モデル                                   |
|スペイン語 (ペルー)                     |`es-PE` | 言語モデル                                   |
|スペイン語 (プエルトリコ)              |`es-PR` | 言語モデル                                   |
|スペイン語 (スペイン)                    |`es-ES` | 音響モデル<br>言語モデル                 |
|スペイン語 (ウルグアイ)                  |`es-UY` | 言語モデル                                   |
|スペイン語 (米国)                      |`es-US` | 言語モデル                                   |
|スペイン語 (ベネズエラ)                |`es-VE` | 言語モデル                                   |
|スウェーデン語 (スウェーデン)                   |`sv-SE` | 言語モデル                                   |
|タミール語 (インド)                      |`ta-IN` | 言語モデル                                   |
|テルグ語 (インド)                     |`te-IN` | 言語モデル                                   |
|タイ語 (タイ)                    |`th-TH` | 言語モデル                                   |
|トルコ語 (トルコ)                   |`tr-TR` | 言語モデル                                   |

## <a name="text-to-speech"></a>テキスト読み上げ

Microsoft Speech SDK と REST API のどちらでもこれらの音声がサポートされ、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。 [音声/リスト API](rest-text-to-speech.md#get-a-list-of-voices) を使用して、特定のリージョン/エンドポイントごとにサポートされている言語と音声の完全な一覧を取得することもできます。 

> [!IMPORTANT]
> 価格は、標準音声、カスタム音声、ニューラル音声ごとに異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)のページを参照してください。

### <a name="neural-voices"></a>ニューラル音声

ニューラル テキスト読み上げは、ディープ ニューラル ネットワークを利用した新しい種類の音声合成です。 ニューラル音声を使用した場合、合成音声は人間の録音とほとんど区別がつきません。

ニューラル音声を使用すると、チャットボットや音声アシスタントとの対話をより自然で魅力的なものにできます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。

| Language | Locale | 性別 | 音声名 | スタイルのサポート |
|---|---|---|---|---|
| アラビア語 (エジプト) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 全般 |
| アラビア語 (サウジアラビア) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 全般 |
| ブルガリア語 (ブルガリア) | `bg-BG` | Female | `bg-BG-KalinaNeural` <sup>[新規作成]</sup> | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 全般 |
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 全般 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 子供の音声、ストーリーのナレーション向けに最適化 |
| 中国語 (標準、簡体字) | `zh-CN` | Male   | `zh-CN-YunyangNeural` | ニュースの読み取り向けに最適化<br /> [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、複数の音声スタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Male   | `zh-CN-YunyeNeural` | ストーリーのナレーション向けに最適化 |
| 中国語 (台湾標準中国語) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 全般 |
| クロアチア語 (クロアチア) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` <sup>[新規作成]</sup> | 全般 |
| チェコ語 (チェコ) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` <sup>[新規作成]</sup>    | 全般 |
| デンマーク語 (デンマーク) | `da-DK` | Female | `da-DK-ChristelNeural` | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 全般 |
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-NatashaNeural` | 全般 |
| 英語 (オーストラリア) | `en-AU` | Male   | `en-AU-WilliamNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (カナダ) | `en-CA` | Female | `en-CA-ClaraNeural` | 全般 |
| 英語 (インド) | `en-IN` | Female | `en-IN-NeerjaNeural` | 全般 |
| 英語 (アイルランド) | `en-IE` | Female | `en-IE-EmilyNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-LibbyNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-MiaNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Male | `en-GB-RyanNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-AriaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 英語 (米国) | `en-US` | Male | `en-US-GuyNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-JennyNeural` <sup>[新規作成]</sup> | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-NooraNeural` | 全般 |
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 全般 |
| フランス語 (カナダ) | `fr-CA` | Male | `fr-CA-JeanNeural` <sup>[新規作成]</sup> | 全般 |
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 全般 |
| フランス語 (フランス) | `fr-FR` | Male | `fr-FR-HenriNeural` <sup>[新規作成]</sup> | 全般 |
| フランス語 (スイス) | `fr-CH` | Female | `fr-CH-ArianeNeural` <sup>[新規作成]</sup> | 全般 |
| ドイツ語 (オーストリア) | `de-AT` | Female | `de-AT-IngridNeural` <sup>[新規作成]</sup> | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Female | `de-DE-KatjaNeural` | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Male | `de-DE-ConradNeural` <sup>[新規作成]</sup> | 全般 |
| ドイツ語 (スイス) | `de-CH` | Female | `de-CH-LeniNeural` <sup>[新規作成]</sup> | 全般 |
| ギリシャ語 (ギリシャ) | `el-GR` | Female | `el-GR-AthinaNeural` <sup>[新規作成]</sup> | 全般 |
| ヘブライ語 (イスラエル) | `he-IL` | Female | `he-IL-HilaNeural` <sup>[新規作成]</sup> | 全般 |
| ヒンディー語 (インド) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 全般 |
| ハンガリー語 (ハンガリー) | `hu-HU` | Female | `hu-HU-NoemiNeural` <sup>[新規作成]</sup> | 全般 |
| インドネシア語 (インドネシア) | `id-ID` | Male | `id-ID-ArdiNeural` <sup>[新規作成]</sup> | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-ElsaNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-IsabellaNeural` <sup>[新規作成]</sup> | 全般 |
| イタリア語 (イタリア) | `it-IT` | Male | `it-IT-DiegoNeural` <sup>[新規作成]</sup> | 全般 |
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 全般 |
| 日本語 (日本) | `ja-JP` | Male | `ja-JP-KeitaNeural` <sup>[新規作成]</sup> | 全般 |
| 韓国語 (韓国) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 全般 |
| 韓国語 (韓国) | `ko-KR` | Male | `ko-KR-InJoonNeural` <sup>[新規作成]</sup> | 全般 |
| マレー語 (マレーシア) | `ms-MY` | Female | `ms-MY-YasminNeural` <sup>[新規作成]</sup> | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-IselinNeural` | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 全般 |
| ポルトガル語 (ブラジル) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| ポルトガル語 (ブラジル) | `pt-BR` | Male | `pt-BR-AntonioNeural` <sup>[新規作成]</sup> | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 全般 |
| ルーマニア語 (ルーマニア) | `ro-RO` | Female | `ro-RO-AlinaNeural` <sup>[新規作成]</sup> | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 全般 |
| スロバキア語 (スロバキア) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` <sup>[新規作成]</sup> | 全般 |
| スロベニア語 (スロベニア) | `sl-SI` | Female | `sl-SI-PetraNeural` <sup>[新規作成]</sup> | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Female | `es-MX-DaliaNeural` | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Male | `es-MX-JorgeNeural` <sup>[新規作成]</sup> | 全般 |
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-ElviraNeural` | 全般 |
| スペイン語 (スペイン) | `es-ES` | Male | `es-ES-AlvaroNeural` <sup>[新規作成]</sup> | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 全般 |
| タミール語 (インド) | `ta-IN` | Female | `ta-IN-PallaviNeural` <sup>[新規作成]</sup> | 全般 |
| テルグ語 (インド) | `te-IN` | Female | `te-IN-ShrutiNeural` <sup>[新規作成]</sup> | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-AcharaNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-PremwadeeNeural` <sup>[新規作成]</sup> | 全般 |
| トルコ語 (トルコ) | `tr-TR` | Female | `tr-TR-EmelNeural` | 全般 |
| ベトナム語 (ベトナム) | `vi-VN` <sup>[新規作成]</sup> | Female | `vi-VN-HoaiMyNeural` | 全般|

#### <a name="neural-voices-in-preview"></a>プレビュー段階のニューラル音声

次のニューラル音声は、パブリック プレビュー段階です。 

| Language                         | Locale  | 性別 | 音声名                             | スタイルのサポート |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 中国語 (標準、簡体字) | `zh-CN` | Male   | `zh-CN-YunxiNeural` <sup>[新規作成]</sup> | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaohanNeural` <sup>[新規作成]</sup> | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` <sup>[新規作成]</sup> | 全般、複数のロールプレイと[スタイルが SSML の使用により使用可能](speech-synthesis-markup.md#adjust-speaking-styles) |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaomoNeural` <sup>[新規作成]</sup> | 全般、複数のロールプレイと[スタイルが SSML の使用により使用可能](speech-synthesis-markup.md#adjust-speaking-styles) |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` <sup>[新規作成]</sup> | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、高齢者の音声、複数のスタイルを使用可能 |
| エストニア語 (エストニア) | `et-EE` | Female | `et-EE-AnuNeural` <sup>[新規作成]</sup> | 全般 |
| ゲール語 (アイルランド) | `ga-IE` | Female | `ga-IE-OrlaNeural` <sup>[新規作成]</sup> | 全般 |
| リトアニア語 (リトアニア) | `lt-LT` | Female | `lt-LT-OnaNeural` <sup>[新規作成]</sup> | 全般 |
| ラトビア語 (ラトビア) | `lv-LV` | Female | `lv-LV-EveritaNeural` <sup>[新規作成]</sup> | 全般 |
| マルタ語 (マルタ) | `mt-MT` | Female | `mt-MT-GraceNeural` <sup>[新規作成]</sup> | 全般 |

> [!IMPORTANT]
> パブリック プレビュー段階の音声は、3 つのサービス リージョン (米国東部、西ヨーロッパ、東南アジア) でのみ使用できます。

リージョン別の提供状況の詳細については、[リージョン](regions.md#standard-and-neural-voices)に関するページを参照してください。

ニューラル音声 (話し方など) を構成および調整する方法については、「[音声合成マークアップ言語](speech-synthesis-markup.md#adjust-speaking-styles)」を参照してください。

> [!IMPORTANT]
> `en-US-JessaNeural` 音声が `en-US-AriaNeural` に変更されました。 前に "Jessa" を使用していた場合は、"Aria" に変換します。

> [!TIP]
> 音声合成要求では、"Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" のような完全なサービス名マッピングを引き続き使用できます。

### <a name="standard-voices"></a>標準音声

テキストから合成音声への変換用に、45 を超える言語とロケールで 75 を超える標準的な音声が用意されています。 リージョン別の提供状況の詳細については、[リージョン](regions.md#standard-and-neural-voices)に関するページを参照してください。

| Language | ロケール (BCP-47) | 性別 | 音声名 |
|--|--|--|--|
| アラビア語 (アラブ) | `ar-EG` | Female | `ar-EG-Hoda`|
| アラビア語 (サウジアラビア) | `ar-SA` | Male | `ar-SA-Naayf`|
| ブルガリア語 (ブルガリア) | `bg-BG` | Male | `bg-BG-Ivan`|
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| 中国語 (繁体字) | `zh-HK` | Male | `zh-HK-Danny`|
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-Kangkang`|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Female | `zh-TW-Yating`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| クロアチア語 (クロアチア) | `hr-HR` | Male | `hr-HR-Matej`|
| チェコ語 (チェコ共和国) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| デンマーク語 (デンマーク) | `da-DK` | Female | `da-DK-HelleRUS`|
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-Catherine`|
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-HayleyRUS`|
| 英語 (カナダ) | `en-CA` | Female | `en-CA-HeatherRUS`|
| 英語 (カナダ) | `en-CA` | Female | `en-CA-Linda`|
| 英語 (インド) | `en-IN` | Female | `en-IN-Heera`|
| 英語 (インド) | `en-IN` | Female | `en-IN-PriyaRUS`|
| 英語 (インド) | `en-IN` | Male | `en-IN-Ravi`|
| 英語 (アイルランド) | `en-IE` | Male | `en-IE-Sean`|
| 英語 (イギリス) | `en-GB` | Male | `en-GB-George`|
| 英語 (イギリス) | `en-GB` | Female | `en-GB-HazelRUS`|
| 英語 (イギリス) | `en-GB` | Female | `en-GB-Susan`|
| 英語 (米国) | `en-US` | Male | `en-US-BenjaminRUS`|
| 英語 (米国) | `en-US` | Male | `en-US-GuyRUS`|
| 英語 (米国) | `en-US` | Female | `en-US-JessaRUS`|
| 英語 (米国) | `en-US` | Female | `en-US-ZiraRUS`|
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-Caroline`|
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-Julie`|
| フランス語 (フランス) | `fr-FR` | Male | `fr-FR-Paul`|
| フランス語 (スイス) | `fr-CH` | Male | `fr-CH-Guillaume`|
| ドイツ語 (オーストリア) | `de-AT` | Male | `de-AT-Michael`|
| ドイツ語 (ドイツ) | `de-DE` | Female | `de-DE-HeddaRUS`|
| ドイツ語 (ドイツ) | `de-DE` | Male | `de-DE-Stefan`|
| ドイツ語 (スイス) | `de-CH` | Male | `de-CH-Karsten`|
| ギリシャ語 (ギリシャ) | `el-GR` | Male | `el-GR-Stefanos`|
| ヘブライ語 (イスラエル) | `he-IL` | Male | `he-IL-Asaf`|
| ヒンディー語 (インド) | `hi-IN` | Male | `hi-IN-Hemant`|
| ヒンディー語 (インド) | `hi-IN` | Female | `hi-IN-Kalpana`|
| ハンガリー語 (ハンガリー) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| インドネシア語 (インドネシア) | `id-ID` | Male | `id-ID-Andika`|
| イタリア語 (イタリア) | `it-IT` | Male | `it-IT-Cosimo`|
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-LuciaRUS`|
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-Ayumi`|
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| 日本語 (日本) | `ja-JP` | Male | `ja-JP-Ichiro`|
| 韓国語 (韓国) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| マレー語 (マレーシア) | `ms-MY` | Male | `ms-MY-Rizwan`|
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| ポルトガル語 (ブラジル) | `pt-BR` | Male | `pt-BR-Daniel`|
| ポルトガル語 (ブラジル) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| ルーマニア語 (ルーマニア) | `ro-RO` | Male | `ro-RO-Andrei`|
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-Irina`|
| ロシア語 (ロシア) | `ru-RU` | Male | `ru-RU-Pavel`|
| スロバキア語 (スロバキア) | `sk-SK` | Male | `sk-SK-Filip`|
| スロベニア語 (スロベニア) | `sl-SI` | Male | `sl-SI-Lado`|
| スペイン語 (メキシコ) | `es-MX` | Female | `es-MX-HildaRUS`|
| スペイン語 (メキシコ) | `es-MX` | Male | `es-MX-Raul`|
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-HelenaRUS`|
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-Laura`|
| スペイン語 (スペイン) | `es-ES` | Male | `es-ES-Pablo`|
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| タミール語 (インド) | `ta-IN` | Male | `ta-IN-Valluvar`|
| テルグ語 (インド) | `te-IN` | Female | `te-IN-Chitra`|
| タイ語 (タイ) | `th-TH` | Male | `th-TH-Pattara`|
| トルコ語 (トルコ) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| ベトナム語 (ベトナム) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa` 音声が `en-US-Aria` に変更されました。 前に "Jessa" を使用していた場合は、"Aria" に変換します。

> [!TIP]
> 音声合成要求では、"Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" のような完全なサービス名マッピングを引き続き使用できます。

### <a name="customization"></a>カスタマイズ

音声のカスタマイズは、`de-DE`、`en-GB`、`en-IN`、`en-US`、`es-MX`、`fr-FR`、`it-IT`、`pt-BR`、および `zh-CN` に使用できます。 カスタム音声モデルをトレーニングする必要があるトレーニング データと一致する適切なロケールを選択します。 たとえば、持っている録音データが英国アクセントの英語で話されている場合は、`en-GB` を選択します。

> [!NOTE]
> Custom Voice では、中国語と英語のバイリンガルを除き、バイリンガル モデル トレーニングはサポートされていません。 英語も話すことができる中国人の音声をトレーニングする場合は、[Chinese-English bilingual]\(中国語 - 英語のバイリンガル\) を選択します。 すべてのロケールの音声トレーニングは、任意のサイズのトレーニング データから始めることができる `en-US` と `zh-CN` を除き、2,000 以上の発話のデータ セットから始まります。

## <a name="speech-translation"></a>音声翻訳

**Speech Translation** API では、音声を音声に翻訳し、音声をテキストに翻訳するために、さまざまな言語をサポートしています。 ソース言語は、音声テキスト変換言語の表に常に含まれている必要があります。 使用可能なターゲット言語は、何に翻訳するか (音声かテキストか) によって決まります。 受信した音声は [60 言語](https://www.microsoft.com/translator/business/languages/)以上に翻訳できます。 一部の言語は[音声合成](language-support.md#text-languages)に利用できます。

### <a name="text-languages"></a>テキスト言語

| テキスト言語           | 言語コード |
|:------------------------|:-------------:|
| アフリカーンス語               | `af`          |
| アラビア語                  | `ar`          |
| ベンガル語                  | `bn`          |
| ボスニア語 (ラテン)         | `bs`          |
| ブルガリア語               | `bg`          |
| 広東語 (繁体字) | `yue`         |
| カタロニア語                 | `ca`          |
| 簡体中国語      | `zh-Hans`     |
| 中国語 (繁体字)     | `zh-Hant`     |
| クロアチア語                | `hr`          |
| チェコ語                   | `cs`          |
| デンマーク語                  | `da`          |
| オランダ語                   | `nl`          |
| 英語                 | `en`          |
| エストニア語                | `et`          |
| フィジー語                  | `fj`          |
| フィリピン語                | `fil`         |
| フィンランド語                 | `fi`          |
| フランス語                  | `fr`          |
| ドイツ語                  | `de`          |
| ギリシャ語                   | `el`          |
| グジャラート語                | `gu`          |
| ハイチ・クレオール語          | `ht`          |
| ヘブライ語                  | `he`          |
| ヒンディー語                   | `hi`          |
| ミャオ語               | `mww`         |
| ハンガリー語               | `hu`          |
| インドネシア語              | `id`          |
| アイルランド語                   | `ga`          |
| イタリア語                 | `it`          |
| 日本語                | `ja`          |
| カンナダ語                 | `kn`          |
| スワヒリ語               | `sw`          |
| クリンゴン語                 | `tlh-Latn`    |
| クリンゴン語 (plqaD)         | `tlh-Piqd`    |
| 韓国語                  | `ko`          |
| ラトビア語                 | `lv`          |
| リトアニア語              | `lt`          |
| マダガスカル語                | `mg`          |
| マレー語                   | `ms`          |
| マラヤーラム語               | `ml`          |
| マルタ語                 | `mt`          |
| マオリ語                   | `mi`          |
| マラーティー語                 | `mr`          |
| ノルウェー語               | `nb`          |
| ペルシャ語                 | `fa`          |
| ポーランド語                  | `pl`          |
| ポルトガル語 (ブラジル)     | `pt-br`       |
| ポルトガル語 (ポルトガル)   | `pt-pt`       |
| パンジャーブ語                 | `pa`          |
| オトミ語         | `otq`         |
| ルーマニア語                | `ro`          |
| ロシア語                 | `ru`          |
| サモア語                  | `sm`          |
| セルビア語 (キリル)      | `sr-Cyrl`     |
| セルビア語 (ラテン)         | `sr-Latn`     |
| スロバキア語                  | `sk`          |
| スロベニア語               | `sl`          |
| スペイン語                 | `es`          |
| スウェーデン語                 | `sv`          |
| タヒチ語                | `ty`          |
| タミル語                   | `ta`          |
| テルグ語                  | `te`          |
| タイ語                    | `th`          |
| トンガ語                  | `to`          |
| トルコ語                 | `tr`          |
| ウクライナ語               | `uk`          |
| ウルドゥ語                    | `ur`          |
| ベトナム語              | `vi`          |
| ウェールズ語                   | `cy`          |
| ユカテコ語            | `yua`         |

## <a name="speaker-recognition"></a>Speaker Recognition

さまざまな Speaker Recognition API でサポートされる言語については、次の表を参照してください。 Speaker Recognition の詳細については、[概要](speaker-recognition-overview.md)に関するページを参照してください。

| Language | ロケール (BCP-47) | テキスト依存の認証 | テキストに依存しない認証 | テキストに依存しない識別 |
|----|----|----|----|----|
|英語 (米国)  |  ja-JP  |  はい  |  はい  |  はい |
|中国語 (標準、簡体字) | zh-CN     |     該当なし |     はい |     はい|
|英語 (オーストラリア)     | en-AU     | 該当なし     | はい     | はい|
|英語 (カナダ)     | en-CA     | 該当なし |     はい |     はい|
|英語 (英国)     | en-GB     | 該当なし     | はい     | はい|
|フランス語 (カナダ)     | fr-CA     | 該当なし     | はい |     はい|
|フランス語 (フランス)     | fr-FR     | 該当なし     | はい     | はい|
|ドイツ語 (ドイツ)     | de-DE     | 該当なし     | はい     | はい|
|イタリア語 | it-IT     |     該当なし     | はい |     はい|
|日本語     | ja-JP | 該当なし     | はい     | はい|
|ポルトガル語 (ブラジル) | pt-BR |     該当なし |     はい |     はい|
|スペイン語 (メキシコ)     | es-MX     | 該当なし |     はい |     はい|
|スペイン語 (スペイン)     | es-ES | 該当なし     | はい |     はい|

## <a name="next-steps"></a>次のステップ

* [無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください
* [C# で音声を認識する方法を確認する](./get-started-speech-to-text.md?pivots=programming-language-chsarp)