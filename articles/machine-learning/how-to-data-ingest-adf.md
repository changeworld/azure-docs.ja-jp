---
title: Azure Data Factory を使用したデータ インジェスト
titleSuffix: Azure Machine Learning
description: Azure Data Factory を使用したデータ インジェスト パイプラインの作成に使用できるオプションと、それぞれの利点について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 73850d8022618dd6544d19564e425288aff09771
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360634"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory を使用したデータ インジェスト

この記事では、Azure Data Factory (ADF) を使用したデータ インジェスト パイプラインの作成に使用できるオプションについて説明します。 このパイプラインは、Azure Machine Learning で使用するデータを取り込むために使用されます。 Azure Data Factory を使用すると、データの抽出、変換、読み込み (ETL) を簡単に実行できます。 変換されてストレージに読み込まれたデータは、機械学習モデルのトレーニングに使用できます。

単純なデータ変換は、[データ フロー](../data-factory/control-flow-execute-data-flow-activity.md)などのネイティブ ADF アクティビティとインストルメントを使用して処理できます。 より複雑なシナリオについては、何らかのカスタム コードを使用してデータを処理できます。 たとえば、Python や R コードです。

インジェスト中にデータを変換するために Azure Data Factory を使用する一般的な手法がいくつかあります。 各手法には、長所と短所があり、特定のユース ケースに適しているかどうかがわかります。

| 手法 | 長所 | 短所 |
| ----- | ----- | ----- |
| ADF と Azure Functions | 短い待機時間、サーバーレス コンピューティング</br>ステートフル関数</br>再利用可能な関数 | 実行時間の短い処理にのみ適している |
| ADF とカスタム コンポーネント | 大規模な並列コンピューティング</br>大量のアルゴリズムに適している | 実行可能ファイルにコードをラップする</br>依存関係と IO の処理が複雑である |
| ADF と Azure Databricks ノートブック | Apache Spark</br>ネイティブ Python 環境 | コストがかかる可能性がある</br>クラスターの初回作成に時間がかかり、待機時間が増加する

## <a name="adf-with-azure-functions"></a>ADF と Azure Functions

![図には、Azure 関数と ML パイプラインの実行を含む Azure Data Factory パイプライン、モデルのトレーニングを含む Azure Machine Learning パイプライン、およびこれらが生データおよび準備されたデータを操作する方法が示されています。](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions を使用すると、アプリケーションのインフラストラクチャについて気にすることなく、小規模なコード (関数) を実行できます。 このオプションでは、Azure 関数にラップされたカスタム Python コードを使用してデータが処理されます。 

この関数は、[ADF の Azure 関数アクティビティ](../data-factory/control-flow-azure-function-activity.md)を使用して呼び出されます。 この方法は、軽量なデータ変換に適したオプションです。 

* 長所:
    * データは、比較的短い待機時間でサーバーレス コンピューティングで処理されます
    * ADF パイプラインは、高度なデータ変換フローを実装できる [Durable Azure Functions](../azure-functions/durable/durable-functions-overview.md) を呼び出すことができます 
    * データ変換の詳細は、Azure Functions によって抽象化され、他の場所から再利用して呼び出すことができます
* 短所:
    * Azure Functions を ADF で使用する前に作成しておく必要があります
    * Azure Functions は、実行時間の短いデータ処理にのみ適しています

## <a name="adf-with-custom-component-activity"></a>ADF とカスタム コンポーネント アクティビティ

![図には、カスタム コンポーネントと ML パイプラインの実行を含む Azure Data Factory パイプライン、モデルのトレーニングを含む Azure Machine Learning パイプライン、およびそれらが生データおよび準備されたデータを操作する方法が示されています。](media/how-to-data-ingest-adf/adf-customcomponent.png)

このオプションでは、実行可能ファイルにラップされたカスタム Python コードを使用してデータが処理されます。 これは、[ADF カスタム コンポーネント アクティビティ](../data-factory/transform-data-using-dotnet-custom-activity.md)を使用して呼び出されます。 この方法は、前の手法に比べて大規模なデータにより適しています。

* 長所:
    * データは [Azure Batch](../batch/batch-technical-overview.md) プールで処理されます。これにより、大規模な並列かつハイパフォーマンス コンピューティングが実現されます
    * 大量のアルゴリズムの実行や大量のデータの処理に使用できます
* 短所:
    * Azure Batch プールを ADF で使用する前に作成しておく必要があります
    * Python コードを実行可能ファイルにラップすることに関連した過度なエンジニアリング。 依存関係と入出力パラメーターの処理が複雑です

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF と Azure Databricks Python ノートブック

![図には、Azure Databricks Python と ML パイプラインの実行を含む Azure Data Factory パイプライン、モデルのトレーニングを含む Azure Machine Learning パイプライン、およびこれらが生データおよび準備されたデータを操作する方法が示されています。](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) は、Microsoft クラウドの Apache Spark ベースの分析プラットフォームです。

この手法では、Azure Databricks クラスターで実行されている [Python ノートブック](../data-factory/transform-data-using-databricks-notebook.md)によってデータ変換が実行されます。 これはおそらく、Azure Databricks サービスの能力を最大限に活用する最も一般的な方法です。 これは、大規模な分散データ処理向けに設計されています。

* 長所:
    * データは、Apache Spark 環境によってバックアップされる、最も強力なデータ処理 Azure サービスで変換されます
    * Python に加え、データ サイエンス フレームワークとライブラリ (TensorFlow、PyTorch、scikit-learn など) のネイティブ サポート
    * Python コードを関数または実行可能モジュールにラップする必要はありません。 コードはそのままで動作します。
* 短所:
    * Azure Databricks インフラストラクチャを ADF で使用する前に作成しておく必要があります
    * Azure Databricks 構成によってはコストが高くなる可能性があります
    * コンピューティング クラスターを "コールド" モードから起動するにはしばらく時間がかかり、ソリューションで長い待機時間が発生します 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure Machine Learning パイプラインでのデータの使用

![図には、Azure Data Factory パイプラインと Azure Machine Learning パイプライン、およびこれらが生データおよび準備されたデータを操作する方法が示されています。 Data Factory パイプラインが準備されたデータのデータベースにデータをフィードし、このデータベースがデータストアにフィードし、このデータストアが Machine Learning ワークスペースにデータセットをフィードします。](media/how-to-data-ingest-adf/aml-dataset.png)

ADF パイプラインから変換されたデータは、データ ストレージ (Azure Blob など) に保存されます。 Azure Machine Learning は、[データストア](./how-to-access-data.md#create-and-register-datastores)と[データセット](./how-to-create-register-datasets.md)を使用してこのデータにアクセスできます。

ADF パイプラインを実行するたびに、データはストレージ内の別の場所に保存されます。 この場所を Azure Machine Learning に渡すために、ADF パイプラインは Azure Machine Learning パイプラインを呼び出します。 ML パイプラインを呼び出すと、データの場所と実行 ID がパラメーターとして送信されます。 その後、ML パイプラインでは、そのデータの場所を使用してデータストアまたはデータセットを作成できます。 

> [!TIP]
> データセットは[バージョン管理をサポートしている](./how-to-version-track-datasets.md)ため、ML パイプラインは ADF パイプラインからの最新のデータを指すデータセットの新しいバージョンを登録できます。

データストアまたはデータセットを使用してデータにアクセスできるようになったら、それを使用して ML モデルをトレーニングできます。 トレーニング プロセスは、ADF から呼び出されるものと同じ ML パイプラインの一部である場合があります。 または、Jupyter ノートブックでの実験など、別のプロセスである場合もあります。

データセットはバージョン管理をサポートし、パイプラインから実行するたびに新しいバージョンが作成されるため、モデルのトレーニングに使用されたデータのバージョンを簡単に把握できます。

## <a name="next-steps"></a>次の手順

* [Azure Data Factory で Databricks ノートブックを実行する](../data-factory/transform-data-using-databricks-notebook.md)
* [Azure ストレージ サービスのデータにアクセスする](./how-to-access-data.md#create-and-register-datastores)
* [Azure Machine Learning でデータセットを使用してモデルをトレーニングする](./how-to-train-with-datasets.md)
* [データ インジェスト パイプラインの DevOps](./how-to-cicd-data-ingestion.md)