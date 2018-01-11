---
title: "Azure Machine Learning データ準備での Python 拡張機能の使用 | Microsoft Docs"
description: "このドキュメントでは、Python コードを使用してデータ準備の機能を拡張する方法の概要と、いくつかの詳細な例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4b888facdba2eb5ff48bcbf43c93c1b75183cbad
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="data-preparations-python-extensions"></a>データ準備の Python 拡張機能
組み込み機能間の機能性ギャップを埋める方法として、Azure Machine Learning データ準備には複数のレベルの拡張機能が含まれています。 このドキュメントでは、Python スクリプトを介して拡張機能を説明します。 

## <a name="custom-code-steps"></a>カスタム コード ステップ 
データ準備には、ユーザーがコードを記述できる次のカスタム ステップがあります。

* ファイル リーダー*
* ライター*
* 列の追加
* 高度なフィルター
* データ フローの変換
* パーティションの変換

*これらのステップは現在、Spark 実行でサポートされていません。

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
 

エラー  

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

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

その後、次のコマンドのいずれかを実行します。 

`conda install <libraryname>` 

or 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Mac で場所を探すには、Python のアプリ固有インストールとそのスクリプトがあるディレクトリを探します。 既定の場所は次のとおりです。 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

その後、次のコマンドのいずれかを実行します。 

`./conda install <libraryname>`

or 

`./pip install <libraryname>`

## <a name="column-data"></a>列のデータ 
ドット表記またはキー/値表記を使用して、行から列のデータにアクセスできます。 スペースや特殊文字が含まれている列名は、ドット表記を使用してアクセスできません。 Python 拡張機能の両方のモード (モジュールおよび式) で、`row` 変数が常に定義されている必要があります。 

例 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>ファイル リーダー 
### <a name="purpose"></a>目的 
ファイル リーダー拡張点を使用すると、データ フローへのファイル読み取りのプロセスを完全に制御できます。 システムがコードを呼び出し、プロセス対象のファイルの一覧を渡します。 コードは Pandas データフレームを作成して返す必要があります。 

>[!NOTE]
>この拡張点は Spark では機能しません。 


### <a name="how-to-use"></a>使用方法 
この拡張点には、**[Open Data Source]\(データ ソースを開く\)** ウィザードからアクセスします。 最初のページで **[File]\(ファイル\)** を選択し、ファイルの場所を選択します。 **[Choose File Parameters]\(ファイル パラメーターの選択\)** ページで、**[File Type]\(ファイルの種類\)** ドロップダウンリストから **[Custom File (Script)]\(カスタム ファイル (スクリプト)\)** を選択します。 

読み取る必要のあるファイルに関する情報を含む、"df" という名前の Pandas データフレームがコードに与えられます。 複数のファイルを含むディレクトリを開くことを選択した場合、データフレームには複数の行が含まれます。  

このデータフレームには、次の列があります。

- Path: 読み取るファイル。
- PathHint: ファイルの場所を示します。 値: Local、AzureBlobStorage、および AzureDataLakeStorage。
- AuthenticationType: ファイルにアクセスするために使用する認証の種類。 値: None、SasToken、および OAuthToken。
- AuthenticationValue: None または使用するトークン。

### <a name="syntax"></a>構文 
式 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


モジュール  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>ライター 
### <a name="purpose"></a>目的 
ライター拡張点を使用すると、データ フローからのデータ書き込みのプロセスを完全に制御できます。 システムがコードを呼び出し、データフレームを渡します。 コードはそのデータフレームを使用して、指定したとおりにデータを書き込みます。 

>[!NOTE]
>ライター拡張点は Spark では機能しません。


### <a name="how-to-use"></a>使用方法 
この拡張点は、データ フローの書き込み (スクリプト) ブロックを使用して追加できます。 トップレベルの **[Transformations]\(変換\)** メニューから使用できます。

### <a name="syntax"></a>構文 
式

```python
    df.to_csv('c:\\temp\\output.csv')
```

モジュール

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
このカスタム書き込みブロックは、一連のステップ実行の途中に入れることができます。 モジュールを使用する場合、書き込み関数はデータフレームを返す必要があり、それが続くステップ実行の入力となります。 

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
