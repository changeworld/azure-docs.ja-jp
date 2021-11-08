---
title: フェールオーバーとディザスター リカバリー
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のディザスター リカバリーを計画し、事業継続を維持する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 0a18a2bb7efd673ff6e2d6460890e80d52609b02
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564843"
---
# <a name="failover-for-business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリーのためのフェールオーバー

アップタイムを最大化するために、Azure Machine Learning での事業継続の維持とディザスター リカバリーの準備について事前に計画を立てておきます。 

Microsoft は、Azure サービスを常に使用できるようにする作業に取り組んでいます。 そうはいっても、計画されていないサービスの停止が発生する可能性はあります。 リージョン規模のサービス停止に対処するため、ディザスター リカバリー計画を用意しておくことをお勧めします。 この記事では、次の方法について学習します。

* Azure Machine Learning と関連リソースの複数リージョンへのデプロイを計画する。
* ソリューションの高可用性に対応した設計を行う。
* 別のリージョンへのフェールオーバーを開始する。

> [!NOTE]
> Azure Machine Learning 自体では、自動フェールオーバーやディザスター リカバリーは提供されていません。

ワークスペースまたは対応するコンポーネントを誤って削除した場合に備え、この記事では、現在サポートされている回復オプションについても説明します。

## <a name="understand-azure-services-for-azure-machine-learning"></a>Azure Machine Learning のための Azure サービスについて

Azure Machine Learning は複数の Azure サービスに依存しており、これには複数の層があります。 これらのサービスの一部は、ユーザー (顧客) サブスクリプションでプロビジョニングされています。 これらのサービスの高可用性構成は、ユーザーが行う必要があります。 他のサービスは Microsoft サブスクリプションで作成され、Microsoft によって管理されます。 

次のような Azure サービスがあります。

* **Azure Machine Learning インフラストラクチャ**: Azure Machine Learning ワークスペース用の Microsoft によって管理される環境。

* **関連付けられているリソース**: Azure Machine Learning ワークスペースの作成時にサブスクリプションでプロビジョニングされたリソース。 これらのリソースには、Azure Storage、Azure Key Vault、Azure Container Registry、Application Insights が含まれます。 これらのリソースの高可用性の設定の構成は、ユーザーが行う必要があります。
  * 既定のストレージには、モデル、トレーニング ログ データ、データセットなどのデータがあります。
  * Key Vault には、Azure Storage、Container Registry、データ ストアのための資格情報が格納されています。
  * Container Registry には、トレーニングおよび推論環境用の Docker イメージがあります。
  * Application Insights は、Azure Machine Learning を監視するためのものです。

* **コンピューティング リソース**: ワークスペースのデプロイ後に作成するリソース。 たとえば、機械学習モデルをトレーニングするために作成するコンピューティング インスタンスまたはコンピューティング クラスターです。
  * コンピューティング インスタンスとコンピューティング クラスター: Microsoft によって管理されるモデル開発環境。
  * その他のリソース:Azure Kubernetes Service (AKS)、Azure Databricks、Azure Container Instances、Azure HDInsight など、Azure Machine Learning に接続できる Microsoft のコンピューティング リソース。 これらのリソースの高可用性の設定の構成は、ユーザーが行う必要があります。

* **他のデータ ストア**: Azure Machine Learning には、トレーニング データ用に Azure Storage、Azure Data Lake Storage、Azure SQL Database などの別のデータ ストアをマウントできます。  これらのデータ ストアは、サブスクリプション内でプロビジョニングされます。 高可用性の設定の構成は、ユーザーが行う必要があります。

次の表では、Microsoft によって管理される Azure サービスとユーザーによって管理される Azure サービスを示します。 また、既定で高可用性になっているサービスも示します。

| サービス | 管理者 | 既定で高可用性 |
| ----- | ----- | ----- |
| **Azure Machine Learning インフラストラクチャ** | Microsoft | |
| **関連付けられているリソース** |
| Azure Storage | あなたが | |
| Key Vault | あなたが | ✓ |
| Container Registry | あなたが | |
| Application Insights | あなたが | NA |
| **コンピューティング リソース** |
| コンピューティング インスタンス | Microsoft |  |
| コンピューティング クラスター | Microsoft |  |
| その他のコンピューティング リソース (AKS、 <br>Azure Databricks、Container Instances、HDInsight など) | 自分 |  |
| **他のデータ ストア** (Azure Storage、SQL Database、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks ファイル システムなど) | 自分 | |

この記事の残りの部分では、これらの各サービスを高可用性にするために必要な操作について説明します。

## <a name="plan-for-multi-regional-deployment"></a>複数リージョンへのデプロイを計画する

複数リージョンへのデプロイは、2 つの Azure リージョンで Azure Machine Learning および他のリソース (インフラストラクチャ) を作成することに依存しています。 リージョン規模での停止が発生した場合、他のリージョンに切り替えることができます。 リソースをデプロイする場所を計画する際は、次の点を考慮してください。

* __リージョンの可用性__: ユーザーに近いリージョンを使用します。 Azure Machine Learning のリージョンの可用性を確認するには、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。
* __Azure ペア リージョン__: ペアになったリージョンでは、プラットフォームの更新が調整され、必要に応じて復旧作業が優先されます。 詳細については、「[Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)」をご覧ください。
* __サービスの可用性__: ソリューションで使用されるリソースを、ホット/ホット、ホット/ウォーム、またはホット/コールドのいずれにするかを決定します。
    
    * __ホット/ホット__: 両方のリージョンが同時にアクティブになり、1 つのリージョンですぐに使用を開始できます。
    * __ホット/ウォーム__: プライマリ リージョンがアクティブになり、セカンダリ リージョンでは重要なリソース (デプロイされたモデルなど) を開始する準備ができています。 重要でないリソースは、セカンダリ リージョンに手動でデプロイする必要があります。
    * __ホット/コールド__: プライマリ リージョンがアクティブになり、セカンダリ リージョンには必要なデータとともに Azure Machine Learning および他のリソースがデプロイされています。 モデル、モデル デプロイ、パイプラインなどのリソースは、手動でデプロイする必要があります。

> [!TIP]
> ビジネス要件によっては、異なる Azure Machine Learning リソースを異なる方法で扱ってもかまいません。 たとえば、デプロイされたモデル (推論) にはホット/ホットを使用し、実験 (トレーニング) にはホット/コールドを使用することもできます。

Azure Machine Learning は、他のサービスに基づいて構築されます。 一部のサービスは、他のリージョンにレプリケートするように構成できます。 その他のものは、複数のリージョンで手動で作成する必要があります。 次の表に、サービスの一覧、レプリケーションの責任、および構成の概要を示します。

| Azure サービス | Geo レプリケートの責任 | 構成 |
| ----- | ----- | ----- |
| Machine Learning ワークスペース | 自分 | 選択したリージョンにワークスペースを作成します。 |
| Machine Learning コンピューティング | 自分 | 選択したリージョンにコンピューティング リソースを作成します。 動的にスケーリングできるコンピューティング リソースの場合は、両方のリージョンに、ニーズに見合った十分なコンピューティング クォータがあることを確認してください。 |
| Key Vault | Microsoft | 両方のリージョンで、Azure Machine Learning のワークスペースとリソースを備えた同じ Key Vault インスタンスを使用します。 Key Vault は、セカンダリ リージョンに自動的にフェールオーバーします。 詳細については、「[Azure Key Vault の可用性と冗長性](../key-vault/general/disaster-recovery-guidance.md)」を参照してください。|
| Container Registry | Microsoft | Container Registry インスタンスを構成して、Azure Machine Learning 用にペアになっているリージョンにレジストリを geo レプリケートするようにします。 両方のワークスペース インスタンス用に同じインスタンスを使用します。 詳細については、「[Azure Container Registry の geo レプリケーション](../container-registry/container-registry-geo-replication.md)」を参照してください。 |
| ストレージ アカウント | 自分 | Azure Machine Learning では、geo 冗長ストレージ (GRS)、geo ゾーン冗長ストレージ (GZRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)、または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を使用した "__既定のストレージ アカウント__" のフェールオーバーはサポートされていません。 各ワークスペースの既定のストレージに対して、独立したストレージ アカウントを作成します。 </br>他のデータ ストレージに対しては、別々のストレージ アカウントまたはサービスを作成します。 詳細については、「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」を参照してください。 |
| Application Insights | あなたが | 両方のリージョンのワークスペースに対して Application Insights を作成します。 データ保持期間と詳細を調整するには、「[Application Insights でのデータの収集、保持、保存](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)」を参照してください。 |

セカンダリ リージョンでの高速な復旧と再起動を可能にするには、次のような開発方法をお勧めします。

* Azure Resource Manager テンプレートを使用します。 テンプレートは、"コードとしてのインフラストラクチャ" であり、サービスを両方のリージョンで迅速にデプロイできます。
* 2 つのリージョン間のずれを避けるために、継続的インテグレーションとデプロイのパイプラインを更新して両方のリージョンにデプロイします。
* デプロイを自動化する場合は、Azure Kubernetes Service などのワークスペースにアタッチしたコンピューティング リソースの構成を含めます。
* 両方のリージョンのユーザーに対するロールの割り当てを作成します。
* 両方のリージョンに対し、Azure Virtual Networks などのネットワーク リソースとプライベート エンドポイントを作成します。 ユーザーが両方のネットワーク環境にアクセスできるようにします。 たとえば、両方の仮想ネットワークに VPN と DNS の構成を行います。

### <a name="compute-and-data-services"></a>コンピューティングとデータ サービス

ニーズに応じて、Azure Machine Learning によって使用されるコンピューティングまたはデータ サービスを増やすことができます。 たとえば、Azure Kubernetes Services または Azure SQL Database を使用することもできます。 高可用性を実現するためにこれらのサービスを構成する方法については、次の情報を参照してください。

__コンピューティング リソース__

* **Azure Kubernetes Service**:「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス](../aks/operator-best-practices-multi-region.md)」および「[可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](../aks/availability-zones.md)」を参照してください。 AKS クラスターが Azure Machine Learning Studio、SDK、または CLI を使用して作成された場合、複数のリージョンにわたる高可用性はサポートされません。
* **Azure Databricks**:詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](/azure/databricks/scenarios/howto-regional-disaster-recovery)」を参照してください。
* **Container Instances**:フェールオーバーは、オーケストレーターによって行われます。 詳細については、「[Azure Container Instances とコンテナー オーケストレーター](../container-instances/container-instances-orchestrator-relationship.md)」を参照してください。
* **HDInsight**:詳細については、「[Azure HDInsight でサポートされている高可用性サービス](../hdinsight/hdinsight-high-availability-components.md)」を参照してください。

__データ サービス__

* **Azure BLOB コンテナー/Azure Files/Data Lake Storage Gen2**: 「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」を参照してください。
* **[Data Lake Storage Gen1]** :「[Data Lake Storage Gen1 の高可用性とディザスター リカバリーのガイダンス](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)」を参照してください。
* **SQL Database**:「[Azure SQL Database と SQL Managed Instance の高可用性](../azure-sql/database/high-availability-sla.md)」を参照してください。
* **Azure Database for PostgreSQL**:詳細については、「[Azure Database for PostgreSQL - Single Server での高可用性の概念](../postgresql/concepts-high-availability.md)」を参照してください。
* **Azure Database for MySQL**:詳細については、「[Azure Database for MySQL でのビジネス継続性を理解する](../mysql/concepts-business-continuity.md)」を参照してください。
* **Azure Databricks ファイル システム**: 詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](/azure/databricks/scenarios/howto-regional-disaster-recovery)」を参照してください。

> [!TIP]
> 独自のカスタマー マネージド キーを指定して Azure Machine Learning ワークスペースをデプロイした場合、Azure Cosmos DB もサブスクリプション内にプロビジョニングされます。 その場合、その高可用性の設定の構成は、ユーザーが行う必要があります。 詳細については、「[Azure Cosmos DB での高可用性](../cosmos-db/high-availability.md)」を参照してください。

## <a name="design-for-high-availability"></a>高可用性向けの設計

### <a name="deploy-critical-components-to-multiple-regions"></a>重要なコンポーネントを複数のリージョンにデプロイする

目指す事業継続のレベルを決定します。 レベルはソリューションのコンポーネントによって異なる場合があります。 たとえば、運用パイプラインまたはモデル デプロイの場合はホット/ホット構成とし、実験の場合はホット/コールドにすることができます。

### <a name="manage-training-data-on-isolated-storage"></a>分離ストレージ上のトレーニング データを管理する

ワークスペースでログ用に使用する既定のストレージからデータ ストレージを分離することで、次のことが可能になります。

* データストアと同じストレージ インスタンスを、プライマリとセカンダリのワークスペースにアタッチする。
* データ ストレージ アカウント用の geo レプリケーションを使用し、アップタイムを最大化する。

### <a name="manage-machine-learning-artifacts-as-code"></a>機械学習アーティファクトをコードとして管理する

Azure Machine Learning での実行は、実行仕様によって定義されます。 この仕様には、ワークスペース/インスタンス レベルで管理される入力アーティファクト (環境、データセット、コンピューティングを含む) に対する依存関係が含まれます。 複数リージョンの実行の送信とデプロイについては、次の方法をお勧めします。

* コード ベースをローカルで管理し、Git リポジトリによってサポートします。
    * Azure Machine Learning スタジオから重要なノートブックをエクスポートします。
    * スタジオで作成されたパイプラインをコードとしてエクスポートします。

      > [!NOTE]
      > スタジオ デザイナーで作成されたパイプラインは、現在コードとしてエクスポートできません。

* 構成をコードとして管理します。

    * ワークスペースへの参照をハードコーディングすることは避けてください。 代わりに、[構成ファイル](how-to-configure-environment.md#workspace)を使用してワークスペース インスタンスへの参照を構成し、[Workspace.from_config()](/python/api/azureml-core/azureml.core.workspace.workspace#remarks) を使用してワークスペースを初期化します。 プロセスを自動化するには、[Azure CLI 機械学習向け拡張機能](reference-azure-machine-learning-cli.md)のコマンドである [az ml folder attach](/cli/azure/ml(v1)/folder#ext_azure_cli_ml_az_ml_folder_attach) を使用します。
    * [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) や [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)) などの実行の送信ヘルパーを使用します。
    * [Environments.save_to_directory()](/python/api/azureml-core/azureml.core.environment(class)#save-to-directory-path--overwrite-false-) を使用して、環境の定義を保存します。
    * カスタム Docker イメージを使用する場合は、Dockerfile を使用します。
    * [Dataset](/python/api/azureml-core/azureml.core.dataset(class)) クラスを使用して、ソリューションで使用されるデータ [パス](/python/api/azureml-core/azureml.data.datapath)のコレクションを定義します。
    * [Inferenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) クラスを使用して、モデルを推論エンドポイントとしてデプロイします。

## <a name="initiate-a-failover"></a>フェールオーバーの開始

### <a name="continue-work-in-the-failover-workspace"></a>フェールオーバー ワークスペースで作業を続行する

プライマリ ワークスペースが使用できなくなった場合は、セカンダリ ワークスペースに切り替えて、実験と開発を続行することができます。 障害が発生した場合、Azure Machine Learning では実行が自動的にセカンダリ ワークスペースに送信されません。 コード構成を更新して、新しいワークスペース リソースを指定するようにします。 ワークスペース参照のハードコーディングを避けることをお勧めします。 代わりに、[ワークスペース構成ファイル](how-to-configure-environment.md#workspace)を使用して、ワークスペースを変更する際の手動のユーザー手順を最小限に抑えます。 また、新しいワークスペースに対する継続的インテグレーションやデプロイ パイプラインなどのすべての自動化も、更新するようにしてください。

Azure Machine Learning では、ワークスペース インスタンス間でアーティファクトやメタデータを同期したり回復したりすることはできません。 アプリケーションのデプロイ戦略によっては、実行の送信を続行するために、アーティファクトを移動したり、データセット オブジェクトなどの実験入力をフェールオーバー ワークスペース内に再作成したりすることが必要な場合もあります。 geo レプリケーションを有効にして関連リソースを共有するようにプライマリ ワークスペースとセカンダリ ワークスペース リソースを構成した場合、一部のオブジェクトがフェールオーバー ワークスペースで直接使用できることがあります。 たとえば、両方のワークスペースが同じ Docker イメージ、構成されたデータストア、および Azure Key Vault リソースを共有する場合などです。 次の図は、2 つのワークスペースで同じイメージ (1)、データストア (2)、およびKey Vault (3) が共有されている構成を示しています。

![参照リソースの構成](./media/how-to-high-availability-machine-learning/bcdr-resource-configuration.png)

> [!NOTE]
> サービス停止が発生したときに実行中のジョブは、セカンダリ ワークスペースに自動的に移行されません。 また、停止が解決したときに、ジョブがプライマリ ワークスペースで再開されて正常に完了する見込みもありません。 代わりに、セカンダリ ワークスペースまたは (停止が解決した後の) プライマリ ワークスペースのいずれかで、これらのジョブを再送信する必要があります。

### <a name="moving-artifacts-between-workspaces"></a>ワークスペース間でのアーティファクトの移動

復旧方法によっては、作業を続行するにはデータセットやモデル オブジェクトなどのアーティファクトをワークスペース間でコピーすることが必要な場合もあります。 現時点では、ワークスペース間でのアーティファクトの移植性は限定的です。 フェールオーバー インスタンスで再作成できるよう、アーティファクトを可能な限りコードとして管理することをお勧めします。

次のアーティファクトは、[Azure CLI 機械学習向け拡張機能](reference-azure-machine-learning-cli.md)を使用して、ワークスペース間でエクスポートおよびインポートできます。

| アーティファクト | エクスポート | [インポート] |
| ----- | ----- | ----- |
| モデル | [az ml model download --model-id {ID} --target-dir {PATH}](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download) | [az ml model register –name {NAME} --path {PATH}](/cli/azure/ext/azure-cli-ml/ml/model) |
| 環境 | [az ml environment download -n {NAME} -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_download) | [az ml environment register -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_register) |
| Azure ML パイプライン (コード生成) | [az ml pipeline get --path {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_get) | [az ml pipeline create --name {NAME} -y {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_create)

> [!TIP]
> * "__登録済みのデータセット__" は、ダウンロードしたり移動したりすることができません。 これには中間パイプライン データセットなど、Azure ML によって生成されたデータセットが含まれます。 ただし、両方のワークスペースからアクセスできたり、基になるデータ ストレージがレプリケートされたりする共有ファイルの場所を参照するデータセットは、両方のワークスペースに登録できます。 データセットを登録するには、[az ml dataset register](/cli/azure/ml(v1)/dataset#ext_azure_cli_ml_az_ml_dataset_register) を使用します。
> * "__実行の出力__" は、ワークスペースに関連付けられた既定のストレージ アカウントに格納されます。 サービス停止時にスタジオ UI から実行の出力にアクセスできなくなる可能性がありますが、このときストレージ アカウントを使用して、データに直接アクセスできます。 BLOB に格納されているデータの操作の詳細については、「[Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](../storage/blobs/storage-quickstart-blobs-cli.md)」を参照してください。

## <a name="recovery-options"></a>Recovery options

### <a name="workspace-deletion"></a>ワークスペースの削除

ワークスペースを誤って削除した場合、現在、回復することはできません。 ただし、次の手順を実行する場合は、対応するストレージから既存のノートブックを取得できます。
* [Azure portal](https://portal.azure.com) で、Azure Machine Learning ワークスペースにリンクされていたストレージ アカウントに移動します。
* 左側の [データ ストレージ] セクションで、 **[ファイル共有]** をクリックします。
* ノートブックは、ワークスペース ID を含む名前が付けられたファイル共有にあります。 

![ノートブック ファイル共有の参照](./media/how-to-high-availability-machine-learning/notebook-file-share.png)

## <a name="next-steps"></a>次のステップ

関連付けられたリソースを使用して高可用性設定で Azure Machine Learning をデプロイするには、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/)を使用します。
