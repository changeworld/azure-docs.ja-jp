---
title: 回復性を高める方法
titleSuffix: Azure Machine Learning
description: 高可用性構成を使用して、Azure Machine Learning 関連リソースの障害に対する回復性を高める方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094925"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Azure Machine Learning の回復性を高める

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

高可用性構成を使用して、Azure Machine Learning 関連リソースの回復性を高める方法について説明します。 Azure Machine Learning が依存している Azure サービスは、高可用性を実現するように構成できます。 この記事では、高可用性を実現するために構成できるサービスと、これらのリソースの構成に関する情報へのリンクを提供します。

> [!NOTE]
> Azure Machine Learning 自体は、ディザスター リカバリーオプションを提供していません。

## <a name="understand-azure-services-for-azure-machine-learning"></a>Azure Machine Learning の Azure サービスについて

Azure Machine Learning は複数の Azure サービスに依存しており、これには複数の層があります。 それらの一部は、ユーザー (顧客) サブスクリプションでプロビジョニングされています。 これらのサービスの高可用性構成は、ユーザーが行う必要があります。 一部は Microsoft サブスクリプションで作成され、Microsoft によって管理されます。

* **Azure Machine Learning インフラストラクチャ**:Azure Machine Learning ワークスペース用の Microsoft によって管理される環境。

* **関連付けられているリソース**:Azure Machine Learning ワークスペースの作成時にサブスクリプションでプロビジョニングされたリソース。 これには、Azure Storage、Azure Key Vault、Azure Container Registry (ACR)、および App Insights が含まれます。 これらのリソースの高可用性の設定は、ユーザーが行う必要があります。
  * 既定のストレージには、モデル、トレーニング ログ データ、データセットなどのデータがあります。
  * Key Vault には、ストレージ、ACR、およびデータ ストアの資格情報があります。
  * ACR には、トレーニングおよび推論環境用の Docker イメージがあります。
  * App Insights は、Azure Machine Learning を監視するためのものです。

* **コンピューティング リソース**:ワークスペースのデプロイ後に作成するリソース。 たとえば、機械学習モデルをトレーニングするために作成するコンピューティング インスタンスまたはコンピューティング クラスターです。
  * コンピューティング インスタンスとコンピューティング クラスター:Microsoft によって管理されるモデル開発環境。
  * その他のリソース:これらは、Azure Kubernetes Service (AKS)、Azure Databricks、Azure Container Instances (ACI)、HDInsight などの Azure Machine Learning に接続できるコンピューティング リソースです。 高可用性の設定はユーザーが行う必要があります。

* **追加のデータ ストア**:Azure Machine Learning には、トレーニング データ用に Azure Storage、Azure Data Lake Storage、Azure SQL Database などの追加のデータ ストアをマウントできます。  これらはサブスクリプション内にあるため、高可用性の設定はユーザーが行う必要があります。

次の表に、Microsoft によって管理されるサービスとユーザーによって管理されるサービス、およびそれらの内、既定で高可用性を実現しているサービスを示します。

| サービス | 管理者 | 既定で HA を実現 |
| ----- | ----- | ----- |
| **Azure Machine Learning インフラストラクチャ** | Microsoft | |
| **関連付けられているリソース** |
| Azure Storage | あなたが | |
| Azure Key Vault | あなたが | ✓ |
| Azure Container Registry | あなたが | |
| Application Insights | あなたが | NA |
| **コンピューティング リソース** |
| コンピューティング インスタンス | Microsoft |  |
| コンピューティング クラスター | Microsoft |  |
| Azure Kubernetes Service、 <br>Azure Databricks、Azure Container Instance、Azure HDInsight などのその他のリソース | あなたが |  |
| Azure Storage、Azure SQL Database、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks ファイル システムなどの追加のデータ ストア | あなたが | |

これらの各サービスで高可用性を実現するために必要な操作については、このドキュメントの残りの部分に記載されている情報を参照してください。

## <a name="associated-resources"></a>関連付けられているリソース

> [!IMPORTANT]
> Azure Machine Learning では、Geo 冗長ストレージ (GRS)、geo ゾーン冗長ストレージ (GZRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)、または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を使用した既定のストレージ アカウントのフェールオーバーはサポートされていません。

以下の情報を参照して、各リソースの高可用性の設定を確認してください。

* **Azure Storage**:高可用性の設定を構成するには、「[Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)」を参照してください。
* **Azure Key Vault**:既定で高可用性サービスを提供し、ユーザー操作は必要ありません。  詳細については、「[Azure Key Vault の可用性と冗長性](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)」を参照してください。
* **Azure Container Registry**:Geo レプリケーション用の Premium SKU を選択します。 詳細については、「[Azure Container Registry の geo レプリケーション](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)」を参照してください。
* **Application Insights**:高可用性設定は提供されません。 データ保持期間と詳細を調整するには、「[Application Insights でのデータの収集、保持、保存](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)」を参照してください。

## <a name="compute-resources"></a>コンピューティング リソース

以下のドキュメントを参照して、各リソースの高可用性の設定を確認してください。

* **Azure Kubernetes Service**:「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)」および「[可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](https://docs.microsoft.com/azure/aks/availability-zones)」を参照してください。 AKS クラスターが (スタジオ、SDK、または ML CLI を使用して) Azure Machine Learning によって作成された場合、複数のリージョンにわたる高可用性はサポートされません。
* **Azure Databricks**:詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)」を参照してください。
* **Azure Container Instance**:フェールオーバーは、ACI オーケストレーターによって行われます。 詳細については、「[Azure Container Instances とコンテナー オーケストレーター](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)」を参照してください。
* **Azure HDInsight**:詳細については、「[Azure HDInsight でサポートされている高可用性サービス](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)」を参照してください。

## <a name="additional-data-stores"></a>その他のデータ ストア

以下のドキュメントを参照して、各リソースの高可用性の設定を確認してください。

* **Azure BLOB コンテナー / Azure ファイル共有 / Azure Data Lake Gen2**:既定のストレージと同じです。
* **Azure Data Lake Gen1**:詳細については、「[Azure Data Lake Storage Gen1 内のデータに対するディザスター リカバリーのガイダンス](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)」を参照してください。
* **Azure SQL Database**:「[高可用性と Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)」を参照してください。
* **Azure Database for PostgreSQL**:詳細については、「[Azure Database for PostgreSQL - Single Server での高可用性の概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)」を参照してください。
* **Azure Database for MySQL**:詳細については、「[Azure Database for MySQL でのビジネス継続性を理解する](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)」を参照してください。
* **Databricks ファイル システム**:詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)」を参照してください。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

独自のキー (カスタマー マネージド キー) を指定して Azure Machine Learning ワークスペースをデプロイした場合、Cosmos DB もサブスクリプション内にプロビジョニングされます。 その場合、その高可用性はユーザーが確保する必要があります。 詳細については、「[Azure Cosmos DB での高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)」を参照してください。

## <a name="next-steps"></a>次のステップ

関連付けられたリソースを使用して高可用性設定で Azure Machine Learning をデプロイするには、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)を使用します。