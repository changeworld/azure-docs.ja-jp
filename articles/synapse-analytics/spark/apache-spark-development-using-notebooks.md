---
title: Synapse ノートブックの使用方法
description: この記事では、データの準備と視覚化を行うために、Synapse ノートブックを作成および開発する方法について学習します。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/08/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: de703ea3f85025657b4c71089f158b7d1db2189d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013607"
---
# <a name="create-develop-and-maintain-synapse-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Synapse ノートブックを作成、開発、管理する

Synapse ノートブックは、ライブ コード、視覚化、および説明テキストを含むファイルを作成するための Web インターフェイスです。 ノートブックは、アイデアを確認し、簡単な実験を使用してデータから分析情報を得るのに最適な場所です。 また、ノートブックは、データの準備、データの視覚化、機械学習、およびその他のビッグ データのシナリオで広く使用されています。

Synapse ノートブックでは、次のことができます。 

* 設定作業をせずに使用を開始する。
* 組み込みのエンタープライズ セキュリティ機能を使用してデータを安全に保つ。
* 未加工の形式 (CSV、txt、JSON など) 全体のデータ、処理されたファイル形式 (parquet、Delta Lake、ORC など)、および Spark と SQL に対する SQL の表形式データ ファイルを分析する。
* 高度な作成機能と組み込みのデータの視覚化で生産性を高める。

この記事では、Synapse Studio でノートブックを使用する方法について説明します。


## <a name="create-a-notebook"></a>ノートブックを作成する

ノートブックを作成するには、2 つの方法があります。 新しいノートブックを作成することも、既存のノートブックを **オブジェクト エクスプローラー** から Synapse ワークスペースにインポートすることもできます。 Synapse ノートブックでは、標準の Jupyter Notebook IPYNB ファイルが認識されます。

![ノートブックの新規作成またはインポートのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>ノートブックを開発する

ノートブックは、個別にまたはグループとして実行できるコードまたはテキストの個々のブロックである、セルで構成されます。

ノートブックを開発するための豊富な操作が提供されます。
+ [セルを追加する](#add-a-cell)
+ [第一言語を設定する](#set-a-primary-language)
+ [複数の言語を使用する](#use-multiple-languages)
+ [一時テーブルを使用して言語間でデータを参照する](#use-temp-tables-to-reference-data-across-languages)
+ [IDE スタイルの IntelliSense](#ide-style-intellisense)
+ [コード スニペット](#code-snippets)
+ [ツールバー ボタンを使用してテキスト セルを書式設定する](#format-text-cell-with-toolbar-buttons)
+ [セル操作を元に戻す/やり直す](#undo-redo-cell-operation)
+ [コード セルのコメント化](#Code-cell-commenting)
+ [セルを移動する](#move-a-cell)
+ [セルを削除する](#delete-a-cell)
+ [セル入力を折りたたむ](#collapse-a-cell-input)
+ [セル出力を折りたたむ](#collapse-a-cell-output)
+ [ノートブックのアウトライン](#notebook-outline)

<h3 id="add-a-cell">セルを追加する</h3>

ノートブックに新しいセルを追加するには、複数の方法があります。

1. 2 つのセル間のスペースをポイントし、 **[コード]** または **[Markdown]** を選択します。
    ![add-azure-notebook-cell-with-cell-button のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)

2. [コマンド モードの aznb ショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルの上にセルを挿入するには、**A** キーを押します。 現在のセルの下にセルを挿入するには、**B** キーを押します。

---

<h3 id="set-a-primary-language">第一言語を設定する</h3>

Synapse ノートブックでは、次の 4 つの Apache Spark 言語がサポートされます。

* PySpark (Python)
* Spark (Scala)
* Spark SQL
* .NET Spark (C#)

上部のコマンド バーにあるドロップダウン リストから、新しく追加されたセルの第一言語を設定できます。

   ![既定の Synapse 言語のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-default-language.png)


<h3 id="use-multiple-languages">複数の言語を使用する</h3>

1 つのノートブックで、セルの先頭に正しい言語マジック コマンドを指定することで、複数の言語を使用できます。 次の表に、セルの言語を切り替えるマジック コマンドを一覧表示します。

|マジック コマンド |Language | 説明 |  
|---|------|-----|
|%%pyspark| Python | Spark コンテキストに対して、**Python** クエリを実行します。  |
|%%spark| Scala | Spark コンテキストに対して、**Scala** クエリを実行します。  |  
|%%sql| SparkSQL | Spark コンテキストに対して、**SparkSQL** クエリを実行します。  |
|%%csharp | .NET for Spark C# | Spark コンテキストに対して、 **.NET for Spark C#** クエリを実行します。 |

次の図は、**Spark(Scala)** ノートブックで、 **%%pyspark** マジック コマンドを使用する PySpark クエリ、または **%%sql** マジック コマンドでの SparkSQL クエリの記述方法の例です。 ノートブックの第一言語が PySpark に設定されていることに注目してください。

   ![Synapse spark マジック コマンドのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)


<h3 id="use-temp-tables-to-reference-data-across-languages">一時テーブルを使用して言語間でデータを参照する</h3>

Synapse ノートブックでは、異なる言語間でデータや変数を直接参照することはできません。 Spark では、複数の言語間で一時テーブルを参照することができます。 以下は、回避策として Spark の一時テーブルを使用して、`PySpark` および `SparkSQL` で `Scala` DataFrame を読み取る方法の例です。

1. セル 1 で、Scala を使用して SQL プール コネクタから DataFrame を読み取り、一時テーブルを作成します。

   ```scala
   %%spark
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. セル 2 で、Spark SQL を使用してデータに対してクエリを実行します。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. セル 3 では、PySpark のデータを使用します。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

<h3 id="ide-style-intellisense">IDE スタイルの IntelliSense</h3>

Synapse ノートブックは、IDE スタイルの IntelliSense をセル エディターに取り込むために、Monaco エディターと統合されています。 構文の強調表示、エラー マーカー、および自動コード補完は、コードを記述したり、問題をより迅速に特定したりするために役立ちます。

IntelliSense の機能は、言語によって異なる成熟度レベルにあります。 次の表を使用して、サポートされている内容を確認してください。

|Languages| 構文の強調表示 | 構文のエラー マーカー  | 構文のコード補完 | 変数のコード補完| システム関数のコード補完| ユーザー関数のコード補完| スマート インデント | コードの折りたたみ|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|はい|はい|はい|はい|はい|はい|はい|はい|
|Spark (Scala)|はい|はい|はい|はい|はい|はい|-|はい|
|SparkSQL|はい|はい|はい|はい|はい|-|-|-|
|.NET for Spark (C#)|はい|はい|はい|はい|はい|はい|はい|Yes|

>[!Note]
> 変数コード補完、システム関数コード補完、.NET for Spark のユーザー関数コード補完 (C#) を利用するには、アクティブな Spark セッションが必要です。

<h3 id="code-snippets">コード スニペット</h3>

Synapse ノートブックには、Spark セッションの構成、Spark DataFrame としてのデータの読み取り、matplotlib でのグラフの描画など、一般的に使用されるコード パターンの入力を容易にするコード スニペットが用意されています。

スニペットは、他の候補と一緒に [IDE スタイルの IntelliSense のショートカット キー](#ide-style-intellisense)に表示されます。 コード スニペットの内容は、コード セル言語に準拠したものとなります。 使用可能なスニペットを確認するには、「**Snippet**」と入力するか、コード セル エディターのスニペットのタイトルに表示されるキーワードを入力します。 たとえば、「**read**」と入力すると、さまざまなデータ ソースからデータを読み取るスニペットの一覧が表示されます。

![Synapse コード スニペットのアニメーション GIF](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)


<h3 id="format-text-cell-with-toolbar-buttons">ツールバー ボタンを使用してテキスト セルを書式設定する</h3>

テキスト セル ツールバーの書式ボタンを使用して、一般的なマークダウン アクションを実行することができます。 たとえば、テキストの太字設定、テキストの斜体設定、ドロップダウンを使用した段落と見出し、コードの挿入、順序指定されていないリストの挿入、順序指定済みリストの挿入、ハイパーリンクの挿入、URL からの画像の挿入などです。

  ![Synapse テキスト セル ツール バーのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar-preview.png)

---

<h3 id="undo-redo-cell-operation">セル操作を元に戻す/やり直す</h3>

**[元に戻す]**  /  **[やり直す]** ボタンを選択するか、**Z** / **Shift+Z** キーを押して、最新のセル操作を取り消します。 これで、最大で 10 個の最新の過去のセル操作を元に戻すかやり直すことができます。

   ![aznb の Synapse セルを元に戻すのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-undo-cells-aznb.png)

サポートされているセルを元に戻す操作:
+ セルの挿入/削除: **[元に戻す]** を選択すると、削除操作を取り消すことができます。テキスト コンテンツはセルと共に保持されます。
+ セルの順序を変更する。
+ パラメーターを切り替える。
+ コード セルと Markdown セルの間で変換を行う。

> [!NOTE]
> セル内のテキスト操作とコード セルのコメント化操作は元に戻せません。
> これで、最大で 10 個の最新の過去のセル操作を元に戻すかやり直すことができます。


---

<h3 id="Code-cell-commenting">コード セルのコメント化</h3>

1. ノートブックのツール バーの **[コメント]** ボタンを選択して、 **[コメント]** ペインを開きます。

   ![Synapse のコメント ボタンのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-comments-button.png)

2. コード セル内のコードを選択し、 **[コメント]** ペインで **[新規]** をクリックし、コメントを追加してから、 **[コメントの投稿]** ボタンをクリックして保存します。

   ![Synapse の新しいコメントのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-new-comments.png)

3. コメントの横にある **[詳細]** ボタンをクリックすると、**コメントの編集**、**スレッドの解決**、または **スレッドの削除** を実行できます。 

   ![Synapse のコメントの編集のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-edit-comments.png)

---

<h3 id="move-a-cell">セルを移動する</h3>

セルの左側をクリックし、目的の位置までドラッグします。 
    ![Synapse のセルの移動のアニメーション GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

<h3 id="delete-a-cell">セルを削除する</h3>

セルを削除するには、セルの右側にある削除ボタンを選択します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを削除するには、**Shift + D** キーを押します。 

   ![Azure ノートブックのセルの削除のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

<h3 id="collapse-a-cell-input">セル入力を折りたたむ</h3>

セル ツールバーで **[More commands]\(その他のコマンド\)** 省略記号 (...) を選択し、 **[入力の非表示]** を選択して、現在のセルの入力を折りたたみます。 これを展開するには、セルが折りたたまれている状態で **[Show input]\(入力を表示する\)** を選択します。

   ![Azure ノートブックのセル入力の折りたたみのアニメーション GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

<h3 id="collapse-a-cell-output">セル出力を折りたたむ</h3>

セル ツールバーで **[More commands]\(その他のコマンド\)** 省略記号 (...) を選択し、 **[出力の非表示]** を選択して、現在のセルの出力を折りたたみます。 これを展開するには、セルの出力が非表示になっている状態で **[出力の表示]** を選択します。

   ![Azure ノートブックのセル出力の折りたたみのアニメーション GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

<h3 id="notebook-outline">ノートブックのアウトライン</h3>

アウトライン (目次) では、素早くナビゲーションできるようにサイドバー ウィンドウ内に任意の Markdown セルの最初の Markdown ヘッダーが表示されます。 アウトラインのサイドバーは、最適な方法で画面に合わせてサイズを変更したり折りたたんだりすることができます。 ノートブックのコマンド バーにある **[アウトライン]** ボタンを選択すると、サイドバーを開いたり非表示にしたりできます。

![Azure ノートブックのアウトラインのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-outline.png)

---


## <a name="run-notebooks"></a>ノートブックを実行する

ノートブックのコード セルは、個別に、または一度にすべて実行できます。 ノートブックには各セルの状態と進行状況が示されます。

### <a name="run-a-cell"></a>セルを実行する

セルでコードを実行するには、いくつかの方法があります。

1. 実行するセルをポイントし、 **[セルの実行]** ボタンを選択するか、**Ctrl + Enter** キーを押します。

   ![セル 1 の実行のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [コマンド モードのショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルを実行し、下のセルを選択するには、**Shift + Enter** キーを押します。 現在のセルを実行し、新しいセルを下に挿入するには、**Alt + Enter** キーを押します。

---

### <a name="run-all-cells"></a>すべてのセルを実行する
現在のノートブック内のすべてのセルを順に実行するには、 **[すべて実行]** ボタンを選択します。

   ![すべてのセルの実行のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>上または下のすべてのセルを実行する

**[すべて実行]** ボタンからドロップダウン リストを展開し、 **[Run cells above]\(上のセルの実行\)** を選択して、現在のセルの上にあるものをすべて順に実行します。 現在のセルの下にあるものをすべて実行するには、 **[Run cells below]\(下のセルの実行\)** を選択します。

   ![Azure ノートブックの上または下のセルの実行のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>実行中のすべてのセルを取り消す

実行中のセルまたはキューで待機しているセルを取り消すには、 **[すべて取り消し]** ボタンを選択します。 
   ![Azure ノートブックのすべてのセルの取り消しのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---

### <a name="notebook-reference"></a>ノートブック参照

```%run <notebook path>``` マジック コマンドを使用して、現在のノートブックのコンテキスト内で別のノートブックを参照することができます。 参照ノートブックで定義されているすべての変数を、現在のノートブックで使用できます。 ```%run``` マジック コマンドでは、入れ子になった呼び出しはサポートされますが、再帰呼び出しはサポートされません。 ステートメントの深さが **5** を超えると、例外が発生します。  

例: ``` %run /<path>/Notebook1 { "parameterInt": 1, "parameterFloat": 2.5, "parameterBool": true,  "parameterString": "abc" } ```.

Notebook リファレンスは、対話モードと Synapse パイプラインの両方で機能します。

> [!NOTE]
> - ```%run``` コマンドでは現在、絶対パスまたはノートブック名のみをパラメーターとして渡すことのみがサポートされており、相対パスはサポートされていません。 
> - ```%run``` コマンドでは現在、4 つのパラメーター値型 `int`、`float`、`bool`、`string` のみがサポートされており、変数置換操作はサポートされていません。
> - 参照されているノートブックを発行する必要があります。 ノートブックを参照するには、それらを発行する必要があります。 Synapse Studio では、Git リポジトリから発行されていないノートブックは認識されません。 
> - 参照されるノートブックでは、深さが **5** を超えるステートメントはサポートされていません。
>

---

### <a name="variable-explorer"></a>変数エクスプローラー

Synapse ノートブックは、PySpark (Python) セルの現在の Spark セッションでの変数の名前、型、長さ、値の一覧を表示するための、組み込み変数エクスプローラーを提供します。 コード セルに定義されている変数が増えると、自動的に表示される変数も増えます。 各列ヘッダーをクリックすると、テーブル内の変数が並べ替えられます。

ノートブックのコマンド バーの **[変数]** ボタンを選択して、変数エクスプローラーを開いたり非表示にしたりできます。

![Azure ノートブックの変数エクスプローラーのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)

> [!NOTE]
> 変数エクスプローラーは Python にのみ対応しています。

---

### <a name="cell-status-indicator"></a>セルの状態インジケーター

セルの下に詳細なセルの実行の状態が表示されます。これは、現在の進行状況を確認するのに役立ちます。 セルの実行が完了すると、合計期間と終了時間を含む実行の概要が表示され、今後の参照用に保持されます。

![セルの状態のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 進行状況インジケーター

Synapse ノートブックは、純粋に Spark ベースです。 コード セルは、サーバーレス Apache Spark プールに対してリモートで実行されます。 Spark ジョブの進行状況インジケーターは、リアルタイムの進行状況バーで示されます。これは、ジョブの実行状態を把握するのに役立ちます。
ジョブごとまたはステージごとのタスク数は、Spark ジョブの並列レベルを識別するのに役立ちます。 また、ジョブ (またはステージ) の名前のリンクを選択することで、特定のジョブ (またはステージ) の Spark UI をさらに掘り下げて調べることもできます。


![Spark 進行状況インジケーターのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark セッションの構成

**[Configure session]\(セッションの構成\)** では、現在の Spark セッションに設定するタイムアウト期間、Executor の数とサイズを指定できます。 Spark セッションを再起動すると、構成の変更が有効になります。 キャッシュされたノートブック変数はすべて消去されます。

[![セッション管理のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark セッションを構成するためのマジック コマンド
マジック コマンド **%%configure** を使用して Spark セッションの設定を指定することもできます。 設定を有効にするには、Spark セッションを再起動する必要があります。 **%%configure** はノートブックの先頭で実行することをお勧めします。 次に例を示します。有効なパラメーターの一覧については、 https://github.com/cloudera/livy#request-body を参照してください。 

```json
%%configure
{
    //You can get a list of valid parameters to config the session from https://github.com/cloudera/livy#request-body.
    "driverMemory":"28g", // Recommended values: ["28g", "56g", "112g", "224g", "400g", "472g"]
    "driverCores":4, // Recommended values: [4, 8, 16, 32, 64, 80]
    "executorMemory":"28g",
    "executorCores":4, 
    "jars":["abfs[s]://<file_system>@<account_name>.dfs.core.windows.net/<path>/myjar.jar","wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>/myjar1.jar"],
    "conf":{
    //Example of standard spark property, to find more available properties please visit:https://spark.apache.org/docs/latest/configuration.html#application-properties.
        "spark.driver.maxResultSize":"10g",
    //Example of customized property, you can specify count of lines that Spark SQL returns by configuring "livy.rsc.sql.num-rows".
        "livy.rsc.sql.num-rows&quot;:&quot;3000" 
    }
}
```
> [!NOTE]
> - %%configure では、"DriverMemory" と "ExecutorMemory" を同じ値に設定することをお勧めします。"driverCores" と "executorCores" も同様です。
> - Synapse パイプラインで Spark セッション構成マジック コマンドを使用できます。 それは、最上位レベルで呼び出された場合にのみ有効になります。 参照されたノートブックで使用されている %%configure は無視されます。
> - Spark の構成プロパティは、"conf" の本文で使用する必要があります。 Spark 構成プロパティの最上位レベルの参照はサポートされていません。
>

## <a name="bring-data-to-a-notebook"></a>データをノートブックに取り込む

以下のコード サンプルに示すように、Azure Blob Storage、Azure Data Lake Store Gen 2、および SQL プールからデータを読み込むことができます。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2 から CSV を Spark DataFrame として読み取る

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage から CSV を Spark DataFrame として読み取る

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>プライマリ ストレージ アカウントからデータを読み取る

プライマリ ストレージ アカウントのデータに直接アクセスすることができます。 秘密鍵を指定する必要はありません。 データ エクスプローラーで、ファイルを右クリックし、 **[新しいノートブック]** を選択して、データ エクストラクターが自動生成された新しいノートブックを表示します。

![データからセルのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)


## <a name="ipython-widgets"></a>IPython ウィジェット

ウィジェットは、多くの場合、スライダーやテキストボックスなどのコントロールとして、ブラウザーに表示されるイベントフルな Python オブジェクトです。IPython ウィジェットは Python 環境でのみ動作し、他の言語 (Scala、SQL、C# など) ではまだサポートされていません。 

### <a name="to-use-ipython-widget"></a>IPython ウィジェットを使用するには
1. Jupyter ウィジェット フレームワークを使用するには、最初に `ipywidgets` モジュールをインポートする必要があります。
   ```python
   import ipywidgets as widgets
   ```
2. トップレベルの `display` 関数を使用してウィジェットをレンダリングしたり、**ウィジェット** の種類の式をコード セルの最後の行に残したりすることができます。
   ```python
   slider = widgets.IntSlider()
   display(slider)
   ```

   ```python
   slider = widgets.IntSlider()
   slider
   ```
   
3. セルを実行すると、ウィジェットが出力領域に表示されます。

   ![IPython ウィジェット スライダーのスクリーンショット](./media/apache-spark-development-using-notebooks/ipython-widgets-slider.png)

4. 複数の `display()` 呼び出しを使用して同じウィジェット インスタンスを複数回レンダリングできますが、それらは互いに同期されたままです。

   ```python
   slider = widgets.IntSlider()
   display(slider)
   display(slider)
   ```

   ![IPython ウィジェット スライダーのスクリーンショット](./media/apache-spark-development-using-notebooks/ipython-widgets-multiple-sliders.png)

5. 2 つのウィジェットを互いに独立してレンダリングするには、2 つのウィジェット インスタンスを作成します。

   ```python
   slider1 = widgets.IntSlider()
   slider2 = widgets.IntSlider()
   display(slider1)
   display(slider2)
   ```


### <a name="supported-widgets"></a>サポートされているウィジェット

|ウィジェットの種類|Widgets (ウィジェット)|
|--|--|
|数値ウィジェット|IntSlider、FloatSlider、FloatLogSlider、IntRangeSlider、FloatRangeSlider、IntProgress、FloatProgress、BoundedIntText、BoundedFloatText、IntText、FloatText|
|ブール値ウィジェット|ToggleButton、Checkbox、Valid|
|選択ウィジェット|Dropdown、RadioButtons、Select、SelectionSlider、SelectionRangeSlider、ToggleButtons、SelectMultiple|
|文字列ウィジェット|Text、Text area、Combobox、Password、Label、HTML、HTML Math、Image、Button|
|再生 (アニメーション) ウィジェット|日付の選択、カラー ピッカー、コントローラー|
|コンテナーまたはレイアウト ウィジェット|Box、HBox、VBox、GridBox、Accordion、Tabs、Stacked|


### <a name="known-limitations"></a>既知の制限事項

1. 次のウィジェットはまだサポートされていません。以下のような対応する回避策に従ってください。

   |機能|回避策|
   |--|--|
   |`Output` ウィジェット|代わりに `print()` 関数を使用して、stdout にテキストを書き込むことができます。|
   |`widgets.jslink()`|`widgets.link()` 関数を使用して、似たような 2 つのウィジェットをリンクできます。|
   |`FileUpload` ウィジェット| まだサポートされていません。|

2. Synapse によって提供されるグローバル `display` 関数では、IPython `display` 関数とは異なる 1 回の呼び出しで複数のウィジェットを表示すること (つまり、`display(a, b)`) はサポートされません。
3. IPython ウィジェットを含むノートブックを閉じると、対応するセルを再度実行するまで表示したり操作したりすることはできません。


---
## <a name="save-notebooks"></a>ノートブックを保存する

ワークスペースには、単一のノートブックまたはすべてのノートブックを保存できます。

1. 単一のノートブックに加えた変更を保存するには、ノートブックのコマンド バーにある **[公開]** ボタンを選択します。

   ![ノートブックの公開のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. ワークスペース内のすべてのノートブックを保存するには、ワークスペースのコマンド バーにある **[すべて公開]** ボタンを選択します。 

   ![すべての公開のスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

ノートブックのプロパティでは、保存時にセル出力を含めるかどうかを構成できます。

   ![ノートブックのプロパティのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

<!--
## Export a notebook
You can Export your notebook to other standard formats. Synapse notebook supports to be exported into: 

+ Standard Notebook file(.ipynb) that is usually used for Jupyter notebooks. 
+ HTML file(.html) that can be opened from browser directly.  
+ Python file(.py).  
+ Latex file(.tex).  

   ![Screenshot of notebook-export](./media/apache-spark-development-using-notebooks/synapse-notebook-export.png)

-->

## <a name="magic-commands"></a>マジック コマンド
Synapse ノートブックでは、使い慣れた Jupyter マジック コマンドを使用できます。 以下の一覧で、現在使用可能なマジック コマンドを確認してください。 ユーザーのニーズに合ったマジック コマンドを引き続き作成できるよう、[GitHub でユース ケース](https://github.com/MicrosoftDocs/azure-docs/issues/new)についてお知らせください。

> [!NOTE]
> Synapse パイプラインでは、次のマジック コマンドのみがサポートされています: %%pyspark、%%spark、%%csharp、%%sql。 
>


使用可能なライン マジック: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history)、[%run](#notebook-reference)、[%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

使用可能なセル マジック: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)、[%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html)、[%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>ノートブックを統合する

### <a name="add-a-notebook-to-a-pipeline"></a>ノートブックをパイプラインに追加する

右上隅にある **[パイプラインへの追加]** ボタンを選択して、ノートブックを既存のパイプラインに追加するか、新しいパイプラインを作成します。

![パイプラインへのノートブックの追加のスクリーンショット](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>パラメーター セルを指定する

ノートブックをパラメーター化するには、省略記号 (...) を選択して、セル ツールバーの **[more commands]\(その他のコマンド\)** にアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

![Azure ノートブックのパラメーターの切り換えのクリーンショット](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

パラメーター セルが検索され、このセルは実行時に渡されるパラメーターの既定値として扱われます。 実行エンジンは、既定値を上書きするために、入力パラメーターを含んだ新しいセルをパラメーター セルの下に追加します。 


### <a name="assign-parameters-values-from-a-pipeline"></a>パイプラインからパラメーター値を割り当てる

パラメーターを使ってノートブックを作成したら、Synapse Notebook アクティビティを使用して、パイプラインから実行できます。 アクティビティをパイプライン キャンバスに追加した後、 **[設定]** タブの **[基本パラメーター]** セクションで、パラメーターの値を設定できます。 

![パラメーターの割り当てのスクリーンショット](./media/apache-spark-development-using-notebooks/assign-parameter.png)

パラメーター値を割り当てるときは、[パイプライン式言語](../../data-factory/control-flow-expression-language-functions.md) または [システム変数](../../data-factory/control-flow-system-variables.md)を使用できます。



## <a name="shortcut-keys"></a>ショートカット キー

Jupyter Notebook と同様に、Synapse ノートブックにはモーダル ユーザー インターフェイスが採用されています。 キーボードの動作は、ノートブック セルのモードによって異なります。 Synapse ノートブックでは、特定のコード セルに対して、コマンド モードと編集モードという 2 つのモードがサポートされます。

1. 入力を求めるテキスト カーソルがない場合、セルはコマンド モードになります。 セルがコマンド モードの場合、ノートブックを全体として編集できますが、個々のセルに入力することはできません。 `ESC` キーを押すか、マウスを使用してセルのエディター領域の外側を選択し、コマンド モードに入ります。

   ![コマンド モードのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. 編集モードは、エディター領域への入力を求めるテキスト カーソルによって示されます。 セルが編集モードの場合、セルに入力することができます。 `Enter` キーを押すか、マウスを使用してセルのエディター領域を選択し、編集モードに入ります。
   
   ![編集モードのスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>コマンド モードのショートカット キー

| アクション |Synapse ノートブックのショートカット  |
|--|--|
|現在のセルを実行して下のものを選択する | Shift + Enter |
|現在のセルを実行して下に挿入する | Alt + Enter |
|現在のセルを実行する| Ctrl + Enter |
|上のセルを選択する| 上へ |
|下のセルを選択する| [下へ] |
|前のセルを選択する| K |
|次のセルを選択する| J |
|上にセルを挿入する| A |
|下にセルを挿入する| B |
|選択されたセルを削除する| Shift + D |
|編集モードに切り替える| 次に、 |

---

### <a name="shortcut-keys-under-edit-mode"></a>編集モードのショートカット キー


次のキーストローク ショートカットを使用すると、編集モードのときに Synapse ノートブックのコードをより簡単に移動して実行できます。

| アクション |Synapse ノートブックのショートカット  |
|--|--|
|カーソルを上に移動する | 上へ |
|カーソルを下に移動する|[下へ]|
|元に戻す|Ctrl + Z|
|やり直し|Ctrl + Y|
|コメント化する/コメントを解除する|Ctrl + /|
|前の単語を削除する|Ctrl + Backspace|
|後の単語を削除する|Ctrl + Delete|
|セルの先頭に移動する|Ctrl + Home|
|セルの末尾に移動する |Ctrl + End|
|1 単語左に移動する|Ctrl + Left|
|1 単語右に移動する|Ctrl + Right|
|すべて選択する|Ctrl + A|
|インデントする| Ctrl + ]|
|インデントを解除する|Ctrl + [|
|コマンド モードに切り替える| Esc |

---

## <a name="next-steps"></a>次のステップ
- [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [クイック スタート:Web ツールを使用して Azure Synapse Analytics で Apache Spark プールを作成する](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics の Apache Spark とは](apache-spark-overview.md)
- [Azure Synapse Analytics で .NET for Apache Spark を使用する](spark-dotnet.md)
- [.NET for Apache Spark のドキュメント](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
