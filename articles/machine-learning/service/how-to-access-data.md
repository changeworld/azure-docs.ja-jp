---
title: Azure Machine Learning のデータストアを使用してデータにアクセスする
description: トレーニング中にデータストアを使用してデータ ストレージにアクセスする方法
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990809"
---
# <a name="how-to-access-data-during-training"></a>トレーニング中にデータにアクセスする方法
Azure Machine Learning では、データストアは [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) に対する抽象化です。 データストアでは、基になるストレージとして [Azure BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) コンテナーまたは [Azure ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)を参照できます。 データストアを使用すると、Python SDK または CLI を介して、Azure Machine Learning ワークフロー中にデータ ストレージに簡単にアクセスし操作することができます。

## <a name="create-a-datastore"></a>データストアを作成する
データストアを使用するには、まず[ワークスペース](concept-azure-machine-learning-architecture.md#workspace)が必要です。 新しいワークスペースを作成するか、既存のワークスペースを取得することができます。

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>既定のデータストアを使用する
各ワークスペースには、すぐに使用できる既定のデータストアが含まれており、独自のストレージ アカウントを作成して構成する手間を省くことができます。

ワークスペースの既定のデータストアを取得するには、次のコマンドを使用します。
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>データストアを登録する
既存の Azure Storage がある場合は、ワークスペース上のデータストアとして登録できます。 Azure Machine Learning では、Azure Blob コンテナーまたは Azure ファイル共有をデータストアとして登録する機能が提供されます。 すべての登録メソッドは `Datastore` クラス上にあり、`register_azure_*` という形式になります。

#### <a name="azure-blob-container-datastore"></a>Azure BLOB コンテナー データストア
Azure BLOB コンテナー データストアを登録するには、次のコマンドを使用します。

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure ファイル共有データストア
Azure ファイル共有データストアを登録するには、次のコマンドを使用します。

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>既存のデータストアを取得する
登録済みのデータストアを名前でクエリするには、次のコマンドを使用します。
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

ワークスペースのすべてのデータストアを取得することもできます。
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
```

便宜上、登録済みのいずれかのデータストアをワークスペースの既定のデータストアとして設定する場合は、次のコマンドを使用します。
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>データのアップロードとダウンロード
### <a name="upload"></a>アップロード
Python SDK を使用して、データストアにディレクトリまたは個々のファイルをアップロードできます。

データストア `ds` にディレクトリをアップロードするには、次のコマンドを使用します。
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` では、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定値は `None` で、その場合はデータがルートにアップロードされます。 `overwrite=True` では、`target_path` のすべての既存のデータが上書きされます。

または、データストアの `upload_files()` メソッドを使用して、データストアに個々のファイルの一覧をアップロードできます。

### <a name="download"></a>[ダウンロード]
同様に、データストアからローカル ファイル システムにデータをダウンロードできます。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` は、データのダウンロード先となるローカル ディレクトリの場所です。 ダウンロードするファイル共有 (または BLOB コンテナー) 内のフォルダーのパスを指定するには、`prefix` にそのパスを指定します。 `prefix` が `None` である場合は、ファイル共有 (または BLOB コンテナー) のすべてのコンテンツがダウンロードされます。

## <a name="access-datastores-for-training"></a>トレーニングのためにデータストアにアクセスする
Python SDK を使用してリモート コンピューティング ターゲットで、トレーニングの実行中に (トレーニング データや検証データなどのために) データストアにアクセスできます。 

リモート コンピューティング上でデータストアを使用可能にするためにサポートされている方法は 2 つあります。
* **マウント**  
`ds.as_mount()`: このマウント モードを指定すると、リモート コンピューティング上にデータストアが自動的にマウントされます。 
* **ダウンロード/アップロード**  
    * `ds.as_download(path_on_compute='your path on compute')`: このダウンロード モードを指定すると、データストアから、`path_on_compute` によって指定されたリモート コンピューティング内の場所にデータがダウンロードされます。
    * 逆に、トレーニングの実行によって生成されたデータをデータストアにアップロードすることもできます。 たとえば、トレーニング スクリプトによってリモート コンピューティング上の現在の作業ディレクトリ内に `foo.pkl` ファイルが作成される場合、次のように、スクリプトの実行後にこのファイルがデータストアにアップロードされるよう指定できます: `ds.as_upload(path_on_compute='./foo.pkl')`。 これにより、データストアのルートにファイルがアップロードされます。
    
データストア内の特定のフォルダーまたはファイルを参照する場合は、データストアの **`path`** 関数を使用できます。 たとえば、相対パス `./bar` を持つディレクトリがデータストアに存在し、このフォルダーのコンテンツだけをコンピューティング ターゲットにダウンロードする場合、次のようにして実行できます: `ds.path('./bar').as_download()`。

すべての `ds` または `ds.path` オブジェクトは、リモート コンピューティング上のマウント/ダウンロード パスを表す値を持つ、形式 `"$AZUREML_DATAREFERENCE_XXXX"` の環境変数名に解決されます。 リモート コンピューティング上のデータストア パスは、スクリプトの実行パスと同じであるとは限りません。

トレーニング中にデータストアにアクセスするには、`script_params` を使用して、データストアをコマンド ライン引数としてトレーニング スクリプトに渡します。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` はデータストアの既定のモードなので、`'--data_dir'` 引数に `ds` を直接渡すこともできます。

または、推定機能コンストラクターの `inputs` パラメーターにデータストアの一覧を渡して、データストアとの間でマウントまたはコピーを行うことができます。

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
上記のコードによって次の処理が実行されます。
* トレーニング スクリプト `train.py` が実行される前に、データストア `ds1` 内のすべてのコンテンツをリモート コンピューティングにダウンロードします
* `train.py` が実行される前に、データストア `ds2` 内のフォルダー `'./foo'` をリモート コンピューティングにダウンロードします
* スクリプトが実行された後に、リモート コンピューティングからデータストア `d3` にファイル `'./bar.pkl'` をアップロードします

## <a name="next-steps"></a>次の手順
* [モデルをトレーニングする](how-to-train-ml-models.md)
