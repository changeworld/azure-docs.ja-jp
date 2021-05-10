---
title: グラフ検索のクエリ構文
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの検索クエリ構文を使用して、パイプライン グラフ内のノードを検索する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259228"
---
# <a name="graph-search-query-syntax"></a>グラフ検索のクエリ構文

この記事では、Azure Machine Learning でのグラフ検索機能について説明します。 

グラフ検索を使用すると、デバッグやパイプラインの構築中にノードにすばやく移動できます。 ツールバーの入力ボックスまたは左パネルの検索タブに、キーワードまたはクエリのいずれかを入力して、検索をトリガーできます。 一致した結果はすべてキャンバスで黄色で強調表示され、左パネルで結果を選択すると、キャンバスでノードが赤色で強調表示されます。

![グラフ検索エクスペリエンスの例を示すスクリーンショット](media/search/graph-search-0322.png)

グラフ検索では、ノード名とコメントに対するフルテキストのキーワード検索がサポートされています。 runStatus、duration、computeTarget などのノード プロパティでフィルター処理を行うこともできます。 キーワード検索は Lucene クエリに基づいています。 完全な検索クエリは次のようになります。  

**[[lucene クエリ] | [フィルター クエリ]]** 

Lucene クエリとフィルター クエリのどちらも使用できます。 両方を使用するには、区切り記号 **|** を使用します。 フィルター クエリの構文は、Lucene クエリよりも厳密です。 したがって、顧客の入力が両方に解析できる場合、フィルター クエリが適用されます。

たとえば、`data OR model | compute in {cpucluster}` を指定すると、これにより、`data` または `model` を含む名前またはコメントを持ち、compute が cpucluster であるノードが検索されます。
 

## <a name="lucene-query"></a>Lucene クエリ

グラフ検索では、ノード "name" と "comment" に対して Lucene の単純なクエリをフルテキスト検索構文として使用します。 次の Lucene 演算子がサポートされています。

 
- AND/OR
- **?** および **\*** 演算子を使用したワイルドカードでの照合。

### <a name="examples"></a>例

- 単純な検索: `JSON Data`

- AND/OR: `JSON AND Validation`

- 複数の AND/OR: `(JSON AND Validation) OR (TSV AND Training)`

 
- ワイルドカードでの照合: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Lucene クエリを "*" 文字で開始することはできません。

##  <a name="filter-query"></a>フィルター クエリ

 
フィルター クエリでは次のパターンを使用します。
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
次のノード プロパティをキーとして使用できます。

- runStatus
- compute
- duration
- reuse
- [発行]
- tags

使用できる演算子は次のとおりです。

- 以上: `>=`
- 以下: `<=`
- より大きい: `>`
- より小さい: `<`
- 等しい: `==`
- 次を含む: `=`
- 等号否定: `!=`
- In: `in`

 
 

### <a name="example"></a>例

- duration > 100;
- status in { Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>テクニカル ノート

- 複数のフィルター間の関係は "AND" です
- `>=,  >,  <, or  <=` を選択した場合、値は自動的に数値型に変換されます。 それ以外の場合は、文字列型が比較に使用されます。
- すべての文字列型の値について、比較では大文字と小文字は区別されません。
- 演算子 "In" は、値としてコレクションを想定しており、コレクション構文は `{name1, name2, name3}` です
- キーワード間のスペースは無視されます