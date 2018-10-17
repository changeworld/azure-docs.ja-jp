---
title: サポートされている言語 - Translator Speech API
titlesuffix: Azure Cognitive Services
description: Translator Speech API でサポートされている言語を確認できます。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX
ms.openlocfilehash: 3c0d0e17026dc0c970138c30f83dc9eb98db4228
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950184"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Translator Speech API でサポートされている言語
音声翻訳では、次の言語がサポートされています。 両方の言語が音声翻訳でサポートされている場合、音声間翻訳または音声テキスト変換を利用できます。 ターゲット言語が音声翻訳でサポートされていない場合は、音声テキスト変換のみを利用できます。 

| 音声言語    |
|:----------- |
| アラビア語 (現代標準)      |
| 中国語 (標準)      |
| 英語      |
| フランス語      |
| ドイツ語      |
| イタリア語      |
| 日本語      |
| ポルトガル語 (ブラジル)     |
| ロシア語      |
| スペイン語      | 

Translator Speech API では、音声テキスト変換のターゲット言語として、次の言語がサポートされています。 

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
|アイスランド語|`is`          |
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

## <a name="access-the-list-programmatically"></a>プログラミングによるリストへのアクセス

言語リソースを使用すると、サポート対象言語のリストにプログラミングを使用してアクセスできます。 この一覧には、各言語コードと、英語表記またはサポートされているその他の言語表記による言語名が記載されています。 リストは、新しい言語が使用できるようになると、Translator Speech サービスによって自動的に更新されます。

言語リソースは、音声、テキスト、およびテキスト読み上げに対してサポートされている言語の一覧を返します。 言語リソースに認証は必要ありません。

[言語メソッドを試すには API リファレンスをご覧ください](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Microsoft Translator Web サイトのリストにアクセスする

Microsoft Translator Web サイトでは、Translator Text および Speech API でサポートされているすべての言語を簡単に検索できます。 このリストには、言語コードなどの開発者向け情報は含まれていません。

[言語リストを見る](https://www.microsoft.com/translator/languages.aspx) 
