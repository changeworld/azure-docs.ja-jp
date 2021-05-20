---
title: リリース ノート - カスタム翻訳ツール
titleSuffix: Azure Cognitive Services
description: カスタム翻訳ツールのリリース、機能強化、バグ修正、および既知の問題。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/03/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 14004b80004d5cdbf88761e8ae71327d2f098edd
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108778406"
---
# <a name="custom-translator-release-notes"></a>カスタム翻訳ツールのリリース ノート

このページには、カスタム翻訳ツール サービスの機能、機能強化、バグ修正、既知の問題に関する最新のリリース ノートがあります。

## <a name="2021-may-release"></a>2021 年 5 月リリース

### <a name="improvements-and-bug-fixes"></a>機能強化とバグの修正

- カスタム モデルの一般化を強化するための新しいトレーニング パイプラインと、より多くの顧客の用語 (単語やフレーズ) を保持するための容量を追加しました。
- 単語の配置のバグを修正するために、カスタム翻訳ツールのベースラインを更新しました。 影響を受ける言語ペアの一覧を参照してください*。

### <a name="language-pair-list"></a>言語ペアの一覧

| ソース言語   | ターゲット言語 |
|-------------------|-----------------|
| アラビア語 (ar) | 英語 (en-us)|
| ブラジル ポルトガル語 (pt)    | 英語 (en-us)|
| ブルガリア語 (bg)    | 英語 (en-us)|
| 簡体中国語 (zh-Hans)    | 英語 (en-us)|
| 繁体中国語 (zh-Hant)    | 英語 (en-us)|
| クロアチア語 (hr)    | 英語 (en-us)|
| チェコ語 (cs)    | 英語 (en-us)|
| デンマーク語 (da)    | 英語 (en-us)|
| オランダ語 (nl)    | 英語 (en-us)|
| 英語 (en-us)    | アラビア語 (ar)|
| 英語 (en-us)    | ブルガリア語 (bg)|
| 英語 (en-us)    | 簡体中国語 (zh-Hans)|
| 英語 (en-us)    | 繁体中国語 (zh-Hant)|
| 英語 (en-us)    | チェコ語 (cs)|
| 英語 (en-us)    | デンマーク語 (da)|
| 英語 (en-us)    | オランダ語 (nl)|
| 英語 (en-us)    | エストニア語 (et)|
| 英語 (en-us)    | フィジー語 (fj)|
| 英語 (en-us)    | フィンランド語 (fi)|
| 英語 (en-us)    | フランス語|
| 英語 (en-us)    | ギリシャ語 (el)|
| 英語 (en-us)    | ヒンディー語|
| 英語 (en-us)    | ハンガリー語 (hu)|
| 英語 (en-us)    | アイスランド語 (is)|
| 英語 (en-us)    | インドネシア語 (id)|
| 英語 (en-us)    | イヌクティトット語 (iu)|
| 英語 (en-us)    | アイルランド語 (ga)|
| 英語 (en-us)    | イタリア語 (it)|
| 英語 (en-us)    | 日本語 (ja)|
| 英語 (en-us)    | 韓国語 (ko)|
| 英語 (en-us)    | リトアニア語 (lt)|
| 英語 (en-us)    | ノルウェー語 (nb)|
| 英語 (en-us)    |  ポーランド語 (pl)|
| 英語 (en-us)    | ルーマニア語 (ro)|
| 英語 (en-us)    | サモア語|
| 英語 (en-us)    | スロバキア語 (sk)|
| 英語 (en-us)    | スペイン語 (es)|
| 英語 (en-us)    | スウェーデン語 (sv)|
| 英語 (en-us)    | タヒチ語 (ty)|
| 英語 (en-us)    | タイ語 (th)|
| 英語 (en-us)    | トンガ語 (to)|
| 英語 (en-us)    | トルコ語 (tr)|
| 英語 (en-us)    | ウクライナ語|
| 英語 (en-us)    | ウェールズ語 (cy)|
| エストニア語 (et)    | 英語 (en-us)|
| フィジー語    | 英語 (en-us)|
| フィンランド語 (fi)    | 英語 (en-us)|
| ドイツ語 (de)    | 英語 (en-us)|
| ギリシャ語 (el)    | 英語 (en-us)|
| ハンガリー語 (hu)    | 英語 (en-us)|
| アイスランド語 (is)    | 英語 (en-us)|
| インドネシア語 (id)    | 英語 (en-us)
| イヌクティトット語 (iu)    | 英語 (en-us)|
| アイルランド語 (ga)    | 英語 (en-us)|
| イタリア語 (it)    | 英語 (en-us)|
| 日本語 (ja)    | 英語 (en-us)|
| カザフ語 (kk)    | 英語 (en-us)|
| 韓国語 (ko)    | 英語 (en-us)|
| リトアニア語 (lt)    | 英語 (en-us)|
| マダガスカル語 (mg)    | 英語 (en-us)|
| マオリ語 (mi)    | 英語 (en-us)|
| ノルウェー語 (nb)    | 英語 (en-us)|
| ペルシャ語 (fa)    | 英語 (en-us)|
|  ポーランド語 (pl)    | 英語 (en-us)|
| ルーマニア語 (ro)    | 英語 (en-us)|
| ロシア語 (ru)    | 英語 (en-us)|
| スロバキア語 (sk)    | 英語 (en-us)|
| スペイン語 (es)    | 英語 (en-us)|
| スウェーデン語 (sv)    | 英語 (en-us)|
| タヒチ語 (ty)    | 英語 (en-us)|
| タイ語 (th)    | 英語 (en-us)|
| トンガ語 (to)    | 英語 (en-us)|
| トルコ語 (tr)    | 英語 (en-us)|
| ベトナム語 (vi)    | 英語 (en-us)|
| ウェールズ語 (cy)    | 英語 (en-us)|