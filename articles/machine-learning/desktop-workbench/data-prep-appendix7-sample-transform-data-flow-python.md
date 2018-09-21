---
title: Azure Machine Learning データ準備ツールで可能なトランスフォーム データ フロー変換の例 | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning データ準備ツールで可能な「データ フローの変換」変換の例を示します
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ca780b51973a960caec3b9b7a80c8ba5621b5a0b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35641337"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>カスタム データ フロー変換のサンプル (Python) 
メニューでのこの変換の名前は、「**データ フローの変換 (スクリプト)**」です。 この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事をご覧ください。

## <a name="transform-frame"></a>フレームを変換する
### <a name="create-a-new-column-dynamically"></a>新しい列を動的に作成する 
列を動的に作成し (**city2**)、San Francisco の複数の異なるバージョンを既存の city 列からのものに調整します。
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>新しい集計を追加する
score 列に対して計算された最初と最後の集計を使用して新しいフレームを作成します。 これらは **risk_category** 列でグループ化されます。
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>列のウィンザー化 
列内の外れ値を減らすための式を満たすようにデータを再数式化します。
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>データ フローを変換する
### <a name="fill-down"></a>フィル ダウン 

フィル ダウンには 2 つの変換が必要です。 次の表のようなデータを仮定します。

|State         |City       |
|--------------|-----------|
|ワシントン    |レドモンド    |
|              |ベルビュー   |
|              |イサクアー   |
|              |シアトル    |
|カリフォルニア    |ロサンゼルス|
|              |San Diego  |
|              |サンノゼ   |
|テキサス         |ダラス     |
|              |サンアントニオ|
|              |ヒューストン    |

1. 次のコードを使用して、"列の追加 (スクリプト)" 変換を作成します。
```python
    row['State'] if len(row['State']) > 0 else None
```

2. 次のコードを含む "データ フローの変換 (スクリプト)" 変換を作成します。
```python
    df = df.fillna( method='pad')
```

この時点で、データは次の表のようになります。

|State         |newState         |City       |
|--------------|--------------|-----------|
|ワシントン    |ワシントン    |レドモンド    |
|              |ワシントン    |ベルビュー   |
|              |ワシントン    |イサクアー   |
|              |ワシントン    |シアトル    |
|カリフォルニア    |カリフォルニア    |ロサンゼルス|
|              |カリフォルニア    |San Diego  |
|              |カリフォルニア    |サンノゼ   |
|テキサス         |テキサス         |ダラス     |
|              |テキサス         |サンアントニオ|
|              |テキサス         |ヒューストン    |


### <a name="min-max-normalization"></a>最小最大正規化
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```