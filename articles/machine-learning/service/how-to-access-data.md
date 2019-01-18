---
title: トレーニングのためにデータストア内のデータにアクセスする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でトレーニング中にデータストアを使用してデータ ストレージにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 235ad729be434c8329a74e71abfe713480756316
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187145"
---
# <a name="access-data-during-training-from-your-datastores"></a>トレーニング中にデータストアのデータにアクセスする
Azure Machine Learning ワークフローでは、データストアにアクセスしてデータを操作します。

Azure Machine Learning サービスでは、データストアは [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) に対する抽象化です。 データストアでは、基になるストレージとして [Azure BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) コンテナーまたは [Azure ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)を参照できます。 

## <a name="create-a-datastore"></a>データストアを作成する
データストアを使用するには、まず[ワークスペース](concept-azure-machine-learning-architecture.md#workspace)が必要です。 まず、[新しいワークスペースを作成する](quickstart-create-workspace-with-python.md)か、既存のワークスペースを取得します。

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>既定のデータストアを使用する
ストレージ アカウントを作成または構成する必要はありません。  各ワークスペースには、すぐに使い始めることができる既定のデータストアがあります。

ワークスペースの既定のデータストアを取得するには、次のコマンドを使用します。
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>データストアを登録する
既存の Azure Storage がある場合は、ワークスペース上のデータストアとして登録できます。 Azure BLOB コンテナーまたは Azure ファイル共有をデータストアとして登録することができます。 すべての登録メソッドは `Datastore` クラス上にあり、`register_azure_*` という形式になります。

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
    print(name, ds.datastore_type)
```

便宜的に、登録済みのデータストアの 1 つをワークスペースの既定のデータストアとして設定します。
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>データのアップロードとダウンロード
### <a name="upload"></a>アップロード
Python SDK を使用して、データストアにディレクトリまたは個々のファイルをアップロードします。

データストア `ds` にディレクトリをアップロードするには、次のコマンドを使用します。
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` では、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定値は `None` で、その場合はデータがルートにアップロードされます。 `overwrite=True` では、`target_path` のすべての既存のデータが上書きされます。

または、データストアの `upload_files()` メソッドを使用して、データストアに個々のファイルの一覧をアップロードします。

### <a name="download"></a>ダウンロード
同様に、データストアからローカル ファイル システムにデータをダウンロードします。

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
    * `ds.as_download(path_on_compute='your path on compute')` では、データストアからリモート コンピューティングの `path_on_compute` で指定された場所にデータがダウンロードされます。
    * `ds.as_upload(path_on_compute='yourfilename'` では、データがデータストアにアップロードされます。  トレーニング スクリプトで、リモート コンピューティング上の現在の作業ディレクトリに `foo.pkl` ファイルを作成したとします。 スクリプトによってこのファイルが作成された後、`ds.as_upload(path_on_compute='./foo.pkl')` を使用してファイルをデータストアにアップロードします。 ファイルはデータストアのルートにアップロードされます。
    
データストア内の特定のフォルダーまたはファイルを参照するには、データストアの **`path`** 関数を使用します。 たとえば、データストアの `./bar` ディレクトリの内容をコンピューティング ターゲットにダウンロードするには、`ds.path('./bar').as_download()` を使用します。

すべての `ds` または `ds.path` オブジェクトは、リモート コンピューティング上のマウント/ダウンロード パスを表す値を持つ、形式 `"$AZUREML_DATAREFERENCE_XXXX"` の環境変数名に解決されます。 リモート コンピューティング上のデータストア パスは、スクリプトの実行パスと同じであるとは限りません。

トレーニング中にデータストアにアクセスするには、`script_params` を使用して、データストアをコマンドライン引数としてトレーニング スクリプトに渡します。

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
