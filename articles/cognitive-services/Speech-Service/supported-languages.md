---
title: 音声サービスでサポートされている言語 - Microsoft Cognitive Services
description: 音声サービスでサポートされている言語の一覧。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2a768846958f73ace22971f2f2ca8f886c9df252
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282158"
---
# <a name="supported-languages-for-speech-service"></a>音声サービスでサポートされている言語

さまざまな言語が音声サービス機能のためにサポートされています。 次の表は言語サポートをまとめたものです。

## <a name="speech-to-text"></a>音声テキスト変換

Microsoft の音声認識 API では、次の言語がサポートされています。 

  コード | Language | 音響適応 | 言語適応 | 発音適応
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | アラビア語 (エジプト)、現代標準 | いいえ  | はい | いいえ 
 It-IT | イタリア語 (イタリア) | いいえ  | はい | いいえ  
 de-DE | ドイツ語 (ドイツ) | いいえ  | 可能  | [はい]
 ja-JP | 日本語 (日本) | いいえ  | はい | いいえ 
 en-US | 英語 (米国) | [はい] | はい | [はい]
 pt-BR | ポルトガル語 (ブラジル) | いいえ  | はい | いいえ 
 es-ES | スペイン語 (スペイン) | いいえ  | はい | いいえ 
 ru-RU | ロシア語 (ロシア) | いいえ  | はい | いいえ 
 fr-FR | フランス語 (フランス) | いいえ  | はい | いいえ 
 zh-CN | 中国語 (標準、簡体字) | いいえ  | はい | いいえ 

## <a name="text-to-speech"></a>テキストから音声へ

**Text to Speech** API では次の音声が提供されますが、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。

ロケール | Language | 性別 | サービス名のマッピング
-------|----------|---------|--------------------
ar-EG* | アラビア語 (エジプト) | 女性 | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | アラビア語 (サウジアラビア) | 男性 | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | ブルガリア語 | 男性 | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | カタルニア語 (スペイン) | 女性 | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | チェコ語 | 男性 | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | デンマーク語 | 女性 | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | ドイツ語 (オーストリア) | 男性 | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | ドイツ語 (スイス) | 男性 | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | ドイツ語 (ドイツ) | 女性 | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda) "
| | | 女性 | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
| | | 男性 | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo) "
el-GR | ギリシャ語 | 男性 | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | 英語 (オーストラリア) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine) "
| | | 女性 | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | 英語 (カナダ) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
| | | 女性 | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | 英語 (英国) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | 英語 (アイルランド) |男性 | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | 英語 (インド) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
en-US | 英語 (米国) |女性 | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
| | |男性 | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"
es-ES | スペイン語 (スペイン) |女性 | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | スペイン語 (メキシコ) | 女性 | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
| | | 男性 | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | フィンランド語 | 女性 | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | フランス語 (カナダ) |女性 | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
| | | 女性 | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | フランス語 (スイス)|男性 | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | フランス語 (フランス)|女性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| ヘブライ語 (イスラエル) | 男性| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | ヒンディー語 (インド) | 女性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
| | | 男性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | クロアチア語 | 男性 | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | ハンガリー語 | 男性 | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | インドネシア語| 男性 | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | イタリア語 |男性 | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)"
ja-JP | 日本語 |女性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
ko-KR | 韓国語 |女性 | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | マレー語|男性 | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | ノルウェー語|女性 | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | オランダ語|女性 | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | ポーランド語|女性 | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | ポルトガル語 (ブラジル)|女性 | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | ポルトガル語 (ポルトガル)|女性 | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | ルーマニア語|男性 | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU |ロシア語| 女性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)"
sk-SK | スロバキア語|男性 | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | スロベニア語|男性 | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | スウェーデン語|女性 | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | タミール語 (インド) |男性 | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
th-TH | タイ語|男性 | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR |トルコ語| 女性 | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | ベトナム語|男性 | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | 中国語 (大陸)|女性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
| | |男性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | 中国語 (香港)|女性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
| | |女性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
| || 男性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | 中国語 (台湾)|女性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
| || 女性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
| || 男性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"

\* *ar-EG では現代標準アラビア語 (MSA) をサポートしています。*

### <a name="customization"></a>カスタマイズ

Custom **Text to Speech** 音声フォントでは、アメリカ英語 (en-US) と中国語 (zh-CN) をサポートしています。

## <a name="speech-translation"></a>音声翻訳

**Speech Translation** API では、音声を音声に翻訳し、音声をテキストに翻訳するために、さまざまな言語をサポートしています。 ソース言語は、下の音声言語表に含まれている必要があります。 使用可能なターゲット言語は、何に翻訳するか (音声かテキストか) によって決まります。

言語の一覧はプログラムで取得するか、REST API の言語エンドポイントを利用して取得できます。 この一覧には、各言語コードと、英語表記またはサポートされているその他の言語表記による言語名が記載されています。 この一覧は、新しい言語が使用できるようになると、Microsoft Translator サービスによって更新されます。

### <a name="speech-languages"></a>音声言語

| 音声言語   | 言語コード |
|:----------- |-|
| アラビア語 (現代標準)      | `ar` |
| 中国語 (標準)      | `zh` |
| 英語      | `en` |
| フランス語      | `fr` |
| ドイツ語      | `de` |
| イタリア語      | `it` |
| 日本語      | `jp` |
| ポルトガル語 (ブラジル)     | `pt` |
| ロシア語      | `ru` |
| スペイン語      |  `es` |

### <a name="text-languages"></a>テキスト言語

| テキスト言語    | 言語コード |
|:----------- |:-------------:|
| アフリカーンス語      | `af`          |
| アラビア語       | `ar`          |
| バングラ語      | `bn`          |
| ボスニア語 (ラテン)      | `bs`          |
| ブルガリア語      | `bg`          |
| 広東語 (繁体字)      | `yue`          |
| カタルニア語      | `ca`          |
| 中国語 (簡体字)      | `zh-Hans`          | 
| 中国語 (繁体字)      | `zh-Hant`          |
| クロアチア語      | `hr`          |
| チェコ語      | `cs`          |
| デンマーク語      | `da`          |
| オランダ語      | `nl`          |
| 英語      | `en`          |
| エストニア語      | `et`          |
| フィジー語      | `fj`          |
| フィリピン語      | `fil`          |
| フィンランド語      | `fi`          |
| フランス語      | `fr`          |
| ドイツ語      | `de`          |
| ギリシャ語      | `el`          |
| ハイチ クレオール語      | `ht`          |
| ヘブライ語      | `he`          |
| ヒンディー語      | `hi`          |
| ミャオ語      | `mww`          |
| ハンガリー語      | `hu`          |
| インドネシア語      | `id`          |
| イタリア語      | `it`          |
| 日本語      | `ja`          |
| スワヒリ語      | `sw`          |
| クリンゴン語      | `tlh`          |
| クリンゴン語 (plqaD)      | `tlh-Qaak`          |
| 韓国語      | `ko`          |
| ラトビア語      | `lv`          |
| リトアニア語      | `lt`          |
| マダガスカル語      | `mg`          |
| マレー語      | `ms`          |
| マルタ語      | `mt`          |
| ノルウェー語      | `nb`          |
| ペルシャ語      | `fa`          |
| ポーランド語      | `pl`          |
| ポルトガル語      | `pt`          |
| オトミ語      | `otq`          |
| ルーマニア語      | `ro`          |
| ロシア語      | `ru`          |
| サモア語      | `sm`          |
| セルビア語 (キリル文字)      | `sr-Cyrl`          |
| セルビア語 (ラテン)      | `sr-Latn`          |
| スロバキア語     | `sk`          |
| スロベニア語      | `sl`          |
| スペイン語      | `es`          |
| スウェーデン語      | `sv`          |
| タヒチ語      | `ty`          |
| タミール語      | `ta`          |
| タイ語      | `th`          |
| トンガ語      | `to`          |
| トルコ語      | `tr`          |
| ウクライナ語      | `uk`          |
| ウルドゥー語      | `ur`          |
| ベトナム語      | `vi`          |
| ウェールズ語      | `cy`          |
| ユカテコ語      | `yua`          |


## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
