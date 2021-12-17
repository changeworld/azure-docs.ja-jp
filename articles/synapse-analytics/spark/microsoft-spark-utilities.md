---
title: Microsoft Spark Utilities の概要
description: チュートリアル:Azure Synapse Analytics ノートブックでの MSSparkutils
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.custom: subject-rbac-steps
ms.openlocfilehash: f5dba6b81befd569523111b997c29e54b3e82881
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774616"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Microsoft Spark Utilities の概要

Microsoft Spark Utilities (MSSparkUtils) は、一般的なタスクをより簡単に実行できるようにする組み込みパッケージです。 MSSparkUtils を使用すると、ファイル システムを操作し、環境変数を取得し、ノートブックをまとめてチェーン化し、シークレットを操作できます。 MSSparkUtils は、`PySpark (Python)`、`Scala`、および `.NET Spark (C#)` ノートブックと Synapse パイプラインで使用できます。

## <a name="pre-requisites"></a>前提条件

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 へのアクセスを構成する 

Synapse ノートブックでは、Azure Active Directory (Azure AD) パススルーを使用して、ADLS Gen2 アカウントにアクセスします。 ADLS Gen2 アカウント (またはフォルダー) にアクセスするには、**Storage Blob データ共同作成者** である必要があります。 

Synapse パイプラインでは、ワークスペースの管理サービス ID (MSI) を使用してストレージ アカウントにアクセスします。 パイプライン アクティビティで MSSparkUtils を使用するには、ADLS Gen2 アカウント (またはフォルダー) にアクセスするために、ワークスペース ID が **Storage Blob データ共同作成者** である必要があります。

Azure AD とワークスペースの MSI が ADLS Gen2 アカウントにアクセスできることを確認するには、次の手順に従います。
1. [Azure portal](https://portal.azure.com/) と、アクセスしたいストレージ アカウントを開きます。 アクセスしたい特定のコンテナーに移動できます。
1. 左側のパネルから **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB データ共同作成者 |
    | アクセスの割り当て先 | USER と MANAGEDIDENTITY |
    | メンバー | Azure AD アカウントとワークスペース ID |

    > [!NOTE]
    > マネージド ID の名前は、ワークスペース名でもあります。

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
 
1. **[保存]** を選択します。

Synapse Spark を使用して ADLS Gen2 のデータにアクセスするには、次の URL を使用します。

`abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path>`

### <a name="configure-access-to-azure-blob-storage"></a>Azure Blob Storage へのアクセスを構成する  

Synapse は、[**Shared Access Signature (SAS)**](../../storage/common/storage-sas-overview.md) を利用して Azure Blob Storage にアクセスします。 コードの SAS キーが公開されないようにするには、Synapse ワークスペースで、アクセスしたい Azure Blob Storage アカウントにリンクされたサービスを新しく作成することをお勧めします。

Azure Blob Storage アカウントにリンクされたサービスを新しく追加するには、次の手順に従います。

1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開く
2. 左側のパネルで **[管理]** を選択し、 **[外部接続]** の下にある **[リンクされたサービス]** を選択します。
3. 右側にある **[新しいリンクされたサービス]** パネルで **Azure Blob Storage** を検索します。
4. **[続行]** をクリックします。
5. リンクされたサービス名にアクセスして構成するには、Azure Blob Storage アカウントを選択します。 **[認証方法]** には、 **[アカウント キー]** を使用することをお勧めします。
6. **[テスト接続]** を選択して設定が正しいことを検証します。
7. 最初に **[作成]** を選択し、 **[すべて公開]** をクリックして、変更を保存します。 

Synapse Spark を使用して Azure Blob Storage のデータにアクセスするには、次の URL を使用します。

`wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path>`

コード例はこちらです。

:::zone pivot = "programming-language-python"

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
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Azure Key Vault へのアクセスを構成する

Synapse で資格情報を管理するために、リンクされたサービスとして Azure Key Vault を追加することができます。 Synapse のリンクされたサービスとして Azure Key Vault を追加するには、次の手順に従います。
1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開く
2. 左側のパネルで **[管理]** を選択し、 **[外部接続]** の下にある **[リンクされたサービス]** を選択します。
3. 右側にある **[新しいリンクされたサービス]** パネルで **Azure Key Vault** を検索します。
4. リンクされたサービス名にアクセスして構成するには、Azure Key Vault アカウントを選択します。
5. **[テスト接続]** を選択して設定が正しいことを検証します。
6. 最初に **[作成]** を選択し、 **[すべて公開]** をクリックして、変更を保存します。 

Synapse ノートブックでは、Azure Active Directory (Azure AD) パススルーを使用して、Azure Key Vault にアクセスします。 Synapse パイプラインでは、ワークスペース ID (MSI) を使用して Azure Key Vault にアクセスします。 コードがノートブックと Synapse パイプラインの両方で動作することを確認するには、Azure AD アカウントとワークスペース ID の両方に対してシークレット アクセス許可を付与することをお勧めします。

ワークスペース ID にシークレット アクセス権を付与するには、次の手順に従います。
1. [Azure portal](https://portal.azure.com/) と、アクセスしたい Azure Key Vault を開きます。 
2. 左側のパネルで **[アクセス ポリシー]** を選択します。
3. **[アクセス ポリシーの追加]** を選択します。 
    - 構成テンプレートとして **[キー、シークレット、および証明書の管理]** を選択します。
    - プリンシパルの選択で **Azure AD アカウント** と **ワークスペース ID** (ワークスペース名と同じ) を選択するか、既に割り当てられていることを確認します。 
4. **[選択]** および **[追加]** を選択します。
5. **[保存]** ボタンを選択して変更をコミットします。  

## <a name="file-system-utilities"></a>ファイルシステム ユーティリティ

`mssparkutils.fs` では、Azure Data Lake Storage Gen2 (ADLS Gen2) や Azure Blob Storage など、さまざまなファイルシステムを操作するためのユーティリティが提供されています。 [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) および [Azure Blob Storage](#configure-access-to-azure-blob-storage) へのアクセスを適切に構成するようにしてください。

次のコマンドを実行して、使用可能なメソッドの概要を取得します。

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

結果は次のようになります。
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>ファイルの一覧表示
ディレクトリのコンテンツを表示します。


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>ファイルのプロパティを表示します
ファイル名、ファイル パス、ファイル サイズ、ディレクトリであるかファイルであるかどうかなど、ファイルのプロパティを返します。

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>新しいディレクトリの作成

指定されたディレクトリが存在しない場合は作成し、必要な親ディレクトリを作成します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>ファイルのコピー

ファイルまたはディレクトリをコピーします。 ファイル システム間でのコピーをサポートします。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>ファイル コンテンツのプレビュー

指定したファイルの最初の 'maxBytes' バイトまでを、UTF-8 でエンコードされた文字列として返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>ファイルの移動

ファイルまたはディレクトリを移動します。 ファイル システム間での移動をサポートします。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>ファイルの書き込み

指定した文字列を UTF-8 でエンコードしてファイルに書き込みます。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>ファイルへのコンテンツの追加

指定した文字列を UTF-8 でエンコードしてファイルに追加します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append("file path", "content to append", True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path", "content to append", true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>ファイルまたはディレクトリの削除

ファイルまたはディレクトリを削除します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end



## <a name="notebook-utilities"></a>Notebook のユーティリティ 

:::zone pivot = "programming-language-csharp"

サポートされていません。

::: zone-end

:::zone pivot = "programming-language-python"

MSSparkUtils Notebook ユーティリティを使用して、ノートブックを実行したり、値を持つノートブックを終了したりできます。 次のコマンドを実行して、使用可能なメソッドの概要を取得します。

```python
mssparkutils.notebook.help()
```

結果の取得:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="reference-a-notebook"></a>ノートブックの参照
ノートブックを参照し、その終了値を返します。 関数呼び出しの入れ子は、対話形式またはパイプラインで、ノートブックで実行できます。 参照されているノートブックは、ノートブックがこの機能を呼び出す Spark プールで実行されます。  

```python

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

次に例を示します。

```python
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>ノートブックを終了する
値を指定してノートブックを終了します。 関数呼び出しの入れ子は、対話形式またはパイプラインで、ノートブックで実行できます。 

- `exit()`関数ノートブックを対話形式で呼び出すと、Azure Synapse は例外をスローし、サブシーケンス セルの実行をスキップして、Spark セッションを維持します。

- Synapse パイプラインで `exit()` 関数を呼び出す ノートブックを調整すると、Azure Synapse は終了値を返し、パイプラインの実行を完了して、Spark セッションを停止します。  

- 参照されているノートブックで `exit()` 関数を呼び出すと、Azure Synapse は参照されているノートブックでさらに実行を停止し、その `run()` 関数を呼び出すノートブックで次のセルを続けて実行します。 たとえば、Notebook1 には 3 つのセルがあり、 2 番目のセルで `exit()` 関数を呼び出します。 Notebook2 は 5 つのセルを持ち 、3 番目のセルに `run(notebook1)` を呼び出します。 Notebook2 を実行すると、`exit()` 関数がヒットしたときに 2 番目のセルで Notebook1 が停止します。 Notebook2 は、4 番目のセルと 5 番目のセルを引き続き実行します。 


```python
mssparkutils.notebook.exit("value string")
```

次に例を示します。

**Sample1** ノートブックは次の 2 つのセルを持つ **folder/** 下を検索します。 
- セル 1 は、既定値が 10 に設定された **入力** パラメーターを定義します。
- セル2は、終了値として **入力** を使用してノートブック を終了します。 

![サンプル ノートブックのスクリーンショット](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

既定値を使用して、別のノートブックで **Sample1** を実行できます。

```python

exitVal = mssparkutils.notebook.run("folder/Sample1")
print (exitVal)

```
結果は次のようになります。

```
Sample1 run success with input is 10
```

別のノートブックで **Sample1** を実行して、**入力** 値を 20 に設定できます。

```python
exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print (exitVal)
```

結果は次のようになります。

```
Sample1 run success with input is 20
```
::: zone-end

:::zone pivot = "programming-language-scala"

MSSparkUtils Notebook ユーティリティを使用して、ノートブックを実行したり、値を持つノートブックを終了したりできます。 次のコマンドを実行して、使用可能なメソッドの概要を取得します。

```scala
mssparkutils.notebook.help()
```

結果の取得:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="reference-a-notebook"></a>ノートブックの参照
ノートブックを参照し、その終了値を返します。 関数呼び出しの入れ子は、対話形式またはパイプラインで、ノートブックで実行できます。 参照されているノートブックは、ノートブックがこの機能を呼び出す Spark プールで実行されます。  

```scala

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

次に例を示します。

```scala
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>ノートブックを終了する
値を指定してノートブックを終了します。 関数呼び出しの入れ子は、対話形式またはパイプラインで、ノートブックで実行できます。 

- `exit()`関数ノートブックを対話形式で呼び出すと、Azure Synapse は例外をスローし、サブシーケンス セルの実行をスキップして、Spark セッションを維持します。

- Synapse パイプラインで `exit()` 関数を呼び出す ノートブックを調整すると、Azure Synapse は終了値を返し、パイプラインの実行を完了して、Spark セッションを停止します。  

- 参照されているノートブックで `exit()` 関数を呼び出すと、Azure Synapse は参照されているノートブックでさらに実行を停止し、その `run()` 関数を呼び出すノートブックで次のセルを続けて実行します。 たとえば、Notebook1 には 3 つのセルがあり、 2 番目のセルで `exit()` 関数を呼び出します。 Notebook2 は 5 つのセルを持ち 、3 番目のセルに `run(notebook1)` を呼び出します。 Notebook2 を実行すると、`exit()` 関数がヒットしたときに 2 番目のセルで Notebook1 が停止します。 Notebook2 は、4 番目のセルと 5 番目のセルを引き続き実行します。 


```python
mssparkutils.notebook.exit("value string")
```

次に例を示します。

**Sample1** ノートブックは **mssparkutils/folder/** 下にあり、以下の 2 つのセルを持ちます。 
- セル 1 は、既定値が 10 に設定された **入力** パラメーターを定義します。
- セル2は、終了値として **入力** を使用してノートブック を終了します。 

![サンプル ノートブックのスクリーンショット](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

既定値を使用して、別のノートブックで **Sample1** を実行できます。

```scala

val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1")
print(exitVal)

```
結果は次のようになります。

```
exitVal: String = Sample1 run success with input is 10
Sample1 run success with input is 10
```


別のノートブックで **Sample1** を実行して、**入力** 値を 20 に設定できます。

```scala
val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print(exitVal)
```

結果は次のようになります。

```
exitVal: String = Sample1 run success with input is 20
Sample1 run success with input is 20
```
::: zone-end


## <a name="credentials-utilities"></a>資格情報ユーティリティ

MSSparkUtils 資格情報ユーティリティを使用して、リンクされたサービスのアクセス トークンを取得し、Azure Key Vault でシークレットを管理することができます。 

次のコマンドを実行して、使用可能なメソッドの概要を取得します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

結果を取得します。

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>トークンを取得する

指定された対象ユーザー、名前 (省略可能) の Azure AD トークンを返します。 次の表に、使用可能なすべての対象ユーザーの種類を示します。 

|対象ユーザーの種類|対象ユーザーのキー|
|--|--|
|対象ユーザーの解決の種類|'Audience'|
|ストレージ対象ユーザー リソース|'Storage'|
|専用 SQL プール (データ ウェアハウス)|'DW'|
|Data Lake 対象ユーザー リソース|'AzureManagement'|
|Vault 対象ユーザー リソース|'DataLakeStore'|
|Azure OSSDB 対象ユーザー リソース|'AzureOSSDB'|
|Azure Synapse リソース|'Synapse'|
|Azure Data Factory リソース|'ADF'|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>トークンの検証

トークンの有効期限が切れていない場合は true を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>リンクされたサービスの接続文字列または資格情報の取得

リンクされたサービスの接続文字列または資格情報を返します。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>ワークスペース ID を使用したシークレットの取得

ワークスペース ID を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを返します。 [Azure Key Vault](#configure-access-to-azure-key-vault) へのアクセスが適切に構成されていることを確認してください。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>ユーザー資格情報を使用したシークレットの取得

ユーザー資格情報を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを返します。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>ワークスペース ID を使用したシークレットの作成

ワークスペース ID を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを作成します。 [Azure Key Vault](#configure-access-to-azure-key-vault) へのアクセスが適切に構成されていることを確認してください。

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>ワークスペース ID を使用したシークレットの作成

ワークスペース ID を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを作成します。 [Azure Key Vault](#configure-access-to-azure-key-vault) へのアクセスが適切に構成されていることを確認してください。

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>ユーザー資格情報を使用したシークレットの作成

ユーザー資格情報を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを作成します。 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>ユーザー資格情報を使用したシークレットの作成

ユーザー資格情報を使用して、指定した Azure Key Vault 名、シークレット名、およびリンクされたサービス名の Azure Key Vault シークレットを作成します。 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>環境ユーティリティ 

次のコマンドを実行して、使用可能なメソッドの概要を取得します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

結果を取得します。
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>ユーザー名の取得

現在のユーザー名を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>ユーザー ID の取得

現在のユーザー ID を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>ジョブ ID の取得

ジョブ ID を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>ワークスペース名の取得

ワークスペース名を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>プール名の取得

Spark プール名を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>クラスター ID の取得

現在のクラスター ID を返します。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end


## <a name="runtime-context"></a>ランタイム コンテキスト

mssparkutils ランタイム ユーティリティでは、3 つのランタイム プロパティを公開しています。mssparkutils ランタイム コンテキストを使用して、下に示すプロパティを取得できます。
- **Notebookname** - 現在の notbook の名前では常に、対話モードとパイプライン モードの両方の値を返します。
- **Pipelinejobid** - パイプライン実行 ID では、パイプライン モードで値を返し、対話モードで空の文字列を返します。
- **Activityrunid** - ノートブック アクティビティの実行 ID では、パイプライン モードで値を返し、対話モードで空の文字列を返します。

現在、ランタイム コンテキストでは Python と Scala の両方をサポートしています。

:::zone pivot = "programming-language-python"

```python
mssparkutils.runtime.context
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
%%spark
mssparkutils.runtime.context
```
::: zone-end

## <a name="next-steps"></a>次のステップ

- [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [クイック スタート:Web ツールを使用して Azure Synapse Analytics で Apache Spark プールを作成する](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics の Apache Spark とは](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
