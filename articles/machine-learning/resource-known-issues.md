---
title: 既知の問題とトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning に関する既知の問題、回避策、トラブルシューティングの一覧を示します。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619370"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Azure Machine Learning の既知の問題とトラブルシューティング

この記事は、Azure Machine Learning の使用時に発生する可能性があるエラーや障害を見つけて修正するのに役立ちます。

## <a name="diagnostic-logs"></a>診断ログ

サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 いくつかのログを表示するには: 
1. [Azure Machine Learning Studio](https://ml.azure.com) にアクセスします。 
1. 左側の **[実験]** を選択します。 
1. 実験を選択します。
1. 実行を選択します。
1. 上部の **[出力 + ログ]** を選択します。

> [!NOTE]
> Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録すされます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。


## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="installation-and-import"></a>インストールとインポート

* **エラー メッセージ:"Cannot uninstall 'PyYAML'" ('PyYAML' をアンインストールできません)**

    Azure Machine Learning SDK for Python:PyYAML は `distutils` によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **パッケージをインストールする際の Databricks のエラー**

    追加のパッケージがインストールされていると、Azure Databricks で Azure Machine Learning SDK のインストールが失敗します。 `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、ライブラリ バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連したもので、Azure Machine Learning SDK には関連はありません。 他のライブラリでもこの問題が発生する場合があります。 例:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    別の方法として、Python ライブラリでインストールの問題が発生し続けている場合は、初期化スクリプトを使用することができます。 この方法は、公式にはサポートされていません。 詳細については、「[Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)」を参照してください。

* **Databricks インポート エラー: 'pandas._libs.tslibs' から名前 'Timedelta' をインポートできません**:自動機械学習を使用するときにこのエラーが表示される場合は、notebook で次の 2 行を実行します。
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks インポート エラー:'pandas.core.indexes' という名前のモジュールはありません**:自動化された機械学習を使用しているときにこのエラーが表示される場合は、以下を実行します。

    1. 次のコマンドを実行して、Azure Databricks クラスターに 2 つのパッケージをインストールします。
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. クラスターをデタッチし、次にクラスターをノートブックに再アタッチします。
    
    これらの手順で問題が解決しない場合は、クラスターを再起動してみてください。

* **Databricks FailToSendFeather**:Azure Databricks クラスター上のデータの読み取り時に `FailToSendFeather` エラーが表示された場合は、次の解決策を参照してください。
    
    * `azureml-sdk[automl]` パッケージを最新バージョンにアップグレードします。
    * `azureml-dataprep` バージョン 1.1.8 以降を追加します。
    * `pyarrow` バージョン 0.11 以降を追加します。

## <a name="create-and-manage-workspaces"></a>ワークスペースの作成と管理

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

* **Azure ポータル**:SDK またはポータルで共有リンクからワークスペースを直接表示した場合、拡張機能のサブスクリプション情報を含む通常の **[概要]** ページは表示できません。 また、別のワークスペースに切り替えることもできません。 別のワークスペースを表示する必要がある場合は、[Azure Machine Learning Studio](https://ml.azure.com) に直接移動し、そのワークスペース名を検索してください。

## <a name="set-up-your-environment"></a>環境の設定方法

* **AmlCompute の作成に関する問題**:まれにですが、GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに AmlCompute を作成できないことがあります。 サービスにサポート リクエストを送るか、ポータルまたは SDK を使って新しいワークスペースを作成することで、すぐにブロックを解除することができます。

## <a name="work-with-data"></a>データの処理

### <a name="overloaded-azurefile-storage"></a>オーバーロードされた AzureFile ストレージ

エラー `Unable to upload project files to working directory in AzureFile because the storage is overloaded` が発生する場合は、次の回避策を適用してください。

データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。 2 つの異なるワークスペース間でワークロードを分割することもできます。

### <a name="passing-data-as-input"></a>入力としてのデータの引き渡し

*  **TypeError:FileNotFound:そのようなファイルまたはディレクトリはありません**:このエラーは、指定したファイル パスにファイルがない場合に発生します。 ファイルを参照する方法が、コンピューティング先でデータセットをマウントした場所と一致していることを確認する必要があります。 確定的な状態を確保するには、データセットをコンピューティング先にマウントするときに抽象パスを使用することをお勧めします。 たとえば、次のコードでは、コンピューティング先のファイルシステムのルート `/tmp` にデータセットをマウントしています。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    先頭のスラッシュ "/" を含めない場合は、データセットをマウントする場所を示すために、コンピューティング先の作業ディレクトリをプレフィックスとして付ける必要があります (たとえば、`/mnt/batch/.../tmp/dataset`)。

### <a name="data-labeling-projects"></a>プロジェクトのラベル付けデータ

|問題  |解像度  |
|---------|---------|
|使用できるのは BLOB データストアに作成されたデータセットのみ     |  これは、現在のリリースの既知の制限です。       |
|作成後、プロジェクトで "Initializing (初期化しています)" と長時間にわたり表示される     | ページを手動で最新の情報に更新してください。 初期化の進行速度は、1 秒あたり約 20 データポイントです。 自動更新が実行されない問題が確認されています。         |
|画像をレビューする際に、新しくラベル付けされた画像が表示されない     |   ラベル付けされたすべての画像を読み込むには、 **[First]\(1 番目\)** ボタンを選択します。 **[First]\(1 番目\)** ボタンを選択すると、リストの先頭に戻りますが、ラベル付けされたデータはすべて読み込まれます。      |
|オブジェクト検出のラベル付け中に Esc キーを押すと、左上隅にゼロ サイズのラベルが作成されます。 この状態でラベルを送信することはできません。     |   ラベルの横にある交差マークをクリックして、ラベルを削除してください。  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning デザイナー

既知の問題:

* **計算の準備時間が長い**:初回の接続時またはコンピューティング ターゲットの作成時に、数分またはそれ以上の時間がかかることがあります。 

## <a name="train-models"></a>モデルをトレーニングする

* **ModuleErrors (モジュール名が指定されていない)** :Azure ML で実験を送信する際に ModuleErrors が発生した場合、トレーニング スクリプトではパッケージがインストールされていることを期待しているのに、それが追加されていないことを意味します。 パッケージ名を指定すると、Azure ML により、トレーニングの実行に使用される環境にパッケージがインストールされます。 

    [Estimator](concept-azure-machine-learning-architecture.md#estimators) を使用して実験を送信する場合は、パッケージのインストール元に基づく Estimator 内の `pip_packages` または `conda_packages` パラメータ―を使って、パッケージ名を指定できます。 また、`conda_dependencies_file` を使用してすべての依存関係を含む yml ファイルを指定したり、`pip_requirements_file` パラメーターを使用して txt ファイル内のすべての pip 要件を一覧表示したりすることも可能です。 Estimator で使用される既定のイメージをオーバーライドする独自の Azure ML 環境オブジェクトがある場合は、Estimator コンストラクターの `environment` パラメーターを使用してその環境を指定できます。

    Azure ML では、Tensorflow、PyTorch、Chainer、および SKLearn に対応するフレームワーク固有の Estimator も提供されています。 これらの Estimator を使用すると、ユーザーに代わって、トレーニングに使用される環境にコア フレームワークの依存関係が確実にインストールされます。 前述のように、追加の依存関係を指定することもできます。 
 
    Azure ML によって保守される docker イメージとそのコンテンツは、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)内で確認できます。
    フレームワーク固有の依存関係は、それぞれのフレームワークのドキュメント ([Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)) に示されています。

    > [!Note]
    > 特定のパッケージが Azure ML によって保守されるイメージと環境に追加できるほど十分に一般的だと考えられる場合は、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)に関するページで、GitHub の問題を作成してください。 
 
* **NameError (名前が定義されていない)、AttributeError (オブジェクトに属性がない)** :この例外は、トレーニング スクリプトに起因しているはずです。 Azure portal からログ ファイルを参照して、定義されていない特定の名前または属性エラーに関する詳細情報を取得できます。 SDK から、`run.get_details()` を使用して、エラー メッセージを確認できます。 これにより、実行に対して生成されたすべてのログ ファイルも一覧表示されます。 実行を再送信する前に、トレーニング スクリプトを確認し、エラーを修正してください。 

* **Horovod がシャットダウンされている**:"AbortedError:Horovod がシャットダウンされました" に遭遇した場合のほとんどで、この例外は、プロセスの 1 つにおいて horovod のシャットダウンを引き起こす基となる例外が発生していたことを意味します。 MPI ジョブの各ランクでは、Azure ML 内にある固有の専用ログ ファイルが取得されます。 これらのログは、`70_driver_logs` という名前です。 分散トレーニングの場合、ログを区別しやすいようにログ名の末尾に `_rank` が付与されます。 実際に Horovod シャットダウンの原因となったエラーを見つけるには、すべてのログ ファイルを確認して、driver_log ファイルの末尾にある `Traceback` を探します。 これらのファイルの 1 つから、基になる実際の例外がわかります。 

* **実行または実験の削除**:実験をアーカイブするには、[Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) メソッドを使用するか、[Archive experiment]\(アーカイブ実験\) ボタンを介して Azure Machine Learning Studio クライアントの [実験] タブ ビューを使用します。 この操作により、実験はリスト クエリおよびビューから非表示になりますが、削除はされません。

    個々の実験または実行を完全に削除することは現在サポートされていません。 ワークスペース アセットの削除の詳細については、「[Machine Learning service のワークスペース データをエクスポートまたは削除する](how-to-export-delete-data.md)」を参照してください。

* **メトリック ドキュメントが大きすぎる**:Azure Machine Learning には、トレーニングの実行から一度にログに記録できるメトリック オブジェクトのサイズに関する内部制限があります。 リスト値メトリックのログ記録時に "Metric Document is too large (メトリック ドキュメントが大きすぎます)" エラーが発生した場合は、次の例のように、リストを小さいチャンクに分割してみてください。

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    内部的には、Azure ML は同じメトリック名を持つブロックを連続したリストへと連結します。

## <a name="automated-machine-learning"></a>自動化された機械学習

* **Tensor Flow**:自動化された機械学習では、現時点では Tensor Flow バージョン 1.13 はサポートされていません。 このバージョンをインストールすると、パッケージの依存関係が動作を停止することになります。 Microsoft は、将来のリリースでこの問題を解決するよう取り組んでいます。

* **実験グラフ**:自動化された ML の実験のイテレーションで示される二項分類グラフ (精度と再現率、ROC、ゲイン カーブなど) は、4/12 以降のユーザー インターフェイスでは正しくレンダリングされません。 グラフのプロットは現在、逆の結果を示しており、パフォーマンスが良いモデルほど低い結果で示されています。 解決策を調査中です。

* **Databricks での自動化された機械学習の実行をキャンセルする**:自動化された機械学習機能を Azure Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始するには、Azure Databricks クラスターを再起動してください。

* **Databricks での自動化された機械学習の 10 回を超える繰り返し**:自動化された機械学習の設定で、繰り返し回数が 10 回を超えている場合は、実行を送信するときに `show_output` を `False` に設定します。

* **Azure Machine Learning SDK 用の Databricks ウィジェットと自動化された機械学習**:Azure Machine Learning SDK ウィジェットは、Databricks ノートブックではサポートされていません。この理由は、ノートブックが HTML ウィジェットを解析できないからです。 Azure Databricks のノートブック セルで次の Python コードを使用することにより、portal でウィジェットを表示することができます。

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>モデルをデプロイおよび提供する

次のエラーに対して、これらのアクションを実行します。

|エラー  | 解像度  |
|---------|---------|
|Web サービスのデプロイ時のイメージ構築エラー     |  イメージ構成用の pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加します。       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   デプロイで使用される VM の SKU を、メモリがより多い SKU に変更します。 |
|FPGA エラー     |  FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS クラスター内の Azure Machine Learning コンポーネントの更新

Azure Kubernetes Service クラスターにインストールされている Azure Machine Learning コンポーネントの更新プログラムは、手動で適用する必要があります。 

これらの更新プログラムを適用するには、Azure Machine Learning ワークスペースからクラスターをデタッチしてから、クラスターをワークスペースに再アタッチします。 クラスターで TLS が有効な場合は、クラスターを再アタッチするときに TLS/SSL 証明書と秘密キーを指定する必要があります。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

TLS/SSL 証明書と秘密キーがなくなった場合、または Azure Machine Learning によって生成された証明書を使用している場合は、`kubectl` を使用してクラスターに接続し、シークレット `azuremlfessl` を取得することで、クラスターをデタッチする前にファイルを取得できます。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes では、base-64 でエンコードされた形式でシークレットが格納されます。 シークレットの `cert.pem` コンポーネントと `key.pem` コンポーネントを `attach_config.enable_ssl` に提供する前に、base-64 でデ コードする必要があります。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service の Web サービスのエラー

Azure Kubernetes Service の多くの Web サービスのエラーは、`kubectl` を使用してクラスターに接続することでデバッグできます。 以下を実行して、Azure Kubernetes Service クラスターの `kubeconfig.json` を取得できます

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>認証エラー

リモート ジョブからコンピューティング ターゲットで管理操作を実行すると、次のいずれかのエラーが発生します。

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

たとえば、リモート実行のために送信される ML パイプラインからコンピューティング ターゲットを作成またはアタッチしようとすると、エラーが発生します。
