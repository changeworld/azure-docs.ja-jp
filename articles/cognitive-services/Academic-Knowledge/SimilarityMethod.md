---
title: Similarity メソッド - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Similarity メソッドを使用すると、2 つの文字列の学術的な類似度を計算できます。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704850"
---
# <a name="similarity-method"></a>Similarity メソッド

**similarity** REST API を使用して 2 つの文字列間の学術的な類似度を計算します。 
<br>

**REST エンドポイント:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>要求パラメーター

パラメーター        |データ型      |必須 | 説明
----------|----------|----------|------------
**s1**        |String   |はい  |比較する文字列*
**s2**        |String   |はい  |比較する文字列*

<sub> *比較する文字列の長さは最大 1 MB です。</sub>
<br>

## <a name="response"></a>Response

名前 | 説明
--------|---------
**SimilarityScore**        |s1 と s2 のコサイン類似度を表す浮動小数点数値で、値が 1.0 に近いほど類似度が高く、値が -1.0 に近いほど類似度が低くなります

<br>

## <a name="successerror-conditions"></a>成功/エラー条件

HTTP の状態 | 理由 | Response
-----------|----------|--------
**200**         |Success | 浮動小数点数
**400**         | 不適切な要求または要求が無効です | エラー メッセージ      
**500**         |内部サーバー エラー | エラー メッセージ
**Timed out**     | 要求がタイムアウトしました。  | エラー メッセージ

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>例:2 つの部分的な要約の類似度を計算します
#### <a name="request"></a>要求:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
この例では、**similarity** API を使用して、2 つの部分的な要約の間の類似度スコアを生成します。
#### <a name="response"></a>応答:
```
0.520
```
#### <a name="remarks"></a>解説:
類似度スコアは、単語埋め込みによる学術的概念の評価によって決定されます。 この例では、0.52 は、2 つの部分的な要約がある程度似ていることを意味します。
<br>
