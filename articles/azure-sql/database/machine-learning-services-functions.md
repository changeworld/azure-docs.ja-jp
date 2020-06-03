---
title: 高度な R 関数の記述
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Machine Learning Services (プレビュー) を使用し Azure SQL Database に高度な統計計算のための R 関数を記述する方法を説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84035643"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、R の算術関数およびユーティリティ関数を SQL ストアド プロシージャに埋め込む方法を説明します。 T-SQL で実装するのが複雑な高度な統計関数は、R を使用すると 1 行のコードだけで実行できます。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

- 以降の演習のサンプル コードを実行するには、あらかじめ、[Machine Learning Services (R を使用) が有効になった Azure SQL データベース](machine-learning-services-overview.md)を用意しておく必要があります。

- 最新の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) をインストールしていることを確認してください。 他のデータベース管理またはクエリ ツールを使用して R スクリプトを実行することはできますが、このクイック スタートでは SSMS を使用します。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>乱数を生成するストアド プロシージャを作成する

わかりやすくするために、Machine Learning Services (プレビュー) を使用する Azure SQL Database で既定でインストールされて読み込まれる R の `stats` パッケージを使用します。 このパッケージには一般的な統計タスク用の関数が数百個含まれており、その中に `rnorm` 関数があります。 この関数は、与えられた標準偏差と平均に対して正規分布を使用して、指定された数の乱数を生成します。

たとえば、次の R コードは、指定された 3 の標準偏差で、平均値が 50 の 100 個の数値を返します。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

T-SQL からこの R の行を呼び出すには、次のように、`sp_execute_external_script` を実行し、R スクリプト パラメーターに R 関数を追加します。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

さまざまな乱数のセットを簡単に生成するにはどうすればよいでしょうか。

SQL と組み合わせれば簡単です。 ユーザーから引数を取得し、これらの引数を変数として R スクリプトに渡すストアド プロシージャを定義します。

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 最初の行は、ストアド プロシージャを実行するときに必要になる SQL 入力パラメーターそれぞれを定義します。

- `@params` で始まる行は、R コードで使用されるすべての変数と対応する SQL データ型を定義します。

- すぐ後に続く行では、SQL パラメーター名と対応する R 変数名をマップします。

これでストアド プロシージャに R 関数がラップされたため、次のように関数を簡単に呼び出してさまざまな値を渡すことができます。

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>トラブルシューティングのために R ユーティリティ関数を使用する

既定でインストールされる `utils` パッケージでは、現在の R 環境を調査するためのさまざまなユーティリティ関数が提供されます。 これらの関数は、R コードの実行方法が SQL 内と外部環境で異なる場合に役立ちます。 たとえば、R `memory.limit()` 関数を使用すると、現在の R 環境のメモリを取得できます。

`utils` パッケージはインストールされますが、既定では読み込まれないため、最初に `library()` 関数を使用して読み込む必要があります。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 多くのユーザーは、R プロセスによって使用される時間を取得して、パフォーマンスの問題を分析するために、R のシステム タイミング関数 (`system.time` や `proc.time` など) を使用しようとします。
