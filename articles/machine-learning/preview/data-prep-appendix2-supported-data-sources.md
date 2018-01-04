---
title: "Azure Machine Learning データ準備で使用可能なサポートされているデータ ソース | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備で使用可能なサポートされているデータ ソースの完全な一覧を示します。"
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
ms.date: 09/12/2017
ms.openlocfilehash: 32707a8089abef6caebedea168f5891161b1b480
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Azure Machine Learning データ準備のサポートされているデータ ソース 
この記事では、Azure Machine Learning データ準備で現在サポートされているデータ ソースについて説明します。

このリリースでサポートされているデータ ソースは次のとおりです。

## <a name="types"></a>型 

### <a name="sql-server"></a>SQL Server
オンプレミスの SQL Server または Azure SQL データベースから読み取ります。

#### <a name="options"></a>オプション
- サーバー アドレス
- 信頼できるサーバー (サーバー上の証明書が有効ではない場合でも 慎重に使用してください)。
- 認証方法 (Windows、サーバー)
- ユーザー名
- パスワード
- 接続先のデータベース
- SQL クエリ

#### <a name="notes"></a>メモ
- sql-variant 型の列はサポートされません
- Time 列は変換され、データベースの時刻に日付 1970/1/1 を付加して決定されます
- Spark クラスター上で実行すると、1583 年よりも前の日付の場合、すべてのデータ関連の列 (date、datetime、datetime2、datetimeoffset) は不適切な値を評価します
- 10 進数の列の値は 10 進数への変換のために有効桁数を失う可能性があります

### <a name="directory-vs-file"></a>ディレクトリとファイル
1 つのファイルを選択し、データ準備に読み込みます。 ファイルの種類が解析され、次の画面に表示されるファイル接続の既定のパラメーターを決定します。

ディレクトリ、またはディレクトリ内の一連のファイルを選択します (ファイル ピッカーは複数選択です)。 いずれの方法でも、ファイルは 1 つのデータ フローとして読み取られ、相互にアペンドされます (ヘッダーは必要に応じて除去されます)。

サポートされるファイルの種類は次のとおりです。
- 区切り形式ファイル (csv、tsv、txt など)
- 固定幅ファイル
- プレーンテキスト ファイル
- JSON ファイル

### <a name="csv-file"></a>CSV ファイル
コンマ区切り値ファイルをストレージから読み取ります。

#### <a name="options"></a>オプション
- 区切り記号
- Comment (コメント)
- headers
- 小数点の記号
- ファイルのエンコード
- スキップする行

### <a name="tsv-file"></a>TSV ファイル
タブ区切り値ファイルをストレージから読み取ります。

#### <a name="options"></a>オプション
- Comment (コメント)
- headers
- ファイルのエンコード
- スキップする行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
シート名または番号を指定して一度に 1 シートずつ Excel ファイルを読み取ります。

#### <a name="options"></a>オプション
- シート名または番号
- headers
- スキップする行

### <a name="json-file"></a>JSON ファイル
ストレージから JSON ファイルを読み取ります。 ファイルは読み取り時に "フラット化" されます。

#### <a name="options"></a>オプション
- なし

### <a name="parquet"></a>Parquet
1 つのファイルまたはフォルダーのどちらかで Parquet データ セットを読み取ります。

形式としての Parquet は、記憶域でさまざまなフォームを取ることができます。 小さなデータ セットに対しては、1 つの .parquet ファイルが使用される場合があります。 さまざまな Python ライブラリが 1 つの .parquet ファイルの読み取りまたは書き込みをサポートしています。 現時点では、Azure Machine Learning データ準備は、ローカルで対話式に使用している間は、Parquet の読み取りに PyArrow Python ライブラリを使用しています。 これは、Parquet データ セットのほか、1 つの .parquet ファイルをサポートします (大きなデータ セットの一部ではなく、それ自体で書き込まれている限り)。

Parquet データ セットは、複数の .parquet ファイルのコレクションです。そのファイルのそれぞれが大きなデータ セットの小さなパーティションを表します。 データ セットは通常、フォルダーに含まれており、Spark や Hive などのプラットフォームの既定の parquet 出力形式です。

>[!NOTE]
>複数の .parquet ファイルを含むフォルダー内にある Parquet データを読み取る場合、読み取り用のディレクトリと、**[Parquet データ セット]** オプションを選択することが最も安全です。 これにより、PyArrow は個々 のファイルではなくフォルダー全体を読み取ります。 こうすることで、フォルダーのパーティション分割など、さらに複雑な方法でディスクに格納された Parquet の読み取りがサポートされます。

スケールアウトの実行は、Spark の Parquet 読み取り機能に依存し、ローカルで対話式に使用するのと同様、1 つのファイルのほかフォルダーもサポートします。

#### <a name="options"></a>オプション
- Parquet データ セット。 このオプションは、Azure Machine Learning データ準備で、指定したディレクトリを展開して各ファイルを個別に読み取るか (未選択モード)、ディレクトリをデータ セット全体として処理するか (選択モード) を決定します。 選択モードの場合、PyArrow によって、最適なファイル解釈方法が選択されます。


## <a name="locations"></a>場所
### <a name="local"></a>ローカル
ローカルのハード ドライブまたはマップされたネットワーク上の保存場所。

### <a name="sql-server"></a>SQL Server
オンプレミスの SQL Server または Azure SQL データベース。

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ
Azure サブスクリプションを必要とする Azure Blob Storage。

