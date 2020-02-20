---
title: 言語サポート - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API では、Neural Machine Translation (NMT) を利用したテキスト対テキストの翻訳で、以下の言語をサポートしています。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206125"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Translator Text API の言語と地域のサポート

Translator Text API では、テキスト対テキストの翻訳について、以下の言語をサポートしています。 Neural Machine Translation (NMT) は、AI を使用する高品質機械翻訳の新しい標準で、ニューラル システムが使用できる場合に Translator Text API の V3 を使用して既定で提供されます。

[機械翻訳の詳細](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>翻訳

**V2 Translator API**

> [!NOTE]
> V2 は、2018 年 4 月 30 日に非推奨となりました。 V3 で独占的に使用できる新しい機能を活用するためにアプリケーションを V3 に移行してください。

* 統計のみ: この言語では、ニューラル システムは利用できません。
* ニューラルが利用可能: ニューラル システムを利用できます。 ニューラル システムにアクセスするには、パラメーター `category=generalnn` を使用します。
* ニューラルが既定: ニューラルが既定の翻訳システムです。 Microsoft Translator Hub 用の統計システムにアクセスするには、パラメーター `category=smt` を使用します。
* ニューラルのみ: 利用できるのはニューラル翻訳のみです。

**V3 Translator API** V3 Translator API は既定でニューラルとなっており、ニューラル システムが存在しない場合にのみ統計システムを利用できます。

> [!NOTE]
> 現在、ニューラル言語のサブセットはカスタム翻訳ツールで利用することができ、Mcrosoft では、段階的に内容を追加しています。 [カスタム翻訳ツールで現在利用できる言語をご確認ください](#customization)。

|Language|  言語コード|  V3 API|
|:-----|:-----:|:-----|
|アフリカーンス語| `af`|   ニューラル|
|アラビア語|    `ar`    |   ニューラル|
|ベンガル語|    `bn`    |   ニューラル|
|ボスニア語 (ラテン)|   `bs`    |   ニューラル|
|Bulgarian| `bg`    |   ニューラル|
|広東語 (繁体字)|   `yue`|  統計|
|カタロニア語|   `ca`    |   統計|
|簡体中国語|    `zh-Hans`|ニューラル|
|中国語 (繁体字)|   `zh-Hant`       |ニューラル|
|Croatian|  `hr`    |ニューラル|
|Czech| `cs`    |   ニューラル|
|Danish|    `da`        |ニューラル|
|Dutch| `nl`|   ニューラル|
|English|   `en`    |   ニューラル|
|Estonian|  `et`    |   ニューラル|
|フィジー語|    `fj`    |   統計|
|フィリピン語|  `fil`   |   統計|
|Finnish|   `fi`    |   ニューラル|
|French|    `fr`    |   ニューラル|
|German|    `de`    |   ニューラル|
|Greek| `el`    |   ニューラル|
|ハイチ・クレオール語|    `ht`        |統計|
|ヘブライ語 |`he`   |ニューラル
|ヒンディー語| `hi`    |   ニューラル|
|ミャオ語| `mww`   |   統計|
|Hungarian| `hu`    |   ニューラル|
|アイスランド語| `is`    |   ニューラル|
|インドネシア語|    `id`    |   統計|
|アイルランド語 | `ga`| ニューラル
|Italian|   `it`    |   ニューラル|
|Japanese|  `ja`    |   ニューラル|
|カンナダ語|`kn`| ニューラル
|スワヒリ語| `sw`    |   統計|
|クリンゴン語|   `tlh`   |   統計|
|クリンゴン語 (plqaD)|   `tlh-Qaak`  |   統計|
|Korean |`ko`   |   ニューラル|
|Latvian|   `lv`    |   ニューラル|
|Lithuanian|    `lt`    |   ニューラル|
|マダガスカル語|  `mg`    |   統計|
|マレー語| `ms`        |統計|
|マラヤーラム語| `ml` | ニューラル
|マルタ語|   `mt`    |   統計|
|マオリ語| `mi`  | ニューラル|
|ノルウェー語| `nb`    |   ニューラル|
|ペルシャ語|   `fa`    |   ニューラル|
|Polish|    `pl`    |   ニューラル|
|ポルトガル語 (ブラジル)|   `pt-br` |   ニューラル|
|ポルトガル語 (ポルトガル)| `pt-pt` | ニューラル
|パンジャーブ語|`pa`|ニューラル
|オトミ語|   `otq`   |   統計|
|Romanian|  `ro`    |   ニューラル|
|Russian|   `ru`    |   ニューラル|
|サモア語|    `sm`    |   統計|
|セルビア語 (キリル)|    `sr-Cyrl`|  統計|
|セルビア語 (ラテン)|   `sr-Latn`       |統計|
|Slovak|    `sk`    |   ニューラル|
|Slovenian| `sl`    |   ニューラル|
|Spanish|   `es`    |   ニューラル|
|Swedish|   `sv`    |ニューラル|
|タヒチ語|  `ty`    |統計|
|タミル語| `ta`    |   ニューラル|
|テルグ語|    `te`    |   ニューラル|
|Thai|  `th`    |   ニューラル|
|トンガ語|    `to`    |   統計|
|Turkish|   `tr`        |ニューラル|
|ウクライナ語| `uk`    |   ニューラル|
|ウルドゥ語|  `ur`    |   統計|
|ベトナム語|    `vi`    |   ニューラル|
|ウェールズ語| `cy`    |   ニューラル|
|ユカテコ語|  `yua`   |   統計|

> [!NOTE]
> 言語コード`pt`は、既定では`pt-br`、ポルトガル語 (ブラジル) になります。

## <a name="transliteration"></a>音訳

音訳メソッドは、以下の言語をサポートします。 "ソース言語/ターゲット言語"、"<-->" は、ソース言語あるいはターゲット言語、または記載されているスクリプトのどちらにも音訳できることを示しています。 "-->" は、言語が、あるスクリプトから一方向のみに音訳できることを示しています。

| Language    | 言語コード | スクリプト | ソース言語/ターゲット言語 | スクリプト|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| アラビア語 | `ar` | アラビア語 `Arab` | <--> | ラテン語 `Latn` |
|ベンガル語  | `bn` | ベンガル語 `Beng` | <--> | ラテン語 `Latn` |
| 簡体中国語 | `zh-Hans` | 中国語簡体字 `Hans`| <--> | ラテン語 `Latn` |
| 簡体中国語 | `zh-Hans` | 中国語簡体字 `Hans`| <--> | 中国語繁体字 `Hant`|
| 繁体中国語 | `zh-Hant` | 中国語繁体字 `Hant`| <--> | ラテン語 `Latn` |
| 繁体中国語 | `zh-Hant` | 中国語繁体字 `Hant`| <--> | 中国語簡体字 `Hans` |
| グジャラート語 | `gu`  | グジャラート語 `Gujr` | --> | ラテン語 `Latn` |
| ヘブライ語 | `he` | ヘブライ語 `Hebr` | <--> | ラテン語 `Latn` |
| ヒンディー語 | `hi` | デーヴァナーガリー `Deva` | <--> | ラテン語 `Latn` |
| Japanese | `ja` | 日本語 `Jpan` | <--> | ラテン語 `Latn` |
| カンナダ語 | `kn` | カンナダ語 `Knda` | --> | ラテン語 `Latn` |
| マラヤーラム語 | `ml` | マラヤーラム語 `Mlym` | --> | ラテン語 `Latn` |
| マラーティー語 | `mr` | デーヴァナーガリー `Deva` | --> | ラテン語 `Latn` |
| オリヤー語 | `or` | オリヤー語 `Orya` | <--> | ラテン語 `Latn` |
| パンジャーブ語 | `pa` | グルムキー文字 `Guru`  | <--> | ラテン語 `Latn`  |
| セルビア語 (キリル) | `sr-Cyrl` | キリル語 `Cyrl`  | --> | ラテン語 `Latn` |
| セルビア語 (ラテン) | `sr-Latn` | ラテン語 `Latn` | --> | キリル語 `Cyrl`|
| タミル語 | `ta` | タミール語 `Taml` | --> | ラテン語 `Latn` |
| テルグ語 | `te` | テルグ語 `Telu` | --> | ラテン語 `Latn` |
| Thai | `th` | タイ語 `Thai` | --> | ラテン語 `Latn` |

## <a name="dictionary"></a>Dictionary

辞書では、Lookup および Examples メソッドを使用して、以下の言語と英語間で双方向の翻訳がサポートされています。

| Language    | 言語コード |
|:----------- |:-------------:|
| アフリカーンス語      | `af`          |
| アラビア語       | `ar`          |
| ベンガル語      | `bn`          |
| ボスニア語 (ラテン)      | `bs`          |
| Bulgarian      | `bg`          |
| カタロニア語      | `ca`          |
| 簡体中国語      | `zh-Hans`          |
| Croatian      | `hr`          |
| Czech      | `cs`          |
| Danish      | `da`          |
| Dutch      | `nl`          |
| Estonian      | `et`          |
| Finnish      | `fi`          |
| French      | `fr`          |
| German      | `de`          |
| Greek      | `el`          |
| ハイチ・クレオール語      | `ht`          |
| ヘブライ語      | `he`          |
| ヒンディー語      | `hi`          |
| ミャオ語      | `mww`          |
| Hungarian      | `hu`          |
| アイスランド語    | `is`  |
| インドネシア語      | `id`          |
| Italian      | `it`          |
| Japanese      | `ja`          |
| スワヒリ語      | `sw`          |
| クリンゴン語      | `tlh`          |
| Korean      | `ko`          |
| Latvian      | `lv`          |
| Lithuanian      | `lt`          |
| マレー語      | `ms`          |
| マルタ語      | `mt`          |
| ノルウェー語      | `nb`          |
| ペルシャ語      | `fa`          |
| Polish      | `pl`          |
| ポルトガル語 (ブラジル)     | `pt-br`          |
| Romanian      | `ro`          |
| Russian      | `ru`          |
| セルビア語 (ラテン)      | `sr-Latn`          |
| Slovak     | `sk`          |
| Slovenian      | `sl`          |
| Spanish      | `es`          |
| Swedish      | `sv`          |
| タミル語      | `ta`          |
| Thai      | `th`          |
| Turkish      | `tr`          |
| ウクライナ語      | `uk`          |
| ウルドゥ語      | `ur`          |
| ベトナム語      | `vi`          |
| ウェールズ語      | `cy`          |

## <a name="detect"></a>Detect

Translator Text API では、翻訳および音訳に使用できるすべての言語が検出されます。


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Translator Text API の言語一覧にプログラムでアクセスする

Languages メソッドを使用して、Translator Text API v3.0 でサポートされている言語の一覧を取得することができます。 機能、言語コード、英語またはサポートされている言語での言語名を条件にリストを表示できます。 リストは、新しい言語が使用できるようになると、Microsoft Translator サービスによって自動的に更新されます。

[Languages 操作の参照ドキュメント](reference/v3-0-languages.md)

## <a name="customization"></a>カスタマイズ

次の言語は、[カスタム翻訳ツール](https://aka.ms/CustomTranslator)を使用して、英語との間で双方向のカスタマイズが可能です。

| Language    | 言語コード |
|:----------- |:-------------:|
| アラビア語       | `ar`          |
| ベンガル語      | `bn`          |
| ボスニア語 (ラテン)      | `bs`          |
| Bulgarian      | `bg`          |
| 簡体中国語      | `zh-Hans`          |
|中国語 (繁体字)|   `zh-Hant`   |
| Croatian      | `hr`          |
| Czech      | `cs`          |
| Danish      | `da`          |
| Dutch      | `nl`          |
| English    | `en`     |
| Estonian      | `et`          |
| Finnish      | `fi`          |
| French      | `fr`          |
| German      | `de`          |
| Greek      | `el`          |
| ヘブライ語      | `he`          |
| ヒンディー語      | `hi`          |
| Hungarian      | `hu`          |
| アイスランド語 | `is` |
| インドネシア語|   `id`    |
| アイルランド語 | `ga`  |
| Italian      | `it`          |
| Japanese      | `ja`          |
| スワヒリ語|    `sw`    |
| Korean      | `ko`          |
| Latvian      | `lv`          |
| Lithuanian      | `lt`          |
| マダガスカル語| `mg`    |
| マオリ語| `mi`  |
| ノルウェー語      | `nb`          |
| ペルシャ語      | `fa`          |
| Polish      | `pl`          |
| ポルトガル語 (ブラジル) | `pt-br` |
| Romanian      | `ro`          |
| Russian      | `ru`          |
| サモア語|   `sm`    |
| セルビア語 (ラテン)      | `sr-Latn`          |
| Slovak     | `sk`          |
| Slovenian      | `sl`          |
| Spanish      | `es`          |
| Swedish      | `sv`          |
| Thai      | `th`          |
| Turkish      | `tr`          |
| ウクライナ語      | `uk`          |
| ベトナム語      | `vi`          |
| ウェールズ語 | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Microsoft Translator Web サイトのリストにアクセスする

Microsoft Translator Web サイトでは、Translator Text および Speech API でサポートされているすべての言語を簡単に検索できます。 このリストには、言語コードなどの開発者向け情報は含まれていません。

[言語リストを見る](https://www.microsoft.com/translator/languages.aspx)
