---
title: Azure Machine Learning データ準備での Python 拡張機能の使用 | Microsoft Docs
description: このドキュメントでは、Python コードを使用してデータ準備の機能を拡張する方法の概要と、いくつかの詳細な例を示します
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.openlocfilehash: a713f5fcde31e0e25de080a65b71209011ef551d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35641018"
---
# <a name="data-preparations-python-extensions"></a>データ準備の Python 拡張機能
組み込み機能間の機能性ギャップを埋める方法として、Azure Machine Learning データ準備には複数のレベルの拡張機能が含まれています。 このドキュメントでは、Python スクリプトを介して拡張機能を説明します。 

## <a name="custom-code-steps"></a>カスタム コード ステップ 
データ準備には、ユーザーがコードを記述できる次のカスタム ステップがあります。

* 列の追加
* 高度なフィルター
* データ フローの変換
* パーティションの変換

## <a name="code-block-types"></a>コード ブロック タイプ 
これらのステップごとに、2 つのコード ブロック タイプをサポートします。 まず、そのまま実行される基本の Python 式をサポートします。 次に、Python モジュールをサポートします。このモジュールでは、ユーザーが記述するコードにおいて、既知のシグネチャを使用して特定の関数を呼び出します。

たとえば、次の 2 つの方法で、別の列の対数を計算する新しい列を追加できます。

式 

```python    
    math.log(row["Score"])
```

モジュール 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


モジュール モードの列の追加変換では、行変数を受け取って列の値を返す `newvalue` という関数が見つかります。 このモジュールには、他の関数、import などを含む任意の分量の Python コードを含めることができます。

それぞれの拡張機能の詳細については、以下のセクションで説明します。 

## <a name="imports"></a>インポートする 
式ブロック タイプを使用する場合でも、コードに **import** ステートメントを追加できます。 すべてコードの最初の数行にグループ化する必要があります。

正しい 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
モジュール ブロック タイプを使用する場合、通常の Python のルールに従って **import** ステートメントを使用できます。 

## <a name="default-imports"></a>既定の import
次の import は常に含まれており、コードで使用できます。 再インポートは不要です。 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>新しいパッケージのインストール
既定でインストールされていないパッケージを使用するには、まずデータ準備が使用する環境にそのパッケージをインストールする必要があります。 このインストールは、ローカル コンピューターと、実行場所になるすべてのコンピューティング ターゲットの両方で行う必要があります。

コンピューティング ターゲットにパッケージをインストールするには、プロジェクトのルート下の aml_config フォルダーにある conda_dependencies.yml ファイルを変更する必要があります。

### <a name="windows"></a>Windows 
Windows で場所を探すには、Python のアプリ固有インストールとそのスクリプトがあるディレクトリを探します。 既定の場所は次のとおりです。  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

その後、次のコマンドのいずれかを実行します。 

`conda install <libraryname>` 

or 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Mac で場所を探すには、Python のアプリ固有インストールとそのスクリプトがあるディレクトリを探します。 既定の場所は次のとおりです。 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

その後、次のコマンドのいずれかを実行します。 

`./conda install <libraryname>`

or 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>カスタム モジュールの使用
データ フローの変換 (スクリプト) で、次の Python コードを記述します。

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

列の追加 (スクリプト) で、コード ブロックの種類をモジュールに設定し、次の Python コードを記述します。

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
異なる実行コンテキスト (ローカル、Docker、Spark など) の場合は、適切な場所への絶対パスをポイントします。 このパスは、“os.getcwd() + relativePath” を使用して調べることができます。


## <a name="column-data"></a>列のデータ 
ドット表記またはキー/値表記を使用して、行から列のデータにアクセスできます。 スペースや特殊文字が含まれている列名は、ドット表記を使用してアクセスできません。 Python 拡張機能の両方のモード (モジュールおよび式) で、`row` 変数が常に定義されている必要があります。 

例 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>列の追加 
### <a name="purpose"></a>目的
列の追加拡張点を使用すると、新しい列を計算する Python コードを記述できます。 記述するコードは行全体にアクセスできます。 コードは行ごとに新しい列値を返す必要があります。 

### <a name="how-to-use"></a>使用方法
この拡張点は、列の追加 (スクリプト) ブロックを使用して追加できます。 トップレベルの **[Transformations]\(変換\)** メニューと、**列**のコンテキスト メニューから使用できます。 

### <a name="syntax"></a>構文
式

```python
    math.log(row["Score"])
```

モジュール 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>高度なフィルター
### <a name="purpose"></a>目的 
高度なフィルター拡張点を使用すると、カスタム フィルターを記述できます。 ユーザーは行全体にアクセスでき、ユーザーのコードは True (行を含める) または False (行を除外する) を返す必要があります。 

### <a name="how-to-use"></a>使用方法
この拡張点は、高度なフィルター (スクリプト) ブロックを使用して追加できます。 トップレベルの **[Transformations]\(変換\)** メニューから使用できます。 

### <a name="syntax"></a>構文

式

```python
    row["Score"] > 95
```

モジュール  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>データ フローの変換
### <a name="purpose"></a>目的 
データ フローの変換拡張点を使用すると、データ フローを完全に変換できます。 プロセス対象のすべての列と行が含まれる Pandas データフレームにアクセスできます。 コードは Pandas データフレームを新しいデータとともに返す必要があります。 

>[!NOTE]
>Python では、この拡張機能が使用される場合、メモリに読み込まれるすべてのデータは Pandas データフレームにあります。 
>
>Spark では、すべてのデータは 1 つのワーカー ノードに集められます。 データが多すぎると、ワーカーのメモリが不足する場合があります。 慎重に使用してください。

### <a name="how-to-use"></a>使用方法 
この拡張点は、データ フローの変換 (スクリプト) ブロックを使用して追加できます。 トップレベルの **[Transformations]\(変換\)** メニューから使用できます。 
### <a name="syntax"></a>構文 

式

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

モジュール 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>パーティションの変換  
### <a name="purpose"></a>目的 
パーティションの変換拡張点を使用すると、データ フローのパーティションを変換できます。 そのパーティションのすべての列と行が含まれる Pandas データフレームにアクセスできます。 コードは Pandas データフレームを新しいデータとともに返す必要があります。 

>[!NOTE]
>Python では、データのサイズによって、最終的にパーティションは 1 つまたは複数になる可能性があります。 Spark では、ある特定のワーカー ノード上のパーティションのデータを保持するデータフレームを扱うことになります。 どちらの場合も、データ セット全体にアクセスできると想定することはできません。 


### <a name="how-to-use"></a>使用方法
この拡張点は、パーティションの変換 (スクリプト) ブロックを使用して追加できます。 トップレベルの **[Transformations]\(変換\)** メニューから使用できます。 

### <a name="syntax"></a>構文 

式 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

モジュール 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>エラー値  
データ準備には、エラー値の概念が存在します。 

カスタムの Python コードでエラー値に遭遇する可能性があります。 エラー値は、`DataPrepError` という Python クラスのインスタンスです。 このクラスは Python の例外をラップし、複数のプロパティで構成されます。 プロパティには元の値のほか、その値が処理されたときに発生したエラーに関する詳細が含まれます。 


### <a name="datapreperror-class-definition"></a>DataPrepError クラス定義
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
データ準備の Python フレームワークにおける DataPrepError の作成は、一般的には次のようになります。 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>使用方法 
Python が拡張点で実行されるときに、前の作成方法により、戻り値として DataPrepError が生成されることがあります。 DataPrepError が発生する傾向が強いのは、拡張点でデータが処理されるタイミングです。 現時点で、カスタムの Python コードは、DataPrepError を有効なデータ型として処理する必要があります。

#### <a name="syntax"></a>構文 
式 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
モジュール 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
