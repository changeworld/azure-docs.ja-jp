---
title: Azure Machine Learning サービスのしくみ
description: Azure Machine Learning サービスを構成するアーキテクチャ、用語、概念について説明します。 サービスを使用する一般的なワークフローと、Azure Machine Learning サービスによって使用される Azure サービスについても説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 64104fc70c7be1589c9332905f243a2e1e692eee
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237978"
---
# <a name="architecture-and-concepts-how-does-azure-machine-learning-service-work"></a>アーキテクチャと概念: Azure Machine Learning サービスのしくみ 

このドキュメントでは、Azure Machine Learning サービスのアーキテクチャと概念について説明します。 次の図は、サービスの主要なコンポーネントと、サービスを使用するときの一般的なワークフローを示したものです。 

[![Azure Machine Learning Services のアーキテクチャとワークフロー](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

ワークフローの一般的な手順は次のとおりです。

1. __Python__ で機械学習トレーニング スクリプトを開発します。
1. __コンピューティング ターゲット__ を作成して構成します。
1. 構成したコンピューティング ターゲットに __スクリプトを送信__ して、その環境で実行します。 トレーニングの間に、コンピューティング ターゲットは実行レコードを __データストア__ に格納します。 レコードは __実験__ に保存されます。
1. 現在と過去の実行から __実験をクエリ__ してログに記録されたメトリックを取得します。 メトリックが目的の結果を示していない場合は、ステップ 1 に戻ってスクリプトを繰り返します。
1. 満足できる実行が見つかった場合は、永続化されたモデルを __モデル レジストリ__ に登録します。
1. スコアリング スクリプトを開発します。
1. __イメージを作成__ し、それを __イメージ レジストリ__ に登録します。 
1. Azure に __Web サービス__として__イメージをデプロイ__します。


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> このドキュメントでは、Azure Machine Learning で使用される用語と概念を定義しますが、Azure プラットフォームに関する用語と概念は定義しません。 Azure プラットフォームの用語について詳しくは、「[Microsoft Azure 用語集](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)」をご覧ください。

## <a name="workspace"></a>ワークスペース

ワークスペースは、Azure Machine Learning サービスの最上位のリソースです。 Azure Machine Learning Services を使用すると作成されるすべての成果物を操作するための一元的な場所を提供します。

ワークスペースでは、モデルのトレーニングに使用できるコンピューティング ターゲットのリストが保持されています。 また、スクリプトのログ、メトリック、出力、スナップショットなど、トレーニング実行の履歴も保持されています。 この情報は、最適なモデルを生成するトレーニング実行を決定するために使用されます。

モデルはワークスペースに登録されます。 登録されたモデルとスコアリング スクリプトを使用して、イメージが作成されます。 その後、イメージを REST ベースの HTTP エンドポイントとして Azure Container Instances、Azure Kubernetes Service、またはフィールド プログラマブル ゲート アレイ (FPGA) にデプロイすることができます。 また、モジュールとして Azure IoT Edge デバイスにデプロイすることもできます。

複数のワークスペースを作成でき、各ワークスペースを複数のユーザーで共有できます。 ワークスペースを共有する場合は、次のロールをユーザーに割り当てることで、ワークスペースへのアクセスを制御します。

* Owner
* Contributor
* Reader

新しいワークスペースを作成すると、ワークスペースによって使用される複数の Azure リソースが自動的に作成されます。

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) - トレーニング中およびモデルのデプロイ時に使用される Docker コンテナーを登録します。
* [Azure Storage](https://azure.microsoft.com/services/storage/) - ワークスペースの既定のデータストアとして使用されます。
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) - モデルに関する監視情報を格納します。
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - コンピューティング ターゲットで使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

> [!NOTE]
> 新しいバージョンを作成する代わりに、既存の Azure サービスを使用することもできます。 

次の図は、ワークスペースの分類です。

[![ワークスペースの分類](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>モデル

簡単に言うと、モデルとは入力を受け取って出力を生成するひとまとまりのコードです。 機械学習モデルの作成には、アルゴリズムの選択、アルゴリズムへのデータの提供、およびハイパーパラメーターのチューニングが含まれます。 トレーニングはトレーニング済みモデルを生成する反復的なプロセスであり、トレーニング プロセス中にモデルが学習した内容がカプセル化されています。

モデルは、Azure Machine Learning での実行によって生成されます。 Azure Machine Learning の外部でトレーニングされたモデルを使用することもできます。 Azure Machine Learning Services ワークスペースにモデルを登録できます。

Azure Machine Learning Services は、フレームワークに依存しません。 モデルを作成するときは、scikit-learn、xgboost、PyTorch、TensorFlow、Chainer、CNTK などの人気のある機械学習フレームワークを使用できます。

モデルのトレーニングの例については、[クイック スタート: Machine Learning Services ワークスペースの作成](quickstart-get-started.md)に関するページをご覧ください。

### <a name="model-registry"></a>モデル レジストリ

モデル レジストリにより、Azure Machine Learning Services ワークスペース内のすべてのモデルが追跡されます。 

モデルは、名前とバージョンによって識別されます。 既存のモデルと同じ名前でモデルを登録するたびに、レジストリはそれを新しいバージョンとみなします。 バージョンがインクリメントされて、新しいモデルがその名前で登録されます。

モデルを登録するときに追加のメタデータ タグを指定でき、モデルを検索するときにそれらのタグを使用できます。

イメージによって使用されているモデルを削除することはできません。

モデルの登録の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする方法](tutorial-train-models-with-aml.md)」をご覧ください。

## <a name="image"></a>イメージ

イメージによって、モデルを使用するために必要なすべてのコンポーネントと共に、モデルを確実にデプロイする方法が提供されます。 イメージには次の項目が含まれます。

* モデル。
* スコアリング スクリプトまたはアプリケーション。 このスクリプトは、モデルに入力を渡し、モデルから出力を戻すために使用されます。
* モデルまたはスコアリング スクリプト/アプリケーションで必要な依存関係。  たとえば、Python パッケージの依存関係が列記されている Conda 環境ファイルを含めることがあります。

Azure Machine Learning で作成できるイメージには次の 2 種類があります。

* FPGA イメージ: Azure クラウドのフィールド プログラマブル ゲート アレイにデプロイするときに使用されます。
* Docker イメージ: FPGA 以外のコンピューティング ターゲットにデプロイするときに使用されます。 たとえば、Azure Container Instances や Azure Kubernetes Service などです。

イメージの作成の例については、「[Azure Container Instances (ACI) に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」をご覧ください。

### <a name="image-registry"></a>イメージ レジストリ

イメージ レジストリは、モデルから作成されたイメージを追跡します。 イメージを作成するときに、追加のメタデータ タグを指定できます。 メタデータ タグは、イメージ レジストリによって格納され、イメージを検索するときにクエリできます。

## <a name="deployment"></a>Deployment

デプロイとは、クラウドでホストできる Web サービスへの、または統合されたデバイスのデプロイのための IoT モジュールへの、イメージのインスタンス化です。 

### <a name="web-service"></a>Web サービス

デプロイされた Web サービスでは、Azure Container Instances、Azure Kubernetes Service、またはフィールド プログラマブル ゲート アレイ (FPGA) を使用できます。
サービスは、モデル、スクリプト、および関連付けられたファイルがカプセル化されているイメージから作成されます。 イメージには、Web サービスに送信されたスコアリング要求を受け取る負荷分散された HTTP エンドポイントがあります。

Azure には Application Insight のテレメトリやモデルのテレメトリを収集する機能があり、それを有効にすると、Web サービスのデプロイを監視するのに役立ちます。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーの Application Insights インスタンスとストレージ アカウント インスタンスに格納されます。

自動スケールを有効にしてある場合は、デプロイが自動的にスケーリングされます。

Web サービスとしてのモデルのデプロイの例については、「[Azure Container Instances (ACI) に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」をご覧ください。

### <a name="iot-module"></a>IoT モジュール

デプロイされる IoT モジュールは Docker コンテナーであり、モデルとそれに関連付けられているスクリプトまたはアプリケーション、および追加の依存関係が含まれます。 これらのモジュールは、エッジ デバイス上の Azure IoT Edge を使用してデプロイされます。 

監視を有効にしてある場合、Azure は Azure IoT Edge モジュール内のモデルから利用統計情報データを収集します。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーのストレージ アカウント インスタンスに格納されます。

Azure IoT Edge はモジュールが実行されるのを保証し、モジュールをホストしているデバイスを監視します。

## <a name="datastore"></a>データストア

データストアは、Azure Storage アカウントに対するストレージの抽象化です。 データストアでは、バックエンド ストレージとして Azure BLOB コンテナーまたは Azure ファイル共有を使用できます。 各ワークスペースには既定のデータストアがあり、ユーザーは追加のデータストアを登録できます。 

データストアのファイルを格納および取得するには、Python SDK API または Azure Machine Learning CLI を使用します。 

## <a name="run"></a>ラン

実行は、次の情報を含むレコードです。

* 実行に関するメタデータ (タイムスタンプ、期間など)
* スクリプトによって記録されたメトリック
* 実験によって自動収集された、またはユーザーが明示的にアップロードした、出力ファイル。
* 実行前の、スクリプトを含むディレクトリのスナップショット

モデルをトレーニングするためにスクリプトを送信すると、実行が生成されます。 実行は、0 個以上の子実行を持つことができます。 最上位レベルの実行は2 つの子実行を持つ可能性があり、それぞれが独自の子実行を持つことができます。

モデルのトレーニングによって生成された実行を表示する例については、「[クイック スタート: Azure Machine Learning サービスの基本操作](quickstart-get-started.md)」をご覧ください。

## <a name="experiment"></a>実験

実験は、特定のスクリプトからの多数の実行のグループ化です。 実験は、常に 1 つのワークスペースに属します。 実行を送信するときは、実験名を指定します。 実行に関する情報は、その実験に格納されます。 実行を送信するときに、存在しない実験名を指定すると、その名前を持つ新しい実験が自動的に作成されます。

実験の使用の例については、「[クイック スタート: Azure Machine Learning サービスの基本操作](quickstart-get-started.md)」をご覧ください。

## <a name="compute-target"></a>コンピューティング ターゲット

コンピューティング ターゲットは、トレーニング スクリプトを実行するために、または Web サービスのデプロイをホストするために使用されるコンピューティング リソースです。 サポートされているコンピューティング ターゲットを次に示します。 

* ユーザーのローカル コンピューター
* Azure の Linux VM (Data Science Virtual Machine など)
* Azure Batch AI クラスター
* Apache Spark for HDInsight
* Azure コンテナー インスタンス
* Azure Kubernetes Service

コンピューティング ターゲットはワークスペースに接続されています。 ローカル コンピューター以外のコンピューティング ターゲットは、ワークスペースのユーザーによって共有されます。

ほとんどのコンピューティング ターゲットは、Azure portal、Azure Machine Learning SDK、または Azure CLI を使用して、ワークスペースから直接作成できます。 別のプロセス (たとえば、Azure portal や Azure CLI) で作成されたコンピューティング ターゲットがある場合、それをワークスペースに追加 (接続) できます。 一部のコンピューティング ターゲットは、ワークスペースの外部で作成してから、接続する必要があります。

トレーニング用のコンピューティング ターゲットの選択については、「[コンピューティング ターゲットを選択して使用し、モデルをトレーニングする](how-to-set-up-training-targets.md)」をご覧ください。

デプロイ用のコンピューティング ターゲットの選択については、「[Deploy models with the Azure Machine Learning service](how-to-deploy-and-where.md)」(Azure Machine Learning service でモデルをデプロイする) をご覧ください。

## <a name="run-configuration"></a>実行構成

実行構成は、特定のコンピューティング ターゲットでのスクリプトの実行方法を定義する一連の命令です。 既存の Python 環境を使用するかどうかや、仕様から構築された Conda 環境を使用するかどうかなど、広範な動作の定義のセットが含まれています。

実行構成は、トレーニング スクリプトが含まれるディレクトリ内のファイルに保持すること、またはメモリ内オブジェクトとして構築して実行の送信に使用することができます。

実行構成の例については、「[コンピューティング ターゲットを選択して使用し、モデルをトレーニングする](how-to-set-up-training-targets.md)」をご覧ください。

## <a name="training-script"></a>トレーニング スクリプト

モデルをトレーニングするには、トレーニング スクリプトおよび関連ファイルが格納されているディレクトリを指定します。 実験名も指定します。これは、トレーニング中に収集された情報を格納するために使用されます。 トレーニングでは、ディレクトリ全体がトレーニング環境 (コンピューティング ターゲット) にコピーされて、実行構成で指定されているスクリプトが開始されます。 ディレクトリのスナップショットも、ワークスペース内の実験の下に格納されます。

スクリプトを使用したモデルのトレーニングの例については、[Python を使用したワークスペースの作成](quickstart-get-started.md)に関するページをご覧ください。

## <a name="logging"></a>ログの記録

ソリューションを開発するときは、Python スクリプトで Azure Machine Learning Python SDK を使用して、任意のメトリックを記録します。 実行後にメトリックのクエリを行って、実行によってデプロイするモデルが生成されたかどうかを判断します。 

## <a name="snapshot"></a>スナップショット

実行を送信するとき、Azure Machine Learning はスクリプトが含まれているディレクトリを zip ファイルとして圧縮し、コンピューティング ターゲットに送信します。 その後、zip が展開され、そこでスクリプトが実行されます。 Azure Machine Learning では、zip ファイルもスナップショットとして実行レコード内に格納されます。 ワークスペースにアクセスできるすべてのユーザーは、実行レコードを参照し、スナップショットをダウンできます。

## <a name="activity"></a>アクティビティ

アクティビティは、実行時間の長い操作を表します。 次の操作はアクティビティの例です。

* コンピューティング ターゲットの作成または削除
* コンピューティング ターゲット上でのスクリプトの実行

アクティビティでは、ユーザーがこれらの操作の進行状況を簡単に監視できるように、SDK または Web UI 経由で通知を提供できます。

## <a name="next-steps"></a>次の手順

Azure Machine Learning の使用を開始するには、次のリンクを使用してください。

* [Azure Machine Learning サービスの概要](overview-what-is-azure-ml.md)
* [クイック スタート: Python でワークスペースを作成する](quickstart-get-started.md)
* [チュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)
