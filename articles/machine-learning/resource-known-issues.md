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
ms.date: 11/04/2019
ms.openlocfilehash: 771ae508aaa46167413c2e701d8193790198cb68
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565912"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Azure Machine Learning の既知の問題とトラブルシューティング

この記事は、Azure Machine Learning の使用時に発生したエラーや障害を見つけて修正するのに役立ちます。

## <a name="sdk-installation-issues"></a>SDK のインストールに関する問題

**エラー メッセージ:"Cannot uninstall 'PyYAML'" ('PyYAML' をアンインストールできません)**

Azure Machine Learning SDK for Python:PyYAML は distutils によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**エラー メッセージ: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda の Python 3.7.4 ディストリビューションには、azureml-sdk のインストールが壊れるバグがあります。 この問題については、こちらの [GitHub の問題](https://github.com/ContinuumIO/anaconda-issues/issues/11195)で説明されています。これは、次のコマンドを使用して新しい Conda 環境を作成することによって回避できます。
```bash
conda create -n <env-name> python=3.7.3
```
これにより、Python 3.7.3 を使用する Conda 環境が作成され、この環境には 3.7.4 でのインストールの問題はありません。

## <a name="training-and-experimentation-issues"></a>トレーニングと実験の問題

### <a name="metric-document-is-too-large"></a>Metric Document is too large (メトリック ドキュメントが大きすぎます)
Azure Machine Learning には、トレーニングの実行から一度にログに記録できるメトリック オブジェクトのサイズに関する内部制限があります。 リスト値メトリックのログ記録時に "Metric Document is too large (メトリック ドキュメントが大きすぎます)" エラーが発生した場合は、次の例のように、リストを小さいチャンクに分割してみてください。

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

内部的には、Azure ML は同じメトリック名を持つブロックを連続したリストへと連結します。

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (モジュール名が指定されていない)
Azure ML で実験を送信する際に ModuleErrors が発生した場合、トレーニング スクリプトではパッケージがインストールされていることを期待しているのに、それが追加されていないことを意味します。 パッケージ名を指定すると、Azure ML により、トレーニングの実行に使用される環境にパッケージがインストールされます。 

[Estimator](concept-azure-machine-learning-architecture.md#estimators) を使用して実験を送信する場合は、パッケージのインストール元に基づく Estimator 内の `pip_packages` または `conda_packages` パラメータ―を使って、パッケージ名を指定できます。 また、`conda_dependencies_file` を使用してすべての依存関係を含む yml ファイルを指定したり、`pip_requirements_file` パラメーターを使用して txt ファイル内のすべての pip 要件を一覧表示したりすることも可能です。 Estimator で使用される既定のイメージをオーバーライドする独自の Azure ML 環境オブジェクトがある場合は、Estimator コンストラクターの `environment` パラメーターを使用してその環境を指定できます。

Azure ML では、Tensorflow、PyTorch、Chainer、および SKLearn に対応するフレームワーク固有の Estimator も提供されています。 これらの Estimator を使用すると、ユーザーに代わって、トレーニングに使用される環境にコア フレームワークの依存関係が確実にインストールされます。 前述のように、追加の依存関係を指定することもできます。 
 
Azure ML によって保守される docker イメージとそのコンテンツは、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)内で確認できます。
フレームワーク固有の依存関係は、それぞれのフレームワークのドキュメント ([Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)) に示されています。

> [!Note]
> 特定のパッケージが Azure ML によって保守されるイメージと環境に追加できるほど十分に一般的だと考えられる場合は、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)に関するページで、GitHub の問題を作成してください。 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (名前が定義されていない)、AttributeError (オブジェクトに属性がない)
この例外は、トレーニング スクリプトに起因しているはずです。 Azure portal からログ ファイルを参照して、定義されていない特定の名前または属性エラーに関する詳細情報を取得できます。 SDK から、`run.get_details()` を使用して、エラー メッセージを確認できます。 これにより、実行に対して生成されたすべてのログ ファイルも一覧表示されます。 実行を再送信する前に、トレーニング スクリプトを確認し、エラーを修正してください。 

### <a name="horovod-has-been-shut-down"></a>Horovod がシャットダウンされました
"AbortedError:Horovod がシャットダウンされました" に遭遇した場合のほとんどで、この例外は、プロセスの 1 つにおいて horovod のシャットダウンを引き起こす基となる例外が発生していたことを意味します。 MPI ジョブの各ランクでは、Azure ML 内にある固有の専用ログ ファイルが取得されます。 これらのログは、`70_driver_logs` という名前です。 分散トレーニングの場合、ログを区別しやすいようにログ名の末尾に `_rank` が付与されます。 実際に Horovod シャットダウンの原因となったエラーを見つけるには、すべてのログ ファイルを確認して、driver_log ファイルの末尾にある `Traceback` を探します。 これらのファイルの 1 つから、基になる実際の例外がわかります。 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>分散トレーニングのための AmlCompute における NCv3 マシン上の SR-IOV の可用性
Azure Compute では、Azure ML のマネージド コンピューティング オファリング (AmlCompute) で利用できる、NCv3 マシンの [SR-IOV アップグレード](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) が提供されています。 この更新により、MPI スタック全体のサポートおよび Infiniband RDMA ネットワークの使用が可能になり、マルチ ノード分散トレーニングのパフォーマンスを向上させることができます (特にディープ ラーニングの場合)。

[更新スケジュール](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) を表示して、お客様のリージョンに対してサポートがいつ提供されるかを確認します。

### <a name="run-or-experiment-deletion"></a>実行または実験の削除
実験をアーカイブするには、[Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) メソッドを使用するか、[Archive experiment]\(アーカイブ実験\) ボタンを介して Azure Machine Learning Studio クライアントの [実験] タブ ビューを使用します。 この操作により、実験はリスト クエリおよびビューから非表示になりますが、削除はされません。

個々の実験または実行を完全に削除することは現在サポートされていません。 ワークスペース アセットの削除の詳細については、「[Machine Learning service のワークスペース データをエクスポートまたは削除する](how-to-export-delete-data.md)」を参照してください。

## <a name="azure-machine-learning-compute-issues"></a>Azure Machine Learning コンピューティングの問題
Azure Machine Learning コンピューティング (AmlCompute) の使用に関する既知の問題。

### <a name="trouble-creating-amlcompute"></a>AmlCompute の作成に関する問題

まれにですが、GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに AmlCompute を作成できないことがあります。 サービスにサポート リクエストを送るか、ポータルまたは SDK を使って新しいワークスペースを作成することで、すぐにブロックを解除することができます。

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>停止:AmlCompute の NCv3 マシンに対する SR-IOV のアップグレード

Azure コンピューティングでは、2019 年 11 月の上旬より、MPI のすべての実装とバージョン、および InfiniBand が搭載された仮想マシンでの RDMA 動詞がサポートされるよう、NCv3 SKU が更新されます。 これには、ダウンタイムが短時間必要になります。[SR-IOV のアップグレード](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku)に関する記事を参照してください。

Azure Machine Learning のマネージド コンピューティング オファリング (AmlCompute) のお客様は、現時点で変更を行う必要はありません。 [更新スケジュールに基づいて](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku)、ご自分のトレーニングに小休止を入れる計画をする必要があります。 このサービスでは、お使いのクラスター ノード上の VM イメージの更新と、お使いのクラスターの自動スケールアップも行います。 アップグレードが完了すると、InfiniBand の帯域幅が増加したり、待機時間が短くなったり、分散アプリケーションのパフォーマンスが向上したりするほか、展開されている他のすべての MPI ディストリビューション (Pytorch での OpenMPI など) を使用できるようになります。

## <a name="azure-machine-learning-designer-issues"></a>Azure Machine Learning デザイナーの問題

デザイナーの既知の問題。

### <a name="long-compute-preparation-time"></a>計算の準備時間が長い

新しい計算の作成や計算の終了に、数分またはそれ以上の時間がかかる場合があります。 現在、担当チームが最適化に取り組んでいます。


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>データセットのみを含む実験を実行できない 

データセットのみを含む実験を実行して、データセットを視覚化したい場合があります。 しかし、現在、データセットのみを含む実験は実行できません。 現在、この問題の解決に積極的に取り組んでいます。
 
修正が行われる前に、データセットを任意のデータ変換モジュール (データセット内の列の選択、メタデータの編集、データの分割など) に接続し、実験を実行できます。 その後、データセットを視覚化できます。 

次の図は、その方法を示しています: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>イメージ ビルド エラー

Web サービスのデプロイ時のイメージ ビルド エラー。 回避策は、イメージ構成のために pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加することです。

## <a name="deployment-failure"></a>デプロイの失敗

`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>` を監視する場合、デプロイで使用されている VM の SKU を、メモリがより多い SKU に変更してください。

## <a name="fpgas"></a>FPGA

FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。

## <a name="automated-machine-learning"></a>自動化された機械学習

Tensor Flow の自動化された機械学習は現在、Tensor Flow バージョン 1.13 でサポートされていません。 このバージョンをインストールすると、パッケージの依存関係が動作を停止することになります。 Microsoft は、将来のリリースでこの問題を解決するよう取り組んでいます。 

### <a name="experiment-charts"></a>実験グラフ

自動化された ML の実験のイテレーションで示される二項分類グラフ (精度と再現率、ROC、ゲイン カーブなど) は、4/12 以降のユーザー インターフェイスでは正しくレンダリングされません。 グラフのプロットは現在、逆の結果を示しており、パフォーマンスが良いモデルほど低い結果で示されています。 解決策を調査中です。

## <a name="datasets-and-data-preparation"></a>データセットとデータの準備

これらは、Azure Machine Learning のデータセットの既知の問題です。

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError:FileNotFound:を開けませんでした 原因: ファイルまたはディレクトリが存在しません

このエラーは、指定したファイル パスにファイルがない場合に発生します。 ファイルを参照する方法が、コンピューティング先でデータセットをマウントした場所と一致していることを確認する必要があります。 確定的な状態を確保するには、データセットをコンピューティング先にマウントするときに抽象パスを使用することをお勧めします。 たとえば、次のコードでは、コンピューティング先のファイルシステムのルート `/tmp` にデータセットをマウントしています。 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

先頭のスラッシュ "/" を含めない場合は、データセットをマウントする場所を示すために、コンピューティング先の作業ディレクトリをプレフィックスとして付ける必要があります (たとえば、`/mnt/batch/.../tmp/dataset`)。 

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>HTTP または ADLS Gen 2 から Parquet ファイルを読み取ることができない

AzureML DataPrep SDK バージョン 1.1.25 には、HTTP または ADLS Gen 2 から Parquet ファイルを読み取ってデータセットを作成するときにエラーが発生する既知の問題があります。 `Cannot seek once reading started.` のエラーが発生します。 この問題を解決するには、`azureml-dataprep` を 1.1.26 よりも上のバージョンにアップグレードするか、1.1.24 よりも下のバージョンにダウングレードしてください。

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() got an unexpected keyword argument 'invocation_id' (TypeError: mount() で予期しないキーワード引数 'invocation_id' が発生しました)

このエラーは、`azureml-core` と `azureml-dataprep` のバージョン間に互換性がない場合に発生します。 このエラーが表示される場合は、`azureml-dataprep` パッケージを新しいバージョン (1.1.29 以上) にアップグレードしてください。

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks と Azure Machine Learning の問題。

### <a name="failure-when-installing-packages"></a>パッケージをインストールする際の失敗

追加のパッケージがインストールされていると、Azure Databricks で Azure Machine Learning SDK のインストールが失敗します。 `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、ライブラリ バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連したもので、Azure Machine Learning SDK には関連はありません。 他のライブラリでもこの問題が発生する場合があります。 例:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

別の方法として、Python ライブラリでインストールの問題が発生し続けている場合は、初期化スクリプトを使用することができます。 この方法は、公式にはサポートされていません。 詳細については、「[Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)」を参照してください。

### <a name="cancel-an-automated-machine-learning-run"></a>自動化された機械学習の実行をキャンセルする

自動化された機械学習機能を Azure Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始するには、Azure Databricks クラスターを再起動してください。

### <a name="10-iterations-for-automated-machine-learning"></a>自動化された機械学習での 10 回を超える繰り返し

自動化された機械学習の設定で、繰り返し回数が 10 回を超えている場合は、実行を送信するときに `show_output` を `False` に設定します。

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Azure Machine Learning SDK のウィジェットと自動化された機械学習

Azure Machine Learning SDK ウィジェットは、Databricks ノートブックではサポートされていません。この理由は、ノートブックが HTML ウィジェットを解析できないからです。 Azure Databricks のノートブック セルで次の Python コードを使用することにより、portal でウィジェットを表示することができます。

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>インポート エラー:'pandas.core.indexes' という名前のモジュールはありません

自動化された機械学習を使用しているときにこのエラーが表示される場合は、以下を実行します。

1. 次のコマンドを実行して、Azure Databricks クラスターに 2 つのパッケージをインストールします。 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. クラスターをデタッチし、次にクラスターをノートブックに再アタッチします。 

これらの手順で問題が解決しない場合は、クラスターを再起動してみてください。

### <a name="failtosendfeather"></a>FailToSendFeather

Azure Databricks クラスター上のデータの読み取り時に `FailToSendFeather` エラーが表示された場合は、次の解決策を参照してください。

* `azureml-sdk[automl]` パッケージを最新バージョンにアップグレードします。
* `azureml-dataprep` バージョン 1.1.8 以降を追加します。
* `pyarrow` バージョン 0.11 以降を追加します。

## <a name="azure-portal"></a>Azure portal

SDK またはポータルで共有リンクからワークスペースを直接表示した場合、拡張機能のサブスクリプション情報を含む通常の概要ページを表示できません。 また、別のワークスペースに切り替えることもできません。 別のワークスペースを表示する必要がある場合の回避策としては、[Azure Machine Learning Studio](https://ml.azure.com) に直接移動し、ワークスペース名を検索してください。

## <a name="diagnostic-logs"></a>診断ログ

サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 いくつかのログを確認するには、[Azure Machine Learning Studio](https://ml.azure.com) にアクセスし、自分のワークスペースに移動して、 **[ワークスペース]、[実験]、[実行]、[ログ]** の順に選択します。  

> [!NOTE]
> Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録すされます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

## <a name="activity-logs"></a>アクティビティ ログ

Azure Machine Learning ワークスペース内の一部の操作では、情報は __アクティビティ ログ__ に記録されません。 たとえば、トレーニングの開始やモデルの登録などです。

これらの操作の一部はワークスペースの __[アクティビティ]__ 領域に表示されますが、アクティビティを開始したユーザーは示されません。

## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="authentication-errors"></a>認証エラー

リモート ジョブからコンピューティング ターゲットで管理操作を実行すると、次のいずれかのエラーが発生します。

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

たとえば、リモート実行のために送信される ML パイプラインからコンピューティング ターゲットを作成またはアタッチしようとすると、エラーが発生します。

## <a name="overloaded-azurefile-storage"></a>オーバーロードされた AzureFile ストレージ

エラー `Unable to upload project files to working directory in AzureFile because the storage is overloaded` が発生する場合は、次の回避策を適用してください。

データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。 2 つの異なるワークスペース間でワークロードを分割することもできます。

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service の Web サービスのエラー 

Azure Kubernetes Service の多くの Web サービスのエラーは、`kubectl` を使用してクラスターに接続することでデバッグできます。 以下を実行して、Azure Kubernetes Service クラスターの `kubeconfig.json` を取得できます

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS クラスター内の Azure Machine Learning コンポーネントの更新

Azure Kubernetes Service クラスターにインストールされている Azure Machine Learning コンポーネントの更新プログラムは、手動で適用する必要があります。 

これらの更新プログラムを適用するには、Azure Machine Learning ワークスペースからクラスターをデタッチしてから、クラスターをワークスペースに再アタッチします。 クラスターで SSL が有効な場合は、クラスターを再アタッチするときに SSL 証明書と秘密キーを指定する必要があります。 

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

SSL 証明書と秘密キーがなくなった場合、または Azure Machine Learning によって生成された証明書を使用している場合は、`kubectl` を使用してクラスターに接続し、シークレット `azuremlfessl` を取得することで、クラスターをデタッチする前にファイルを取得できます。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes では、base-64 でエンコードされた形式でシークレットが格納されます。 シークレットの `cert.pem` コンポーネントと `key.pem` コンポーネントを `attach_config.enable_ssl` に提供する前に、base-64 でデ コードする必要があります。 

## <a name="labeling-projects-issues"></a>ラベル付けプロジェクトの問題

ラベル付けプロジェクトに関する既知の問題。

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>使用できるのは BLOB データストアに作成されたデータセットのみ

これは、現在のリリースの既知の制限です。 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>作成後、プロジェクトで "Initializing (初期化しています)" と長時間にわたり表示される

ページを手動で最新の情報に更新してください。 初期化の進行速度は、1 秒あたり約 20 データポイントです。 自動更新が実行されない問題が確認されています。 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>画像をレビューする際に、新しくラベル付けされた画像が表示されない

ラベル付けされたすべての画像を読み込むには、 **[First]\(1 番目\)** ボタンを選択します。 **[First]\(1 番目\)** ボタンを選択すると、リストの先頭に戻りますが、ラベル付けされたデータはすべて読み込まれます。

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>オブジェクト検出のラベル付け中に Esc キーを押すと、左上隅にゼロ サイズのラベルが作成されます。 この状態でラベルを送信することはできません。

ラベルの横にある交差マークをクリックして、ラベルを削除してください。

## <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。
