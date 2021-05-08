---
title: Azure 上のストレージ サービスへの ID ベースのデータ アクセス
titleSuffix: Machine Learning
description: ID ベースのデータ アクセスを使用して、Azure Machine Learning データストアと Machine Learning Python SDK によって Azure のストレージ サービスに接続する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210654"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>ID ベースのデータ アクセスを使用してストレージに接続する (プレビュー)

>[!IMPORTANT]
> この記事に記載されている機能はプレビュー段階です。 これらは、いつでも変更される可能性がある[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能と考える必要があります。

この記事では、ID ベースのデータ アクセスと Azure Machine Learning データストアを使用して、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro) を介して Azure 上のストレージ サービスに接続する方法について説明します。  

通常、データストアでは、資格情報ベースのデータ アクセスを使用して、ストレージ サービスにアクセスするためのアクセス許可があることが確認されます。 サブスクリプション ID やトークン認可などの接続情報は、ワークスペースに関連付けられた[キー コンテナー](https://azure.microsoft.com/services/key-vault/)に格納されます。 ID ベースのデータ アクセスを使用するデータストアを作成する場合は、Azure アカウント ([Azure Active Directory トークン](../active-directory/fundamentals/active-directory-whatis.md)) を使用して、ストレージ サービスにアクセスするためのアクセス許可があることが確認されます。 このシナリオでは、認証資格情報は保存されません。 ストレージ アカウント情報のみがデータストアに格納されます。 

アクセス キーやサービス プリンシパルなど、資格情報ベースの認証を使用するデータストアを作成するには、「[Azure のストレージ サービスに接続する](how-to-access-data.md)」を参照してください。

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure Machine Learning での ID ベースの データ アクセス

Azure Machine Learning で ID ベースのデータ アクセスを適用できるシナリオは 2 つあります。 これらのシナリオは、機密データを扱っていて、より詳細なデータ アクセス管理が必要な場合の ID ベースのアクセスに適しています。

- ストレージ サービスへのアクセス
- プライベート データを使用した機械学習モデルのトレーニング

### <a name="accessing-storage-services"></a>ストレージ サービスへのアクセス

Azure Machine Learning データストアまたは [Azure Machine Learning データセット](how-to-create-register-datasets.md)を使用した ID ベースのデータ アクセスを使用してストレージ サービスに接続できます。 

通常、認証資格情報は、ストレージ サービスにアクセスするためのアクセス許可があることを確認するために使用されるデータストアに保持されます。 これらの資格情報がデータストアを介して登録されると、ワークスペースの閲覧者ロールを持つすべてのユーザーがそれらを取得できます。 このようなアクセスのスケールは、組織によってはセキュリティ上の問題になる場合があります。 [ワークスペースの閲覧者ロールの詳細についてご確認ください。](how-to-assign-roles.md#default-roles) 

ID ベースのデータ アクセスを使用すると、データストアに資格情報を保持する代わりに、Azure Machine Learning によって、データ アクセス認証用の Azure Active Directory トークンの入力が求められます。 このアプローチにより、ストレージ レベルでのデータ アクセス管理が可能になり、資格情報が機密として保持されます。 

同じ動作が、次の場合にも当てはまります。

* [ストレージ URL から直接データセットを作成する場合](#use-data-in-storage)。 
* ローカル コンピューターまたは[コンピューティング インスタンス](concept-compute-instance.md)で Jupyter Notebook を使用して対話形式でデータを操作する場合。

> [!NOTE]
> 資格情報ベースの認証を使用して格納される資格情報には、サブスクリプション ID、Shared Access Signature (SAS) トークン、ストレージ アクセス キー、サービス プリンシパル情報 (クライアント ID やテナント ID など) が含まれます。

### <a name="model-training-on-private-data"></a>プライベート データでのモデル トレーニング

特定の機械学習のシナリオには、プライベート データを使用したトレーニング モデルが含まれます。 このような場合、データ サイエンティストは、機密入力データに公開せずにトレーニング ワークフローを実行する必要があります。 このシナリオでは、トレーニング コンピューティングのマネージド ID がデータ アクセス認証に使用されます。 このアプローチを使用すると、ストレージ管理者は、トレーニング コンピューティングでトレーニング ジョブを実行するために使用されるマネージド ID に「ストレージ BLOB データ閲覧者」アクセス権を付与できます。 個々のデータ サイエンティストにアクセス権を付与する必要はありません。 詳細については、[コンピューティング クラスターでのマネージド ID の設定](how-to-create-attach-compute-cluster.md#managed-identity)に関する説明を参照してください。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- サポートされている種類のストレージを持つ Azure ストレージ アカウント。 プレビューでは、これらのストレージの種類がサポートされています。 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install)。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、[Python SDK を介して既存のもの](how-to-manage-workspace.md#connect-to-a-workspace)を使用します。 

## <a name="storage-access-permissions"></a>ストレージ アクセスのためのアクセス許可

Azure 上のストレージ サービスに安全に接続できるようにするには、Azure Machine Learning では、対応するデータ ストレージにアクセスするためのアクセス許可が必要です。

ID ベースのデータ アクセスでは、次のストレージ サービスへの接続のみがサポートされます。

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL データベース

これらのストレージ サービスにアクセスするには、少なくとも[ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)のアクセス権が必要です。 [Azure portal を使用してアクセス レベルを変更](../storage/common/storage-auth-aad-rbac-portal.md)できるのは、ストレージ アカウントの所有者だけです。

リモート コンピューティング先でモデルをトレーニングする場合は、コンピューティング ID にストレージ サービスから「ストレージ BLOB データ閲覧者」以上のロールが付与されている必要があります。 [コンピューティング クラスターでマネージド ID を設定](how-to-create-attach-compute-cluster.md#managed-identity)する方法を参照してください。

## <a name="work-with-virtual-networks"></a>仮想ネットワークの使用

既定では、Azure Machine Learning は、ファイアウォールの内側または仮想ネットワーク内にあるストレージ アカウントとは通信できません。

特定の仮想ネットワークからのアクセスのみを許可するように、ストレージ アカウントを構成できます。 この構成では、データがネットワークの外に漏洩しないようにするための追加の手順が必要になります。 この動作は、資格情報ベースのデータ アクセスの場合と同じです。 詳細については、[仮想ネットワークのシナリオの構成方法](how-to-access-data.md#virtual-network)に関するページを参照してください。 

## <a name="create-and-register-datastores"></a>データストアの作成と登録

Azure 上のストレージ サービスをデータストアとして登録すると、特定のワークスペースにそのデータストアが自動的に作成および登録されます。 必要なアクセス許可の種類のガイダンスについては、「[ストレージ アクセスのためのアクセス許可](#storage-access-permissions)」を参照してください。 仮想ネットワークの内側にあるデータ ストレージに接続する方法の詳細については、「[仮想ネットワークの使用](#work-with-virtual-networks)」を参照してください。

次のコードでは、認証パラメーター (`sas_token`、`account_key`、`subscription_id`、サービス プリンシパル `client_id` など) がないことに注意してください。 この省略は、Azure Machine Learning での認証に ID ベースのデータ アクセスが使用されることを示します。 データストアの作成は通常、ノートブックまたはスタジオを使用して対話的に行われます。 そのため、データ アクセス認証には Azure Active Directory トークンが使用されます。

> [!NOTE]
> データストア名は、小文字、数字、アンダースコアのみで構成する必要があります。 

### <a name="azure-blob-container"></a>Azure BLOB コンテナー

Azure BLOB コンテナーをデータストアとして登録するには、[`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) を使用します。

次のコードでは、`credentialless_blob` データストアを作成して `ws` ワークスペースに登録し、それが `blob_datastore` 変数に割り当てられます。 このデータストアは、ストレージ アカウント `my-account-name` の BLOB コンテナー `my_container_name` にアクセスします。

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 に接続するデータストアを登録するには、[register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) を使用します。

次のコードでは、`credentialless_adls1` データストアを作成して `workspace` ワークスペースに登録し、それが `adls_dstore` 変数に割り当てられます。 このデータストアは、`adls_storage` Azure Data Lake Storage アカウントにアクセスします。

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 に接続するデータストアを登録するには、[register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) を使用します。

次のコードでは、`credentialless_adls2` データストアを作成して `ws` ワークスペースに登録し、それが `adls2_dstore` 変数に割り当てられます。 このデータストアは、ストレージ アカウント `myadls2` の ファイル システム `tabular` にアクセスします。  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>ストレージ内のデータを使用する

Azure Machine Learning でストレージ内のデータを操作する際は、[Azure Machine Learning データセット](how-to-create-register-datasets.md)のご使用をお勧めします。 

データセットを使用すると、データを機械学習タスク (トレーニングなど) 用に遅延評価された使用可能なオブジェクトにパッケージ化できます。 また、データセットを使用すると、Azure Blob Storage や Azure Data Lake Storage などの Azure ストレージ サービスからコンピューティング先に任意の形式のファイルを[ダウンロードまたはマウント](how-to-train-with-datasets.md#mount-vs-download)できます。


ID ベースのデータ アクセスを使用してデータセットを作成するには、次のオプションがあります。 この種類のデータセットの作成では、データ アクセス認証用の Azure Active Directory トークンが使用されます。 

*  ID ベースのデータ アクセスも使用するデータストアからのパスを参照する。 
<br>次の例では、`blob_datastore` が既に存在し、ID ベースのデータ アクセスが使用されています。   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* データストアの作成をスキップし、ストレージ URL から直接データセットを作成する。 現在、この機能では、Azure BLOB と Azure Data Lake Storage Gen1 および Gen2 のみがサポートされています。

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

ID ベースのデータ アクセスを使用して作成されたデータセットを使用するトレーニング ジョブを送信する場合、トレーニング コンピューティングのマネージド ID がデータ アクセス認証に使用されます。 Azure Active Directory トークンは使用されません。 このシナリオでは、コンピューティングのマネージド ID に、ストレージ サービスから「ストレージ BLOB データ閲覧者」以上のロールが付与されていることを確認します。 詳細については、[コンピューティング クラスターでのマネージド ID の設定](how-to-create-attach-compute-cluster.md#managed-identity)に関する説明を参照してください。 

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)
* [データセットを使ってトレーニングする](how-to-train-with-datasets.md)
* [キー ベースのデータ アクセスを使用してデータストアを作成する](how-to-access-data.md)
