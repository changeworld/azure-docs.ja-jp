---
title: 言語サポート - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスでは、音声翻訳に加え、音声からテキストへの変換とテキストから音声への変換のためのさまざまな言語がサポートされます。 この記事では、サービス機能によってサポートされている言語の包括的な一覧を示します。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: eur
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 1ae10bb589816ae40a033487fb5548e1bc1abeba
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549529"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>音声サービスの言語と音声のサポート

言語サポートは、音声サービスの機能によって異なります。 以下の表に、[音声テキスト変換](#speech-to-text)、[テキスト読み上げ](#text-to-speech)、[音声翻訳](#speech-translation)、および[話者認識](#speaker-recognition)サービスの言語サポートを示します。

## <a name="speech-to-text"></a>音声テキスト変換

Microsoft Speech SDK と REST API は、どちらも以下の言語 (ロケール) をサポートしています。 

精度を高めるために、 **"オーディオ + 人間" というラベルが付いたトランスクリプト** または **関連テキスト (文)** をアップロードすることにより、言語のサブセットに対してカスタマイズが提供されます。 **オーディオ + 人間というラベルが付いたトランスクリプト** を使用した音響モデルのカスタマイズのサポートは、下に示す特定の基本モデルに限定されます。 その他の基本モデルおよび言語では、以下と同様に、カスタム モデルをトレーニングするために、トランスクリプトのテキストのみが使用されます: **関連するテキスト: (文)** をアップロードすることにより、言語のサブセットに対してカスタマイズが提供されます。 カスタマイズの詳細については、[Custom Speech の利用の開始](./custom-speech-overview.md)に関するページを参照してください。

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Language                 | ロケール (BCP-47) | カスタマイズ  | [言語識別](how-to-automatic-language-detection.md) | [発音評価](how-to-pronunciation-assessment.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|--------------------------|
| アラビア語 (アルジェリア)                   | `ar-DZ` | Text                                   |                           |                          |
| アラビア語 (バーレーン)、現代標準  | `ar-BH` | Text                                   |                           |                          |
| アラビア語 (エジプト)                     | `ar-EG` | Text                                   | はい                          |                          |
| アラビア語 (イラク)                      | `ar-IQ` | テキスト                                   |                           |                          |
| アラビア語 (イスラエル)                    | `ar-IL` | テキスト                                   |                           |                          |
| アラビア語 (ヨルダン)                    | `ar-JO` | テキスト                                   |                           |                          |
| アラビア語 (クウェート)                    | `ar-KW` | テキスト                                   |                           |                          |
| アラビア語 (レバノン)                   | `ar-LB` | テキスト                                   |                           |                          |
| アラビア語 (リビア)                     | `ar-LY` | Text                                   |                           |                          |
| アラビア語 (モロッコ)                   | `ar-MA` | テキスト                                   |                           |                          |
| アラビア語 (オマーン)                      | `ar-OM` | テキスト                                   |                           |                          |
| アラビア語 (カタール)                     | `ar-QA` | テキスト                                   |                           |                          |
| アラビア語 (サウジアラビア)              | `ar-SA` | Text                                   |                           |                          |
| アラビア語 (パレスチナ自治政府)     | `ar-PS` | テキスト                                   |                           |                          |
| アラビア語 (シリア)                     | `ar-SY` | Text                                   |                           |                          |
| アラビア語 (チュニジア)                   | `ar-TN` | Text                                   |                           |                          |
| アラビア語 (アラブ首長国連邦)      | `ar-AE` | テキスト                                   |                           |                          |
| アラビア語 (イエメン)                     | `ar-YE` | テキスト                                   |                           |                          |
| ブルガリア語 (ブルガリア)               | `bg-BG` | テキスト                                   |                           |                          |
| カタルニア語 (スペイン)                    | `ca-ES` | Text<br>発音                  | はい                          |                          |
| 中国語 (繁体字)   | `zh-HK` | オーディオ (20201015)<br>Text                 |        はい                   |                          |
| 中国語 (標準、簡体字)     | `zh-CN` | オーディオ (20200910)<br>Text                 |     はい                      | はい                         |
| 中国語 (台湾標準中国語)       | `zh-TW` | オーディオ (20190701、20201015)<br>Text                 |           はい                |                          |
| クロアチア語 (クロアチア)                 | `hr-HR` | テキスト<br>発音                  |                           |                          |
| チェコ語 (チェコ)             | `cs-CZ` | Text<br>発音                  |                           |                          |
| デンマーク語 (デンマーク)                   | `da-DK` | Text<br>発音                  | はい                          |                          |
| オランダ語 (オランダ)                | `nl-NL` | オーディオ (20201015)<br>Text<br>発音|    はい                       |                          |
| 英語 (オーストラリア)                | `en-AU` | オーディオ (20201019)<br>Text<br>発音| はい                          |                          |
| 英語 (カナダ)                   | `en-CA` | オーディオ (20201019)<br>Text<br>発音| はい                          |                          |
| 英語 (ガーナ)                    | `en-GH` | テキスト<br>発音                  |                           |                          |
| 英語 (香港)                | `en-HK` | テキスト<br>発音                  |                           |                          |
| 英語 (インド)                    | `en-IN` | オーディオ (20200923)<br>Text<br>発音 |                          |                          |
| 英語 (アイルランド)                  | `en-IE` | テキスト<br>発音                  |                           |                          |
| 英語 (ケニア)                    | `en-KE` | テキスト<br>発音                  |                           |                          |
| 英語 (ニュージーランド)              | `en-NZ` | オーディオ (20201019)<br>Text<br>発音 |                          |                          |
| 英語 (ナイジェリア)                  | `en-NG` | テキスト<br>発音                  |                           |                          |
| 英語 (フィリピン)              | `en-PH` | テキスト<br>発音                  |                           |                          |
| 英語 (シンガポール)                | `en-SG` | テキスト<br>発音                  |                           |                          |
| 英語 (南アフリカ)             | `en-ZA` | テキスト<br>発音                  |                           |                          |
| 英語 (タンザニア)                 | `en-TZ` | テキスト<br>発音                  |                           |                          |
| 英語 (イギリス)           | `en-GB` | オーディオ (20201019)<br>Text<br>発音| はい                          | はい                         |
| 英語 (米国)            | `en-US` | Audio (20201019、20210223)<br>テキスト<br>発音| はい                          | はい                         |
| エストニア語 (エストニア)                  | `et-EE` | テキスト<br>発音                  |                           |                          |
| フィリピノ語 (フィリピン)             | `fil-PH`| テキスト<br>発音                  |                           |                          |
| フィンランド語 (フィンランド)                  | `fi-FI` | Text<br>発音                  |     はい                      |                          |
| フランス語 (カナダ)                    | `fr-CA` | オーディオ (20201015)<br>Text<br>発音|     はい                      |                          |
| フランス語 (フランス)                    | `fr-FR` | オーディオ (20201015)<br>Text<br>発音|      はい                     |                          |
| フランス語 (スイス)               | `fr-CH` | テキスト<br>発音                  |                           |                          |
| ドイツ語 (オーストリア)                   | `de-AT` | テキスト<br>発音                  |                           |                          |
| ドイツ語 (スイス)               | `de-CH` | テキスト<br>発音                  |                           |                          |
| ドイツ語 (ドイツ)                   | `de-DE` | オーディオ (20190701、20200619、20201127)<br>テキスト<br>発音|  はい                         |                          |
| ギリシャ語 (ギリシャ)                     | `el-GR` | テキスト                                   |  はい                         |                          |
| グジャラート語 (インド)                  | `gu-IN` | テキスト                                   |                           |                          |
| ヘブライ語 (イスラエル)                    | `he-IL` | テキスト                                   |                           |                          |
| ヒンディー語 (インド)                      | `hi-IN` | オーディオ (20200701)<br>Text                 |     はい                      |                          |
| ハンガリー語 (ハンガリー)                | `hu-HU` | テキスト<br>発音                  |                           |                          |
| インドネシア語 (インドネシア)             | `id-ID` | テキスト<br>発音                  |                           |                          |
| アイルランド語 (アイルランド)                    | `ga-IE` | テキスト<br>発音                  |                           |                          |
| イタリア語 (イタリア)                    | `it-IT` | オーディオ (20201016)<br>テキスト<br>発音|      はい                     |                          |
| 日本語 (日本)                   | `ja-JP` | Text                                   |      はい                     |                          |
| カンナダ語 (インド)                   | `kn-IN` | Text                                   |                           |                          |
| 韓国語 (韓国)                     | `ko-KR` | オーディオ (20201015)<br>Text                 |      はい                     |                          |
| ラトビア語 (ラトビア)                   | `lv-LV` | テキスト<br>発音                  |                           |                          |
| リトアニア語 (リトアニア)             | `lt-LT` | テキスト<br>発音                  |                           |                          |
| マレー語 (マレーシア)                   | `ms-MY` | テキスト                                   |                           |                          |
| マルタ語 (マルタ)                    | `mt-MT` | テキスト                                   |                           |                          |
| マラーティー語 (インド)                    | `mr-IN` | テキスト                                   |                           |                          |
| ノルウェー語 (ブークモール、ノルウェー)         | `nb-NO` | Text                                   |     はい                      |                          |
| ペルシア語 (イラン)                     | `fa-IR` | Text                                   |                           |                          |
| ポーランド語 (ポーランド)                    | `pl-PL` | Text<br>発音                  |       はい                    |                          |
| ポルトガル語 (ブラジル)                | `pt-BR` | オーディオ (20190620、20201015)<br>テキスト<br>発音|          はい                 |                          |
| ポルトガル語 (ポルトガル)              | `pt-PT` | Text<br>発音                  |             はい              |                          |
| ルーマニア語 (ルーマニア)                 | `ro-RO` | テキスト<br>発音                  |  はい                         |                          |
| ロシア語 (ロシア)                   | `ru-RU` | オーディオ (20200907)<br>Text                 |                はい           |                          |
| スロバキア語 (スロバキア)                  | `sk-SK` | テキスト<br>発音                  |                           |                          |
| スロベニア語 (スロベニア)               | `sl-SI` | テキスト<br>発音                  |                           |                          |
| スペイン語 (アルゼンチン)                | `es-AR` | テキスト<br>発音                  |                           |                          |
| スペイン語 (ボリビア)                  | `es-BO` | テキスト<br>発音                  |                           |                          |
| スペイン語 (チリ)                    | `es-CL` | テキスト<br>発音                  |                           |                          |
| スペイン語 (コロンビア)                 | `es-CO` | テキスト<br>発音                  |                           |                          |
| スペイン語 (コスタリカ)               | `es-CR` | テキスト<br>発音                  |                           |                          |
| スペイン語 (キューバ)                     | `es-CU` | テキスト<br>発音                  |                           |                          |
| スペイン語 (ドミニカ共和国)       | `es-DO` | テキスト<br>発音                  |                           |                          |
| スペイン語 (エクアドル)                  | `es-EC` | テキスト<br>発音                  |                           |                          |
| スペイン語 (エルサルバドル)              | `es-SV` | テキスト<br>発音                  |                           |                          |
| スペイン語 (赤道ギニア)        | `es-GQ` | テキスト                                   |                           |                          |
| スペイン語 (グアテマラ)                | `es-GT` | テキスト<br>発音                  |                           |                          |
| スペイン語 (ホンジュラス)                 | `es-HN` | テキスト<br>発音                  |                           |                          |
| スペイン語 (メキシコ)                   | `es-MX` | オーディオ (20200907)<br>Text<br>発音|    はい                       |                          |
| スペイン語 (ニカラグア)                | `es-NI` | テキスト<br>発音                  |                           |                          |
| スペイン語 (パナマ)                   | `es-PA` | テキスト<br>発音                  |                           |                          |
| スペイン語 (パラグアイ)                 | `es-PY` | テキスト<br>発音                  |                           |                          |
| スペイン語 (ペルー)                     | `es-PE` | テキスト<br>発音                  |                           |                          |
| スペイン語 (プエルトリコ)              | `es-PR` | テキスト<br>発音                  |                           |                          |
| スペイン語 (スペイン)                    | `es-ES` | オーディオ (20201015)<br>Text<br>発音|  はい                         |                          |
| スペイン語 (ウルグアイ)                  | `es-UY` | テキスト<br>発音                  |                           |                          |
| スペイン語 (米国)                      | `es-US` | テキスト<br>発音                  |                           |                          |
| スペイン語 (ベネズエラ)                | `es-VE` | テキスト<br>発音                  |                           |                          |
| スワヒリ語 (ケニア)                    | `sw-KE` | テキスト                                   |                           |                          |
| スウェーデン語 (スウェーデン)                   | `sv-SE` | Text<br>発音                  |   はい                        |                          |
| タミール語 (インド)                      | `ta-IN` | テキスト                                   |                           |                          |
| テルグ語 (インド)                     | `te-IN` | テキスト                                   |                           |                          |
| タイ語 (タイ)                    | `th-TH` | Text                                   |      はい                     |                          |
| トルコ語 (トルコ)                   | `tr-TR` | テキスト                                   |                           |                          |
| ベトナム語 (ベトナム)               | `vi-VN` | テキスト                                   |                           |                          |

## <a name="text-to-speech"></a>テキスト読み上げ

Microsoft Speech SDK と REST API のどちらでもこれらの音声がサポートされ、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。 [voices/list API](rest-text-to-speech.md#get-a-list-of-voices) を使用して、特定のリージョン/エンドポイントごとにサポートされている言語と音声の完全な一覧を取得することもできます。 

> [!IMPORTANT]
> 価格は、標準音声、カスタム音声、ニューラル音声ごとに異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)のページを参照してください。

### <a name="neural-voices"></a>ニューラル音声

ニューラル テキスト読み上げは、ディープ ニューラル ネットワークを利用した新しい種類の音声合成です。 ニューラル音声を使用した場合、合成音声は人間の録音とほとんど区別がつきません。

ニューラル音声を使用すると、チャットボットや音声アシスタントとの対話をより自然で魅力的なものにできます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。

> [!NOTE]
> ニューラル音声は、24 khz サンプル レートを使用するサンプルから作成されます。
> すべての音声は、合成時に他のサンプル レートにアップサンプリングまたはダウンサンプリングできます。

| Language | Locale | 性別 | 音声名 | スタイルのサポート |
|---|---|---|---|---|
| アフリカーンス語 (南アフリカ) | `af-ZA` | Female | `af-ZA-AdriNeural` <sup>[新規作成]</sup>  | 全般 |
| アフリカーンス語 (南アフリカ) | `af-ZA` | Male | `af-ZA-WillemNeural` <sup>[新規作成]</sup>  | 全般 |
| アムハラ語 (エチオピア) | `am-ET` | Female | `am-ET-MekdesNeural` <sup>[新規作成]</sup>  | 全般 |
| アムハラ語 (エチオピア) | `am-ET` | Male | `am-ET-AmehaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (アルジェリア) | `ar-DZ` | Female | `ar-DZ-AminaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (アルジェリア) | `ar-DZ` | Male | `ar-DZ-IsmaelNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (バーレーン) | `ar-BH` | Female | `ar-BH-LailaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (バーレーン) | `ar-BH` | Male | `ar-BH-AliNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (エジプト) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 全般 |
| アラビア語 (エジプト) | `ar-EG` | Male | `ar-EG-ShakirNeural` | 全般 |
| アラビア語 (イラク) | `ar-IQ` | Female | `ar-IQ-RanaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (イラク) | `ar-IQ` | Male | `ar-IQ-BasselNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (ヨルダン) | `ar-JO` | Female | `ar-JO-Sana Neural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (ヨルダン) | `ar-JO` | Male | `ar-JO-Taim Neural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (クウェート) | `ar-KW` | Female | `ar-KW-NouraNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (クウェート) | `ar-KW` | Male | `ar-KW-FahedNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (リビア) | `ar-LY` | Female | `ar-LY-ImanNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (リビア) | `ar-LY` | Male | `ar-LY-OmarNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (モロッコ) | `ar-MA` | Female | `ar-MA-MounaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (モロッコ) | `ar-MA` | Male | `ar-MA-JamalNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (カタール) | `ar-QA` | Female | `ar-QA-AmalNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (カタール) | `ar-QA` | Male | `ar-QA-MoazNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (サウジアラビア) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 全般 |
| アラビア語 (サウジアラビア) | `ar-SA` | Male | `ar-SA-HamedNeural` | 全般 |
| アラビア語 (シリア) | `ar-SY` | Female | `ar-SY-AmanyNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (シリア) | `ar-SY` | Male | `ar-SY-LaithNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (チュニジア) | `ar-TN` | Female | `ar-TN-ReemNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (チュニジア) | `ar-TN` | Male | `ar-TN-HediNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (アラブ首長国連邦) | `ar-AE` | Female | `ar-AE-FatimaNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (アラブ首長国連邦) | `ar-AE` | Male | `ar-AE-HamdanNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (イエメン) | `ar-YE` | Female | `ar-YE-MaryamNeural` <sup>[新規作成]</sup>  | 全般 |
| アラビア語 (イエメン) | `ar-YE` | Male | `ar-YE-SalehNeural` <sup>[新規作成]</sup>  | 全般 |
| ベンガル語 (バングラデシュ) | `bn-BD` | Female | `bn-BD-NabanitaNeural` <sup>[新規作成]</sup>  | 全般 |
| ベンガル語 (バングラデシュ) | `bn-BD` | Male | `bn-BD-PradeepNeural` <sup>[新規作成]</sup>  | 全般 |
| ブルガリア語 (ブルガリア) | `bg-BG` | Female | `bg-BG-KalinaNeural` | 全般 |
| ブルガリア語 (ブルガリア) | `bg-BG` | Male | `bg-BG-BorislavNeural` | 全般 |
| ビルマ語 (ミャンマー) | `my-MM` | Female | `my-MM-NilarNeural` <sup>[新規作成]</sup>  | 全般 |
| ビルマ語 (ミャンマー) | `my-MM` | Male | `my-MM-ThihaNeural` <sup>[新規作成]</sup>  | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-JoanaNeural` | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Male | `ca-ES-EnricNeural` | 全般 |
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 全般 |
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | 全般 |
| 中国語 (繁体字) | `zh-HK` | Male | `zh-HK-WanLungNeural` | 全般 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | 全般、複数のロールプレイとスタイルが [SSML の使用](speech-synthesis-markup.md#adjust-speaking-styles)により使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、高齢者の音声、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | 全般、複数のロールプレイとスタイルが [SSML の使用](speech-synthesis-markup.md#adjust-speaking-styles)により使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 物語のナレーション向けに最適化された子どもの声 |
| 中国語 (標準、簡体字) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-YunyangNeural` | ニュースの読み取り向けに最適化<br /> [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、複数の音声スタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-YunyeNeural` | ストーリーのナレーション向けに最適化 |
| 中国語 (台湾標準中国語) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | 全般 |
| 中国語 (台湾標準中国語) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 全般 |
| 中国語 (台湾標準中国語) | `zh-TW` | Male | `zh-TW-YunJheNeural` | 全般 |
| クロアチア語 (クロアチア) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | 全般 |
| クロアチア語 (クロアチア) | `hr-HR` | Male | `hr-HR-SreckoNeural` | 全般 |
| チェコ語 (チェコ) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | 全般 |
| チェコ語 (チェコ) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | 全般 |
| デンマーク語 (デンマーク) | `da-DK` | Female | `da-DK-ChristelNeural` | 全般 |
| デンマーク語 (デンマーク) | `da-DK` | Male | `da-DK-JeppeNeural` | 全般 |
| オランダ語 (ベルギー) | `nl-BE` | Female | `nl-BE-DenaNeural` | 全般 | 
| オランダ語 (ベルギー) | `nl-BE` | Male | `nl-BE-ArnaudNeural` | 全般 | 
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-FennaNeural` | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Male | `nl-NL-MaartenNeural` | 全般 |
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-NatashaNeural` | 全般 |
| 英語 (オーストラリア) | `en-AU` | Male | `en-AU-WilliamNeural` | 全般 |
| 英語 (カナダ) | `en-CA` | Female | `en-CA-ClaraNeural` | 全般 |
| 英語 (カナダ) | `en-CA` | Male | `en-CA-LiamNeural` | 全般 |
| 英語 (香港) | `en-HK` | Female | `en-HK-YanNeural` | 全般 |
| 英語 (香港) | `en-HK` | Male | `en-HK-SamNeural` | 全般 |
| 英語 (インド) | `en-IN` | Female | `en-IN-NeerjaNeural` | 全般 |
| 英語 (インド) | `en-IN` | Male | `en-IN-PrabhatNeural` | 全般 |
| 英語 (アイルランド) | `en-IE` | Female | `en-IE-EmilyNeural` | 全般 |
| 英語 (アイルランド) | `en-IE` | Male | `en-IE-ConnorNeural` | 全般 |
| 英語 (ケニア) | `en-KE` | Female | `en-KE-AsiliaNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (ケニア) | `en-KE` | Male | `en-KE-ChilembaNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (ニュージーランド) | `en-NZ` | Female | `en-NZ-MollyNeural` | 全般 |
| 英語 (ニュージーランド) | `en-NZ` | Male | `en-NZ-MitchellNeural` | 全般 |
| 英語 (ナイジェリア) | `en-NG` | Female | `en-NG-EzinneNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (ナイジェリア) | `en-NG` | Male | `en-NG-AbeoNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (フィリピン) | `en-PH` | Female | `en-PH-RosaNeural` | 全般 | 
| 英語 (フィリピン) | `en-PH` | Male | `en-PH-JamesNeural` | 全般 | 
| 英語 (シンガポール) | `en-SG` | Female | `en-SG-LunaNeural` | 全般 |
| 英語 (シンガポール) | `en-SG` | Male | `en-SG-WayneNeural` | 全般 |
| 英語 (南アフリカ) | `en-ZA` | Female | `en-ZA-LeahNeural` | 全般 |
| 英語 (南アフリカ) | `en-ZA` | Male | `en-ZA-LukeNeural` | 全般 |
| 英語 (タンザニア) | `en-TZ` | Female | `en-TZ-ImaniNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (タンザニア) | `en-TZ` | Male | `en-TZ-ElimuNeural` <sup>[新規作成]</sup>  | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-LibbyNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-SoniaNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-MiaNeural` <sup>2021 年 10 月 30 日に廃止されました。以下を参照してください</sup> | 全般 |
| 英語 (イギリス) | `en-GB` | Male | `en-GB-RyanNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-AmberNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-AriaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 英語 (米国) | `en-US` | Female | `en-US-AshleyNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-CoraNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-ElizabethNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-JennyNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 英語 (米国) | `en-US` | Female | `en-US-MichelleNeural`| 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-MonicaNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-SaraNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 英語 (米国) | `en-US` | Kid | `en-US-AnaNeural`| 全般 |
| 英語 (米国) | `en-US` | Male | `en-US-BrandonNeural` | 全般 |
| 英語 (米国) | `en-US` | Male | `en-US-ChristopherNeural`  | 全般 |
| 英語 (米国) | `en-US` | Male | `en-US-EricNeural` | 全般 |
| 英語 (米国) | `en-US` | Male | `en-US-GuyNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| 英語 (米国) | `en-US` | Male | `en-US-JacobNeural` | 全般 |
| エストニア語 (エストニア) | `et-EE` | Female | `et-EE-AnuNeural` | 全般 |
| エストニア語 (エストニア) | `et-EE` | Male | `et-EE-KertNeural` | 全般 |
| フィリピノ語 (フィリピン) | `fil-PH` | Female | `fil-PH-BlessicaNeural` <sup>[新規作成]</sup>  | 全般 |
| フィリピノ語 (フィリピン) | `fil-PH` | Male | `fil-PH-AngeloNeural` <sup>[新規作成]</sup>  | 全般 |
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-NooraNeural` | 全般 |
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-SelmaNeural` | 全般 |
| フィンランド語 (フィンランド) | `fi-FI` | Male | `fi-FI-HarriNeural` | 全般 |
| フランス語 (ベルギー) | `fr-BE` | Female | `fr-BE-CharlineNeural` | 全般 | 
| フランス語 (ベルギー) | `fr-BE` | Male | `fr-BE-GerardNeural` | 全般 | 
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 全般 |
| フランス語 (カナダ) | `fr-CA` | Male | `fr-CA-AntoineNeural` | 全般 |
| フランス語 (カナダ) | `fr-CA` | Male | `fr-CA-JeanNeural` | 全般 |
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 全般 |
| フランス語 (フランス) | `fr-FR` | Male | `fr-FR-HenriNeural` | 全般 |
| フランス語 (スイス) | `fr-CH` | Female | `fr-CH-ArianeNeural` | 全般 |
| フランス語 (スイス) | `fr-CH` | Male | `fr-CH-FabriceNeural` | 全般 |
| ガリシア語 (スペイン) | `gl-ES` | Female | `gl-ES-SabelaNeural` <sup>[新規作成]</sup>  | 全般 |
| ガリシア語 (スペイン) | `gl-ES` | Male | `gl-ES-RoiNeural` <sup>[新規作成]</sup>  | 全般 |
| ドイツ語 (オーストリア) | `de-AT` | Female | `de-AT-IngridNeural` | 全般 |
| ドイツ語 (オーストリア) | `de-AT` | Male | `de-AT-JonasNeural` | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Female | `de-DE-KatjaNeural` | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Male | `de-DE-ConradNeural` | 全般 |
| ドイツ語 (スイス) | `de-CH` | Female | `de-CH-LeniNeural` | 全般 |
| ドイツ語 (スイス) | `de-CH` | Male | `de-CH-JanNeural` | 全般 |
| ギリシャ語 (ギリシャ) | `el-GR` | Female | `el-GR-AthinaNeural` | 全般 |
| ギリシャ語 (ギリシャ) | `el-GR` | Male | `el-GR-NestorasNeural` | 全般 |
| グジャラート語 (インド) | `gu-IN` | Female | `gu-IN-DhwaniNeural` | 全般 |
| グジャラート語 (インド) | `gu-IN` | Male | `gu-IN-NiranjanNeural` | 全般 |
| ヘブライ語 (イスラエル) | `he-IL` | Female | `he-IL-HilaNeural` | 全般 |
| ヘブライ語 (イスラエル) | `he-IL` | Male | `he-IL-AvriNeural` | 全般 |
| ヒンディー語 (インド) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 全般 |
| ヒンディー語 (インド) | `hi-IN` | Male | `hi-IN-MadhurNeural` | 全般 |
| ハンガリー語 (ハンガリー) | `hu-HU` | Female | `hu-HU-NoemiNeural` | 全般 |
| ハンガリー語 (ハンガリー) | `hu-HU` | Male | `hu-HU-TamasNeural` | 全般 |
| インドネシア語 (インドネシア) | `id-ID` | Female | `id-ID-GadisNeural` | 全般 |
| インドネシア語 (インドネシア) | `id-ID` | Male | `id-ID-ArdiNeural` | 全般 |
| アイルランド語 (アイルランド) | `ga-IE` | Female | `ga-IE-OrlaNeural` | 全般 |
| アイルランド語 (アイルランド) | `ga-IE` | Male | `ga-IE-ColmNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-ElsaNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-IsabellaNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Male | `it-IT-DiegoNeural` | 全般 |
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 全般 |
| 日本語 (日本) | `ja-JP` | Male | `ja-JP-KeitaNeural` | 全般 |
| ジャワ語 (インドネシア) | `jv-ID` | Female | `jv-ID-SitiNeural` <sup>[新規作成]</sup>  | 全般 |
| ジャワ語 (インドネシア) | `jv-ID` | Male | `jv-ID-DimasNeural` <sup>[新規作成]</sup>  | 全般 |
| クメール語 (カンボジア) | `km-KH` | Female | `km-KH-SreymomNeural` <sup>[新規作成]</sup>  | 全般 |
| クメール語 (カンボジア) | `km-KH` | Male | `km-KH-PisethNeural` <sup>[新規作成]</sup>  | 全般 |
| 韓国語 (韓国) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 全般 |
| 韓国語 (韓国) | `ko-KR` | Male | `ko-KR-InJoonNeural` | 全般 |
| ラトビア語 (ラトビア) | `lv-LV` | Female | `lv-LV-EveritaNeural` | 全般 |
| ラトビア語 (ラトビア) | `lv-LV` | Male | `lv-LV-NilsNeural` | 全般 |
| リトアニア語 (リトアニア) | `lt-LT` | Female | `lt-LT-OnaNeural` | 全般 |
| リトアニア語 (リトアニア) | `lt-LT` | Male | `lt-LT-LeonasNeural` | 全般 |
| マレー語 (マレーシア) | `ms-MY` | Female | `ms-MY-YasminNeural` | 全般 |
| マレー語 (マレーシア) | `ms-MY` | Male | `ms-MY-OsmanNeural` | 全般 |
| マルタ語 (マルタ) | `mt-MT` | Female | `mt-MT-GraceNeural` | 全般 |
| マルタ語 (マルタ) | `mt-MT` | Male | `mt-MT-JosephNeural` | 全般 |
| マラーティー語 (インド) | `mr-IN` | Female | `mr-IN-AarohiNeural` | 全般 |
| マラーティー語 (インド) | `mr-IN` | Male | `mr-IN-ManoharNeural` | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-IselinNeural` | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-PernilleNeural` | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Male | `nb-NO-FinnNeural` | 全般 |
| ペルシア語 (イラン) | `fa-IR` | Female | `fa-IR-DilaraNeural` <sup>[新規作成]</sup>  | 全般 |
| ペルシア語 (イラン) | `fa-IR` | Male | `fa-IR-FaridNeural` <sup>[新規作成]</sup>  | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Male | `pl-PL-MarekNeural` | 全般 |
| ポルトガル語 (ブラジル) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能 |
| ポルトガル語 (ブラジル) | `pt-BR` | Male | `pt-BR-AntonioNeural` | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-RaquelNeural` | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Male | `pt-PT-DuarteNeural` | 全般 |
| ルーマニア語 (ルーマニア) | `ro-RO` | Female | `ro-RO-AlinaNeural` | 全般 |
| ルーマニア語 (ルーマニア) | `ro-RO` | Male | `ro-RO-EmilNeural` | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Male | `ru-RU-DmitryNeural` | 全般 |
| スロバキア語 (スロバキア) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | 全般 |
| スロバキア語 (スロバキア) | `sk-SK` | Male | `sk-SK-LukasNeural` | 全般 |
| スロベニア語 (スロベニア) | `sl-SI` | Female | `sl-SI-PetraNeural` | 全般 |
| スロベニア語 (スロベニア) | `sl-SI` | Male | `sl-SI-RokNeural` | 全般 |
| ソマリ語 (ソマリア) | `so-SO` | Female | `so-SO-UbaxNeural` <sup>[新規作成]</sup>  | 全般 |
| ソマリ語 (ソマリア) | `so-SO`| Male | `so-SO-MuuseNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (アルゼンチン) | `es-AR` | Female | `es-AR-ElenaNeural` | 全般 |
| スペイン語 (アルゼンチン) | `es-AR` | Male | `es-AR-TomasNeural` | 全般 |
| スペイン語 (ボリビア) | `es-BO` | Female | `es-BO-SofiaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ボリビア) | `es-BO` | Male | `es-BO-MarceloNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (チリ) | `es-CL` | Female | `es-CL-CatalinaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (チリ) | `es-CL` | Male | `es-CL-LorenzoNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (コロンビア) | `es-CO` | Female | `es-CO-SalomeNeural` | 全般 |
| スペイン語 (コロンビア) | `es-CO` | Male | `es-CO-GonzaloNeural` | 全般 |
| スペイン語 (コスタリカ) | `es-CR` | Female | `es-CR-MariaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (コスタリカ) | `es-CR` | Male | `es-CR-JuanNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (キューバ) | `es-CU` | Female | `es-CU-BelkysNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (キューバ) | `es-CU` | Male | `es-CU-ManuelNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ドミニカ共和国) | `es-DO` | Female | `es-DO-RamonaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ドミニカ共和国) | `es-DO` | Male | `es-DO-EmilioNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (エクアドル) | `es-EC` | Female | `es-EC-AndreaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (エクアドル) | `es-EC` | Male | `es-EC-LuisNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (エルサルバドル) | `es-SV` | Female | `es-SV-LorenaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (エルサルバドル) | `es-SV` | Male | `es-SV-RodrigoNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (赤道ギニア) | `es-GQ` | Female | `es-GQ-TeresaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (赤道ギニア) | `es-GQ` | Male | `es-GQ-JavierNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (グアテマラ) | `es-GT` | Female | `es-GT-MartaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (グアテマラ) | `es-GT` | Male | `es-GT-AndresNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ホンジュラス) | `es-HN` | Female | `es-HN-KarlaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ホンジュラス) | `es-HN` | Male | `es-HN-CarlosNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Female | `es-MX-DaliaNeural` | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Male | `es-MX-JorgeNeural` | 全般 |
| スペイン語 (ニカラグア) | `es-NI` | Female | `es-NI-YolandaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ニカラグア) | `es-NI` | Male | `es-NI-FedericoNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (パナマ) | `es-PA` | Female | `es-PA-MargaritaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (パナマ) | `es-PA` | Male | `es-PA-RobertoNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (パラグアイ) | `es-PY` | Female | `es-PY-TaniaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (パラグアイ) | `es-PY` | Male | `es-PY-MarioNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ペルー) | `es-PE` | Female | `es-PE-CamilaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ペルー) | `es-PE` | Male | `es-PE-AlexNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (プエルトリコ) | `es-PR` | Female | `es-PR-Karina Neural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (プエルトリコ) | `es-PR` | Male | `es-PR-Victor Neural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-ElviraNeural` | 全般 |
| スペイン語 (スペイン) | `es-ES` | Male | `es-ES-AlvaroNeural` | 全般 |
| スペイン語 (ウルグアイ) | `es-UY` | Female | `es-UY-ValentinaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ウルグアイ) | `es-UY` | Male | `es-UY-MateoNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (米国) | `es-US` | Female | `es-US-PalomaNeural` | 全般 |
| スペイン語 (米国) | `es-US` | Male | `es-US-AlonsoNeural` | 全般 |
| スペイン語 (ベネズエラ) | `es-VE` | Female | `es-VE-PaolaNeural` <sup>[新規作成]</sup>  | 全般 |
| スペイン語 (ベネズエラ) | `es-VE` | Male | `es-VE-SebastianNeural` <sup>[新規作成]</sup>  | 全般 |
| スンダ語 (インドネシア) | `su-ID` | Female | `su-ID-TutiNeural` <sup>[新規作成]</sup>  | 全般 |
| スンダ語 (インドネシア) | `su-ID` | Male | `su-ID-JajangNeural` <sup>[新規作成]</sup>  | 全般 |
| スワヒリ語 (ケニア) | `sw-KE` | Female | `sw-KE-ZuriNeural` | 全般 |
| スワヒリ語 (ケニア) | `sw-KE` | Male | `sw-KE-RafikiNeural` | 全般 |
| スワヒリ語 (タンザニア) | `sw-TZ` | Female | `sw-TZ-RehemaNeural` <sup>[新規作成]</sup>  | 全般 |
| スワヒリ語 (タンザニア) | `sw-TZ` | Male | `sw-TZ-DaudiNeural` <sup>[新規作成]</sup>  | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-SofieNeural` | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Male | `sv-SE-MattiasNeural` | 全般 |
| タミール語 (インド) | `ta-IN` | Female | `ta-IN-PallaviNeural` | 全般 |
| タミール語 (インド) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | 全般 |
| タミル語 (シンガポール) | `ta-SG` | Female | `ta-SG-VenbaNeural` <sup>[新規作成]</sup>  | 全般 |
| タミル語 (シンガポール) | `ta-SG` | Male | `ta-SG-AnbuNeural` <sup>[新規作成]</sup>  | 全般 |
| タミル語 (スリランカ) | `ta-LK` | Female | `ta-LK-SaranyaNeural` <sup>[新規作成]</sup>  | 全般 |
| タミル語 (スリランカ) | `ta-LK` | Male | `ta-LK-KumarNeural` <sup>[新規作成]</sup>  | 全般 |
| テルグ語 (インド) | `te-IN` | Female | `te-IN-ShrutiNeural` | 全般 |
| テルグ語 (インド) | `te-IN` | Male | `te-IN-MohanNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-AcharaNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-PremwadeeNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Male | `th-TH-NiwatNeural` | 全般 |
| トルコ語 (トルコ) | `tr-TR` | Female | `tr-TR-EmelNeural` | 全般 |
| トルコ語 (トルコ) | `tr-TR` | Male | `tr-TR-AhmetNeural` | 全般 |
| ウクライナ語 (ウクライナ) | `uk-UA` | Female | `uk-UA-PolinaNeural` | 全般 | 
| ウクライナ語 (ウクライナ) | `uk-UA` | Male | `uk-UA-OstapNeural` | 全般 | 
| ウルドゥー語 (インド) | `ur-IN` | Female | `ur-IN-GulNeural` <sup>[新規作成]</sup>  | 全般 |
| ウルドゥー語 (インド) | `ur-IN` | Male | `ur-IN-SalmanNeural` <sup>[新規作成]</sup>  | 全般 |
| ウルドゥー語 (パキスタン) | `ur-PK` | Female | `ur-PK-UzmaNeural`  | 全般 | 
| ウルドゥー語 (パキスタン) | `ur-PK` | Male | `ur-PK-AsadNeural` | 全般 | 
| ウズベク語 (ウズベキスタン) | `uz-UZ` | Female | `uz-UZ-MadinaNeural` <sup>[新規作成]</sup>  | 全般 |
| ウズベク語 (ウズベキスタン) | `uz-UZ` | Male | `uz-UZ-SardorNeural` <sup>[新規作成]</sup>  | 全般 |
| ベトナム語 (ベトナム) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | 全般 |
| ベトナム語 (ベトナム) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | 全般 |
| ウェールズ語 (イギリス) | `cy-GB` | Female | `cy-GB-NiaNeural` | 全般 | 
| ウェールズ語 (イギリス) | `cy-GB` | Male | `cy-GB-AledNeural` | 全般 | 
| ズールー語 (南アフリカ) | `zu-ZA` | Female | `zu-ZA-ThandoNeural` <sup>[新規作成]</sup>  | 全般 |
| ズールー語 (南アフリカ) | `zu-ZA` | Male | `zu-ZA-ThembaNeural` <sup>[新規作成]</sup>  | 全般 |

> [!IMPORTANT]
> 英語 (イギリス) 音声の `en-GB-MiaNeural` は、**2021 年 10 月 30 日** に廃止されました。 `en-GB-MiaNeural` へのサービス要求は、**2021 年 10 月 30 日** 以降、自動的に `en-GB-SoniaNeural` にリダイレクトされるようになります。
> コンテナー ニューラル TTS を使用している場合は、**2021 年 10 月 30 日** 以降は最新バージョンを[ダウンロード](speech-container-howto.md#get-the-container-image-with-docker-pull)してデプロイしてください。以前のバージョンのすべての要求は拒否されます。

#### <a name="neural-voices-in-preview"></a>プレビュー段階のニューラル音声

次のニューラル音声は、パブリック プレビュー段階です。 

| Language                         | Locale  | 性別 | 音声名                             | スタイルのサポート |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 英語 (米国) | `en-US` | Female | `en-US-JennyMultilingualNeural` <sup>[新規作成]</sup> | [SSML](speech-synthesis-markup.md#create-an-ssml-document)を使用して使用できる一般的な多言語機能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaochenNeural` <sup>[新規作成]</sup> | 自然な会話向けに最適化 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoyanNeural` <sup>[新規作成]</sup> | カスタマー サービス向けに最適化 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoshuangNeural` <sup>[新規作成]</sup> | 子供の声。子供向けの話や会話向けに最適化。[SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、複数の音声スタイルを使用可能|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoqiuNeural` <sup>[新規作成]</sup> | ナレーション向けに最適化 |

> [!IMPORTANT]
> パブリック プレビュー段階の音声は、3 つのサービス リージョン (米国東部、西ヨーロッパ、東南アジア) でのみ使用できます。

> [!TIP]
> `en-US-JennyNeuralMultilingual` では、複数の言語がサポートされます。 サポートされている言語の一覧については、[voices/list API ](rest-text-to-speech.md#get-a-list-of-voices)を確認してください。

リージョン別の提供状況の詳細については、[リージョン](regions.md#neural-and-standard-voices)に関するページを参照してください。

ニューラル音声 (話し方など) を構成および調整する方法については、「[音声合成マークアップ言語](speech-synthesis-markup.md#adjust-speaking-styles)」を参照してください。

> [!IMPORTANT]
> `en-US-JessaNeural` 音声が `en-US-AriaNeural` に変更されました。 前に "Jessa" を使用していた場合は、"Aria" に変換します。

> [!TIP]
> 音声合成要求では、"Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)" のような完全なサービス名マッピングを引き続き使用できます。

### <a name="standard-voices"></a>標準音声

テキストから合成音声への変換用に、45 を超える言語とロケールで 75 を超える標準的な音声が用意されています。 リージョン別の提供状況の詳細については、[リージョン](regions.md#neural-and-standard-voices)に関するページを参照してください。

> [!IMPORTANT]
> 標準音声は **2024 年 8 月 31 日** に廃止され、その日以降はサポートされなくなります。これは、**2021 年 8 月 31 日** 以前に作成されたすべての既存の音声サブスクリプションに送られたメールで発表されました。 廃止期間 (**2021 年 8 月 31 日** - **2024 年 8 月 31 日**) 中は、既存の標準音声ユーザーは引き続き標準音声を使用できますが、すべての新しいユーザーと新しい音声リソースではニューラル音声を選択する必要があります。

> [!NOTE]
> 2 つの例外を除き、標準音声は 16 khz サンプル レートを使用するサンプルから作成されます。
> **en-US-AriaRUS** と **en-US-GuyRUS** の音声も、24 khz サンプル レートを使用するサンプルから作成されます。
> すべての音声は、合成時に他のサンプル レートにアップサンプリングまたはダウンサンプリングできます。

| Language | ロケール (BCP-47) | 性別 | 音声名 |
|--|--|--|--|
| アラビア語 (エジプト) | `ar-EG` | Female | `ar-EG-Hoda`|
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
| チェコ語 (チェコ) | `cs-CZ` | Male | `cs-CZ-Jakub`|
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
| 英語 (米国) | `en-US` | Female | `en-US-AriaRUS`|
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

Custom Voice は、ニューラル レベル (カスタム ニューラル音声) 内で使用できます。 カスタム ニューラル音声では、ニューラル TTS テクノロジと複数話者による多言語ユニバーサル モデルに基づき、話すスタイルや適応性のあるクロス言語で高度な合成音声を作成できます。 以下のサポートされている言語をご確認ください。  

> [!IMPORTANT]
> Custom Voice の統計的パラメトリックおよび連結トレーニング方法を含む Standard レベルは非推奨で、2024 年 2 月 29 日に廃止される予定です。 ニューラル以外/Standard Custom Voice を使用している場合は、次の手順に従って、カスタム ニューラル音声に直ちに移行して、品質を向上させ、責任を持って音声をデプロイしてください。 

| Language | Locale | ニューラル | クロス言語 |
|--|--|--|--|
| アラビア語 (エジプト) | `ar-EG` | はい | いいえ |
| ブルガリア語 (ブルガリア) | `bg-BG` | はい | いいえ |
| 中国語 (標準、簡体字) | `zh-CN` | はい | はい |
| 中国語 (標準、簡体字)、英語バイリンガル | `zh-CN` バイリンガル | はい | はい |
| 中国語 (台湾標準中国語) | `zh-TW` | はい | いいえ |
| チェコ語 (チェコ) | `cs-CZ` | はい | いいえ |
| オランダ語 (オランダ) | `nl-NL` | はい | いいえ |
| 英語 (オーストラリア) | `en-AU` | はい | はい |
| 英語 (カナダ) | `en-CA` | はい | いいえ |
| 英語 (インド) | `en-IN` | はい | いいえ |
| 英語 (アイルランド) | `en-IE` | はい | いいえ |
| 英語 (イギリス) | `en-GB` | はい | はい |
| 英語 (米国) | `en-US` | はい | はい |
| フランス語 (カナダ) | `fr-CA` | はい | はい |
| フランス語 (フランス) | `fr-FR` | はい | はい |
| ドイツ語 (オーストリア) | `de-AT` | はい | いいえ |
| ドイツ語 (ドイツ) | `de-DE` | はい | はい |
| ハンガリー語 (ハンガリー) | `hu-HU` | はい | いいえ |
| イタリア語 (イタリア) | `it-IT` | はい | はい |
| 日本語 (日本) | `ja-JP` | はい | はい |
| 韓国語 (韓国) | `ko-KR` | はい | はい |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | はい | いいえ |
| ポルトガル語 (ブラジル) | `pt-BR` | はい | はい |
| ポルトガル語 (ポルトガル) | `pt-PT` | はい | いいえ |
| ロシア語 (ロシア) | `ru-RU` | はい | はい |
| スロバキア語 (スロバキア) | `sk-SK` | はい | いいえ |
| スペイン語 (メキシコ) | `es-MX` | はい | はい |
| スペイン語 (スペイン) | `es-ES` | はい | はい |
| トルコ語 (トルコ) | `tr-TR` | はい | いいえ |
| ベトナム語 (ベトナム) | `vi-VN` | はい | いいえ |

カスタム音声モデルをトレーニングする必要があるトレーニング データと一致する適切なロケールを選択します。 たとえば、持っている録音データが英国アクセントの英語で話されている場合は、`en-GB` を選択します。

> [!NOTE]
> Custom Voice では、中国語と英語のバイリンガルを除き、バイリンガル モデル トレーニングはサポートされていません。 英語も話すことができる中国人の音声をトレーニングする場合は、[Chinese-English bilingual]\(中国語 - 英語のバイリンガル\) を選択します。 標準方法を使用した中国語 - 英語バイリンガル モデルのトレーニングは、北ヨーロッパと米国中北部のみで使用できます。 カスタム ニューラル音声のトレーニングは、英国南部と米国東部で使用できます。

## <a name="speech-translation"></a>音声翻訳

**Speech Translation** API では、音声を音声に翻訳し、音声をテキストに翻訳するために、さまざまな言語をサポートしています。 ソース言語は、音声テキスト変換言語の表に常に含まれている必要があります。 使用可能なターゲット言語は、何に翻訳するか (音声かテキストか) によって決まります。 受信した音声はすべての[サポートされている言語](https://www.microsoft.com/translator/business/languages/)に翻訳できます。 一部の言語は[音声合成](language-support.md#text-languages)に利用できます。

### <a name="text-languages"></a>テキスト言語

| テキスト言語           | 言語コード |
|:------------------------|:-------------:|
| アフリカーンス語 | `af` |
| アルバニア語 | `sq` |
| アムハラ語 | `am` |
| アラビア語 | `ar` |
| アルメニア語 | `hy` |
| アッサム語 | `as` |
| アゼルバイジャン語 | `az` |
| ベンガル語 | `bn` |
| ボスニア語 (ラテン) | `bs` |
| ブルガリア語 | `bg` |
| 広東語 (繁体字) | `yue` |
| カタロニア語 | `ca` |
| 中国語 (文語) | `lzh` |
| 簡体中国語 | `zh-Hans` |
| 中国語 (繁体字) | `zh-Hant` |
| クロアチア語 | `hr` |
| チェコ語 | `cs` |
| デンマーク語 | `da` |
| ダリー語 | `prs` |
| オランダ語 | `nl` |
| 英語 | `en` |
| エストニア語 | `et` |
| フィジー語 | `fj` |
| フィリピン語 | `fil` |
| フィンランド語 | `fi` |
| フランス語 | `fr` |
| フランス語 (カナダ) | `fr-ca` |
| ドイツ語 | `de` |
| ギリシャ語 | `el` |
| グジャラート語 | `gu` |
| ハイチ・クレオール語 | `ht` |
| ヘブライ語 | `he` |
| ヒンディー語 | `hi` |
| ミャオ語 | `mww` |
| ハンガリー語 | `hu` |
| アイスランド語 | `is` |
| インドネシア語 | `id` |
| イヌクティトット語 | `iu` |
| アイルランド語 | `ga` |
| イタリア語 | `it` |
| 日本語 | `ja` |
| カンナダ語 | `kn` |
| カザフ語 | `kk` |
| クメール語 | `km` |
| クリンゴン語 | `tlh-Latn` |
| クリンゴン語 (plqaD) | `tlh-Piqd` |
| 韓国語 | `ko` |
| クルド語 (中央) | `ku` |
| クルド語 (北) | `kmr` |
| ラオス語 | `lo` |
| ラトビア語 | `lv` |
| リトアニア語 | `lt` |
| マダガスカル語 | `mg` |
| マレー語 | `ms` |
| マラヤーラム語 | `ml` |
| マルタ語 | `mt` |
| マオリ語 | `mi` |
| マラーティー語 | `mr` |
| ミャンマー | `my` |
| ネパール語 | `ne` |
| ノルウェー語 | `nb` |
| オディア語 | `or` |
| パシュトウ語 | `ps` |
| ペルシャ語 | `fa` |
| ポーランド語 | `pl` |
| ポルトガル語 (ブラジル) | `pt` |
| ポルトガル語 (ポルトガル) | `pt-pt` |
| パンジャーブ語 | `pa` |
| オトミ語 | `otq` |
| ルーマニア語 | `ro` |
| ロシア語 | `ru` |
| サモア語 | `sm` |
| セルビア語 (キリル) | `sr-Cyrl` |
| セルビア語 (ラテン) | `sr-Latn` |
| スロバキア語 | `sk` |
| スロベニア語 | `sl` |
| スペイン語 | `es` |
| スワヒリ語 | `sw` |
| スウェーデン語 | `sv` |
| タヒチ語 | `ty` |
| タミル語 | `ta` |
| テルグ語 | `te` |
| タイ語 | `th` |
| ティグリニア語 | `ti` |
| トンガ語 | `to` |
| トルコ語 | `tr` |
| ウクライナ語 | `uk` |
| ウルドゥ語 | `ur` |
| ベトナム語 | `vi` |
| ウェールズ語 | `cy` |
| ユカテコ語 | `yua` |

## <a name="speaker-recognition"></a>Speaker Recognition

話者認識は、ほとんど言語に依存しません。 複数の言語からさまざまなデータ ソースを組み合わせることによって、テキストに依存しない話者認識のユニバーサル モデルを構築しました。 次の表に示す言語とロケールでモデルを調整し、評価しました。 Speaker Recognition の詳細については、[概要](speaker-recognition-overview.md)に関するページを参照してください。

| Language | ロケール (BCP-47) | テキスト依存の認証 | テキストに依存しない認証 | テキストに依存しない識別 |
|----|----|----|----|----|
|英語 (米国)  |  `en-US`  |  はい  |  はい  |  はい |
|中国語 (標準、簡体字) | `zh-CN`     |     該当なし |     はい |     はい|
|英語 (オーストラリア)     | `en-AU`    | 該当なし     | はい     | はい|
|英語 (カナダ)     | `en-CA`     | 該当なし |     はい |     はい|
|英語 (インド)     | `en-IN`     | 該当なし |     はい |     はい|
|英語 (英国)     | `en-GB`     | 該当なし     | はい     | はい|
|フランス語 (カナダ)     | `fr-CA`     | 該当なし     | はい |     はい|
|フランス語 (フランス)     | `fr-FR`     | 該当なし     | はい     | はい|
|ドイツ語 (ドイツ)     | `de-DE`     | 該当なし     | はい     | はい|
|イタリア語 | `it-IT`     |     該当なし     | はい |     はい|
|日本語     | `ja-JP` | 該当なし     | はい     | はい|
|ポルトガル語 (ブラジル) | `pt-BR` |     該当なし |     はい |     はい|
|スペイン語 (メキシコ)     | `es-MX`     | 該当なし |     はい |     はい|
|スペイン語 (スペイン)     | `es-ES` | 該当なし     | はい |     はい|

## <a name="custom-keyword-and-keyword-verification"></a>カスタム キーワードとキーワード検証

次の表では、カスタム キーワードおよびキーワードの検証がサポートされている言語の概要を示しています。

| Language | ロケール (BCP-47) | カスタム キーワード | キーワード検証 |
| -------- | --------------- | -------------- | -------------------- |
| 中国語 (標準、簡体字) | zh-CN | Yes | はい |
| 英語 (米国) | ja-JP | Yes | はい |
| 日本語 (日本) | ja-JP | いいえ | はい |
| ポルトガル語 (ブラジル) | pt-BR | いいえ | はい |

## <a name="next-steps"></a>次のステップ

* [無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください
* [C# で音声を認識する方法を確認する](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
