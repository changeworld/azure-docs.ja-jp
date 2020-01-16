---
title: Azure Storage サービスのデータにアクセスする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でトレーニング中にデータストアを使用して Azure ストレージ サービスにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535451"
---
# <a name="access-data-in-azure-storage-services"></a>Azure ストレージ サービスのデータにアクセスする
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning データストアを使用して Azure Storage サービスのデータに簡単にアクセスする方法について説明します。 データストアは、サブスクリプション ID やトークン承認など接続情報を格納する目的で使用されます。 データストアを使用するときには、スクリプト内に接続情報をハードコーディングする必要なしに、ストレージにアクセスできます。 

データストアは、[これらの Azure Storage ソリューション](#matrix)から作成できます。 サポートされていないストレージ ソリューションの場合、機械学習実験中のデータ エグレス コストを節約するため、サポートされている Azure Storage ソリューションに[データを移行する](#move)ことをお勧めします。 

## <a name="prerequisites"></a>前提条件
必要なものは次のとおりです。
- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- [Azure BLOB コンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)または [Azure ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)がある Azure ストレージ アカウント。

- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、Python SDK を介して既存のワークスペースを使用します。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>データストアの作成と登録

Azure Storage ソリューションをデータストアとして登録すると、特定のワークスペースにそのデータストアが自動作成されます。 Python SDK または Azure Machine Learning Studio を使用することで、データストアを作成し、ワークスペースに登録することができます。

### <a name="python-sdk"></a>Python SDK

すべての登録メソッドは [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラスにあり、`register_azure_*` という形式になっています。

`register()` メソッドを指定するために必要な情報は、[Azure portal](https://portal.azure.com)を使用して見つけることができます。

1. 左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。 
2. アカウント名、コンテナー、ファイル共有名などの情報については、 **[概要]** ページに移動してください。 アカウント キーや SAS トークンなどの認証情報については、 **[設定]** ウィンドウの **[アクセス キー]** に移動します。 

> [!IMPORTANT]
> お使いのストレージ アカウントが仮想ネットワーク内にある場合は、Azure BLOB データストアの作成のみがサポートされます。 お使いのワークスペースにストレージ アカウントへのアクセス権を付与するには、パラメーター `grant_workspace_access` を `True` に設定します。

次の例は、Azure BLOB コンテナー、Azure ファイル共有、および Azure SQL データをデータストアとして登録する方法を示しています。

#### <a name="blob-container"></a>BLOB コンテナー

Azure BLOB コンテナーをデータストアとして登録するには、[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) を使用します。

次のコードでは、データストア `blob_datastore_name` を作成し、ワークスペース `ws` に登録しています。 このデータストアは、指定されたアカウント キーを使用して、Azure ストレージ アカウント `my-account-name` の BLOB コンテナー `my-container-name` にアクセスします。

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>ファイル共有

Azure ファイル共有をデータストアとして登録するには、[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) を使用します。 

次のコードでは、データストア `file_datastore_name` を作成し、ワークスペース `ws` に登録しています。 このデータストアは、指定されたアカウント キーを使用して、ストレージ アカウント `my-account-name` のファイル共有 `my-fileshare-name` にアクセスします。

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL data

Azure SQL データストアの場合は、SQL 認証またはサービス プリンシパルのアクセス許可を指定して [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) を使用し、Azure SQL データベースに接続された資格情報データストアを登録します。 

SQL 認証を通して登録する場合:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

サービス プリンシパルを通して登録する場合:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>ストレージのガイダンス

Azure BLOB コンテナーをお勧めします。 BLOB では Standard ストレージと Premium ストレージの両方を使用できます。 Premium ストレージはより高価ですが、スループットの速度が上がるため、特に大規模なデータセットに対するトレーニングでは、トレーニングの実行速度が向上する可能性があります。 ストレージ アカウントの費用については、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)に関するページを参照してください。

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Azure Machine Learning Studio のいくつかの手順で、新しいデータストアを作成します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。
1. 左側のウィンドウの **[管理]** で、 **[データストア]** を選択します。
1. **[+ 新しいデータストア]** を選択します。
1. 新しいデータストアのフォームに入力します。 このフォームは、選択した Azure Storage の種類と認証の種類に基づいて、インテリジェントに自動更新されます。
  
フォームを入力するのに必要な情報は、[Azure portal](https://portal.azure.com) で見つけられます。 左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。 **[概要]** ページには、アカウント名、コンテナー、ファイル共有名などの情報が表示されます。 アカウント キーや SAS トークンなどの認証項目については、 **[設定]** ウィンドウの **[アクセス キー]** に移動します。

次の例は、Azure BLOB データストアを作成するときにフォームがどのように表示されるかを示しています。 
    
![新しいデータストアのフォームに入力する](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>ワークスペースからデータストアを取得する

現在のワークスペースに登録されている特定のデータストアを取得するには、`Datastore` クラスの静的メソッド [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) を使用します。

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
特定のワークスペースに登録されているデータストアの一覧を取得するには、ワークスペース オブジェクトに対して [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) プロパティを使用します。

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

ワークスペースを作成すると、ワークスペースに Azure BLOB コンテナーと Azure ファイル共有が自動的に登録されます。 これらの名前は、それぞれ `workspaceblobstore` および `workspacefilestore` となります。 これらには、ワークスペースにアタッチされているストレージ アカウントでプロビジョニングされた BLOB コンテナーとファイル共有の接続情報が格納されます。 `workspaceblobstore` コンテナーは、既定のデータストアとして設定されます。

ワークスペースの既定のデータストアを取得するには、次の行を使用します。

```Python
datastore = ws.get_default_datastore()
```

現在のワークスペースのために、異なる既定のデータストアを定義するには、ワークスペース オブジェクトに対して [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) メソッドを使用します。

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>データのアップロードとダウンロード

次の例に示されている [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) メソッドと [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) メソッドは、[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) クラスと [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) クラスに固有のもので、これらのクラスに対して同様に動作します。

### <a name="upload"></a>アップロード

Python SDK を使用し、データストアにディレクトリまたは個々のファイルをアップロードします。

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` パラメーターでは、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定では `None` になるため、データはルートにアップロードされます。 `overwrite=True` の場合、`target_path` にある既存のデータはすべて上書きされます。

`upload_files()` メソッドを使用して、データストアに個々のファイルの一覧をアップロードすることもできます。

### <a name="download"></a>ダウンロード

データストアからローカル ファイル システムにデータをダウンロードします。

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` パラメーターは、データのダウンロード先となるローカル ディレクトリの場所です。 ダウンロードするファイル共有 (または BLOB コンテナー) 内のフォルダーのパスを指定するには、`prefix` にそのパスを指定します。 `prefix` が `None` である場合は、ファイル共有 (または BLOB コンテナー) の内容がすべてダウンロードされます。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>トレーニング中にデータにアクセスする

> [!IMPORTANT]
> トレーニングでデータにアクセスする場合、[Azure Machine Learning データセット](how-to-create-register-datasets.md)の使用が推奨されるようになりました。 データセットには、Pandas または Spark のデータフレームに表形式のデータを読み込む関数が用意されています。 データセットには、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL から任意の形式のファイルをダウンロードしたりマウントしたりする機能も用意されています。 [データセットを使ってトレーニングする方法の詳細をご覧ください](how-to-train-with-datasets.md)。

次の表に、実行中にデータストアの使用方法をコンピューティング先に指示するメソッドの一覧を示します。 

方法|方法|[説明]|
----|-----|--------
マウントする| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| コンピューティング先にデータストアをマウントするために使用します。 データストアがマウントされると、データストアのすべてのファイルがコンピューティング先からアクセスできるようになります。
ダウンロード|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|データストアの内容を `path_on_compute` によって指定された場所にダウンロードするために使用します。 <br><br> このダウンロードは実行前に行われます。
アップロード|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| `path_on_compute` によって指定された場所からデータストアにファイルをアップロードするために使用します。 <br><br> このアップロードは実行後に行われます。

データストア内の特定のフォルダーまたはファイルを参照し、それらをコンピューティング先で使用できるようにするには、データストアの [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) メソッドを使用します。

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 指定した `datastore` または `datastore.path` オブジェクトはすべて、`"$AZUREML_DATAREFERENCE_XXXX"` 形式の環境変数名に解決されます。 この名前の値は、コンピューティング先でのマウント/ダウンロード パスを表します。 コンピューティング先でのデータストア パスは、トレーニング スクリプトの実行パスと同じだとは限りません。

### <a name="examples"></a>例 

トレーニング中にデータにアクセスするには [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを使用することをお勧めします。 

`script_params` 変数は、`entry_script` へのパラメーターが含まれるディクショナリです。 これを使用してデータストアを渡し、コンピューティング先でデータを使用できるようにする方法を記述します。 [エンド ツー エンド チュートリアル](tutorial-train-models-with-aml.md)では、さらに詳しく説明しています。

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

`Estimator` コンストラクターの `inputs` パラメーターにデータストアのリストを渡し、データストアとの間でデータのマウントやコピーを行うこともできます。 コード例は次のとおりです。
* トレーニング スクリプト `train.py` が実行される前に、`datastore1` の内容をすべてコンピューティング先にダウンロードします。
* `train.py` が実行される前に、`datastore2` 内のフォルダー `'./foo'` をコンピューティング先にダウンロードします。
* スクリプトの実行後、ファイル `'./bar.pkl'` をコンピューティング先から `datastore3` にアップロードします。

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
トレーニングに `RunConfig` オブジェクトを使用する場合は、[`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) オブジェクトを設定する必要があります。 

次のコードは、予測パイプラインで `DataReference` オブジェクトを操作する方法を示しています。 完全な例については、[このノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)を参照してください。

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>コンピューティングとデータストアのマトリックス

現在、データストアは、次のマトリックスに示すストレージ サービスに対する接続情報の格納をサポートしています。 このマトリックスでは、コンピューティング先とデータストアの異なる組み合わせのシナリオで使用可能なデータアクセス機能を示します。 [詳細については Azure Machine Learning のコンピューティング先に関する記事を参照してください](how-to-set-up-training-targets.md#compute-targets-for-training)。

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| ローカル|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|該当なし         |該当なし                                                                         |
| Azure Machine Learning コンピューティング |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[Machine Learning pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[Machine Learning pipelines](concept-ml-pipelines.md)|該当なし         |該当なし                                                                         |
| 仮想マシン               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |該当なし         |該当なし                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |該当なし         |該当なし                                                                         |
| データ転送                  |[Machine Learning パイプライン](concept-ml-pipelines.md)                                               |該当なし                                           |[Machine Learning パイプライン](concept-ml-pipelines.md)            |[Machine Learning パイプライン](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning パイプライン](concept-ml-pipelines.md)                                              |該当なし                                           |[Machine Learning パイプライン](concept-ml-pipelines.md)             |該当なし                                                                         |
| Azure Batch                    |[Machine Learning パイプライン](concept-ml-pipelines.md)                                               |該当なし                                           |該当なし         |該当なし                                                                         |
| Azure Data Lake Analytics       |該当なし                                           |該当なし                                           |[Machine Learning パイプライン](concept-ml-pipelines.md)             |該当なし                                                                         |

> [!NOTE]
> `as_mount()` ではなく `as_download()` を使用して、高度に反復的で大規模なデータ処理をより高速に実行するシナリオが存在する場合があります。 これは実験的に検証することができます。

### <a name="accessing-source-code-during-training"></a>トレーニング中のソース コードへのアクセス

Azure BLOB ストレージは、スループット速度が Azure ファイル共有よりも高く、並列で開始される多数のジョブに対応します。 このため、ソース コード ファイルの転送については、BLOB ストレージを使用するように実行を構成することをお勧めします。

次のコード例では、実行の構成で、ソース コード転送に使用する BLOB データストアを指定しています。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>スコアリング中にデータにアクセスする

Azure Machine Learning には、スコアリングにモデルを使用する方法が複数用意されています。 これらの方法の一部では、データストアへのアクセスが提供されていません。 次の表を使用して、スコアリング中にデータストアへのアクセスが許可されるのはどの方法かを理解します。

| 方法 | データストア アクセス | [説明] |
| ----- | :-----: | ----- |
| [バッチ予測](how-to-run-batch-predictions.md) | ✔ | 大量のデータの予測を非同期的に行います。 |
| [Web サービス](how-to-deploy-and-where.md) | &nbsp; | モデルを Web サービスとしてデプロイします。 |
| [Azure IoT Edge モジュール](how-to-deploy-and-where.md) | &nbsp; | モデルを IoT Edge デバイスにデプロイします。 |

SDK でデータストアへのアクセスが提供されない場合は、関連する Azure SDK を使用してデータにアクセスするカスタム コードを作成できる場合があります。 たとえば、BLOB またはファイルに格納されたデータには、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) というクライアント ライブラリを使用してアクセスすることができます。

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>サポートされている Azure Storage ソリューションにデータを移動する

Azure Machine Learning では、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL からのデータ アクセスがサポートされています。 サポートされていないストレージを使用する場合は、Azure Data Factory を使用して、サポートされている Azure Storage ソリューションにデータを移動することをお勧めします。 サポートされているストレージにデータを移動すると、機械学習実験中のデータ エグレス コストを節約する助けとなります。 

Azure Data Factory では、80 を超える構築済みのコネクタによって、追加コストなしで効率的かつ回復性があるデータ転送が提供されます。 これには、Azure のデータ サービス、オンプレミスのデータ ソース、Amazon S3 および Redshift、Google BigQuery などのコネクタが含まれます。 [ステップ バイ ステップ ガイドに従って、Azure Data Factory を使用してデータを移動してください](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

## <a name="next-steps"></a>次のステップ

* [モデルをトレーニングする](how-to-train-ml-models.md)
* [モデルのデプロイ](how-to-deploy-and-where.md)
