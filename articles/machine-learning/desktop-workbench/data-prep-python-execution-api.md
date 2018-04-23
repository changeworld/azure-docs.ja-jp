---
title: Azure Machine Learning データ準備実行 API の使用方法に関する詳細ガイド | Microsoft Docs
description: このドキュメントでは、以前に設計されたデータ ソースおよびデータ準備パッケージの実行について詳しく説明しています
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Python からのデータ ソースおよびデータ準備パッケージの実行

Python で Azure Machine Learning データ ソースや Azure Machine Learning データ準備を使用するには:

1. プロジェクトの **[Data]\(データ\)** タブに移動します。

2. 適切なソースを右クリックします。

3. **[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)** を選択します。

このアクションは、パッケージを実行して、データフレームを返す短い Python スクリプトを作成します。

## <a name="data-sources"></a>データ ソース

`azureml.dataprep.datasource` モジュールには、データ ソースを実行し、データフレームを返す 1 つの関数が含まれています: `load_datasource(path, secrets=None, spark=None)`。
- `path` はデータ ソース (.dsource ファイル) へのパスです。
- `secrets` はキーをシークレットにマッピングする省略可能なディクショナリです。
- `spark` は Spark データフレームまたは Pandas データフレームを返すかどうかを指定する省略可能なブール値です。 既定では、Azure Machine Learning ワークベンチは、コンテキストに基づいてランタイムに返されるデータフレームの種類を決定します。

## <a name="data-preparations-packages"></a>データ準備パッケージ

`azureml.dataprep.package` モジュールには、データ準備パッケージからデータ フローを実行する 3 つの関数が含まれています。

### <a name="execution-functions"></a>実行関数

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` は指定された実行用データ フローを送信しますが、データフレームは返しません。
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` は指定されたデータ フローを実行し、データフレームとして結果を返します。
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` はメモリ内データ ソースに基づいて指定されたデータ フローを実行し、データフレームとして結果を返します。 `user_config` 引数は、リストの一覧として表されるメモリ内データ ソースに、データ ソース (.dsource ファイル) の絶対パスをマップするディクショナリです。

### <a name="common-arguments"></a>共通の引数

- `package_path` は、データ準備パッケージ (.dprep ファイル) へのパスです。
- `dataflow_idx` は、パッケージのどのデータ フローを実行するかを示す、0 から始まるインデックスです。 指定したデータ フローが他のデータ フローまたはデータ ソースを参照する場合、これらも実行されます。
- `secrets` はキーをシークレットにマッピングする省略可能なディクショナリです。
- `spark` は Spark データフレームまたは Pandas データフレームを返すかどうかを指定する省略可能なブール値です。 既定では、ワークベンチは、コンテキストに基づいてランタイムに返されるデータフレームの種類を決定します。
