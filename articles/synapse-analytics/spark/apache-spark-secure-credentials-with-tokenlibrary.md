---
title: Apache Spark for Azure Synapse Analytics でリンクされたサービスを使用してアクセス資格情報をセキュリティで保護する
description: この記事では、リンクされたサービスとトークン ライブラリを使用して、Apache Spark for Synapse Analytics を他のサービスと安全に統合する方法についての概念を説明します
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 21b571c859ec8ecc66c1c9a222e0648dc7f28f4f
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422128"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>リンクされたサービスと TokenLibrary を使用して資格情報をセキュリティで保護する
外部ソースからデータにアクセスすることは、よくあるパターンです。 外部データソースで匿名アクセスが許可されていない限り、資格情報、シークレット、または接続文字列を使用して接続をセキュリティで保護することがおそらく必要になります。  Azure Synapse Analytics には、リンクされたサービスまたは Azure Key Vault に接続の詳細を保存することによって統合プロセスを簡略化する、リンクされたサービスが用意されています。 リンクされたサービスを作成すると、Apache Spark からリンクされたサービスを参照して、コード内に接続情報を適用できるようになります。 詳細については、[リンクされたサービス](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。
> [!NOTE]
> ワークスペース内の Azure Data Lake Storage からファイルにアクセスするには、認証に AAD パススルーを使用します。そのため、TokenLibrary を使用する必要はありません。 


## <a name="prerequisite"></a>前提条件
* リンクされたサービス - 外部データ ソースへのリンクされたサービスを作成し、トークン ライブラリからリンクされたサービスを参照する必要があります。 リンクされたサービスの詳細については、[こちら](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)を参照してください。


## <a name="connecting-to-adls-gen2-outside-of-synapse-workspace"></a>Synapse ワークスペース外の ADLS Gen2 への接続

Synapse によって、Azure Data Lake Storage Gen2 のリンクされたサービスが統合されたエクスペリエンスが提供されます。

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="using-the-token-library"></a>トークン ライブラリの使用

他のリンクされたサービスに接続するには、TokenLibrary を直接呼び出すことができます。

### <a name="getconnectionstring"></a>GetConnectionString
 接続文字列を取得するには、<b>getConnectionString</b> 関数を使用し、<b>リンクされたサービスの名前</b>を渡します。

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
次のような接続文字列で "<i>キーと値</i>" のペアから特定の値を解析するには、 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

<b>getConnectionStringAsMap</b> 関数を使用してキーを渡し、値を返します。
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>次のステップ

- [SQL プールへの書き込み](./synapse-spark-sql-pool-import-export.md)

