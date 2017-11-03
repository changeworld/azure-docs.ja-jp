---
title: "Azure Machine Learning データ準備で使用可能なサポートされているデータ ソース | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備で使用可能なサポートされているデータ ソースの完全な一覧を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>このリリースでサポートされるデータ ソース 
次のドキュメントは、Azure Machine Learning データ準備で現在サポートされているデータ ソースの一覧を示しています。

このリリースでサポートされているデータ ソースは、次のとおりです。

## <a name="types"></a>型 
### <a name="directory-versus-file"></a>ディレクトリとファイル
*ファイル/ディレクトリ*: 1 つのファイルを選択し、データの準備に読み込みます。 ファイルの種類が解析され、次の画面に示されるファイル接続の既定のパラメーターを決定します。 ディレクトリまたはディレクトリの中にある一連のファイルを選択します (ファイル ピッカーは複数選択です)。 いずれの方法でも、ファイルは単一のデータフローとして読み込まれ、その際にファイルは相互に連結されます (ヘッダーは必要に応じて除去されます)。

ファイルの種類は次のとおりです。
- 区切り形式ファイル (.csv、.tsv、.txt など) 
- 固定幅ファイル
- プレーンテキスト ファイル
- JSON ファイル

### <a name="csv-file"></a>CSV ファイル
ストレージから CSV ファイルを読み取ります。

#### <a name="options"></a>オプション
- 区切り記号
- コメント
- headers
- 小数点の記号
- ファイルのエンコード
- スキップする行

### <a name="tsv-file"></a>TSV ファイル
ストレージから TSV 値ファイルを読み取ります。

#### <a name="options"></a>オプション
- コメント
- headers
- ファイルのエンコード
- スキップする行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
シート名または番号を指定して、一度に 1 シートずつ Excel ファイルを読み取ります。

#### <a name="options"></a>オプション
- シート名/番号
- headers
- スキップする行

### <a name="json-file"></a>JSON ファイル
ストレージから JSON ファイルを読み取ります。 読み取り時にファイルが "フラット化" されることに注意してください。

#### <a name="options"></a>オプション
なし

### <a name="parquet"></a>Parquet
単一のファイルまたはフォルダーのどちらかで Parquet データセットを読み取ります。

形式としての Parquet は、記憶域でさまざまなフォームを取ることができます。 小さなデータセットに対しては、単一の .parquet ファイルが使用される場合があります。 さまざまな Python ライブラリが単一の .parquet ファイルの読み取りまたは書き込みをサポートしています。 現時点では、Azure Machine Learning ワークベンチは、ローカルで対話式に使用している間は、Parquet の読み取りに PyArrow Python ライブラリを使用しています。 これは単一の .parquet ファイルをサポートします (大きなデータセットの一部ではなく、それ自体として書き込まれている場合に限ります)。 Parquet データセットもサポートします。 

Parquet データセットは、複数の .parquet ファイルのコレクションです。そのファイルのそれぞれが大きなデータセットの小さなパーティションを表します。 データセットは通常、フォルダーに含まれています。 それらは Spark や Hive などの一般的なプラットフォームの既定の Parquet 出力形式です。

>[!NOTE]
>複数の .parquet ファイルを含むフォルダー内にある Parquet データを読み取る場合、読み取り用のディレクトリを選択し、**[Parquet データセット]** オプションをチェックすることが最も安全です。 これにより、PyArrow は個々 のファイルではなくフォルダー全体を読み取ります。 こうすることでさらに複雑な方法 (フォルダーのパーティション分割など) でディスク上に格納された Parquet の読み取りがサポートされます。

スケール アウトの実行は Spark の Parquet 読み取り機能に依存し、フォルダーだけでなく単体のファイルもサポートします。

#### <a name="options"></a>オプション
*[Parquet データセット]*: このオプションは、Azure Machine Learning ワークベンチがティック解除モードかティック モードのどちらを使用するか決定します。 ティック解除モードでは、指定されたディレクトリを展開し、各ファイルを個別に読み取ろうとします。 ティック モードは、ディレクトリをデータ セット全体として扱い、PyArrow にファイルを解析する最善の方法を特定させます。


## <a name="locations"></a>場所
### <a name="local"></a>ローカル
ローカルのハード ドライブまたはマッピングされたネットワーク上の保存場所です。

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ
Azure サブスクリプションが必要です。

