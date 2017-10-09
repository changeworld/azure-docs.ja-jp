---
title: "Python を使用して Azure データ ファクトリを作成する | Microsoft Docs"
description: "Azure データ ファクトリを作成して、データを Azure BLOB ストレージ内のある場所から同じ BLOB ストレージ内の別の場所にコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Python を使用してデータ ファクトリとパイプラインを作成する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使用すると、データドリブン型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このクイックスタートでは、Python を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリのパイプラインは、データを Azure BLOB ストレージ内の 1 つのフォルダーから別のフォルダーにコピーします。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure Storage アカウント**。 BLOB ストレージを、**ソース**と**シンク**のデータ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。 
* [この手順](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)に従って、**Azure Active Directory にアプリケーションを作成します**。 **アプリケーション ID**、**認証キー**、**テナント ID** の値をメモしておいてください。後の手順で使用します。 同じ記事の手順に従って、アプリケーションを "**共同作成者**" ロールに割り当てます。 

### <a name="create-and-upload-an-input-file"></a>入力ファイルを作成およびアップロードする

1. メモ帳を起動します。 次のテキストをコピーし、**input.txt** ファイルとしてディスクに保存します。
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  [Azure Storage Explorer](http://storageexplorer.com/) などのツールを使用して **adfv2tutorial** コンテナーを作成し、このコンテナーに input.txt ファイルをアップロードします。 

## <a name="install-the-python-package"></a>Python パッケージをインストールする
1. 管理者特権でターミナルまたはコマンド プロンプトを開きます。  
2. Data Factory 用の Python パッケージをインストールするには、次のコマンドを実行します。

    ```
    pip install azure-mgmt-datafactory
    ```

    [Data Factory 用の Python SDK](https://github.com/Azure/azure-sdk-for-python) では、Python 2.7、3.3、3.4、3.5、および 3.6 がサポートされています。
## <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

1. **datafactory.py** という名前のファイルを作成します。 次のステートメントを追加して、名前空間への参照を追加します。
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. 情報を出力する以下の関数を追加します。 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. DataFactoryManagementClient クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

**データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

**Azure Storage のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、コピー ソースとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、サンプルでは "AzureStorageLinkedService" という名前です。

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>データセットを作成する
このセクションでは、ソース用とシンク用の 2 つのデータセットを作成します。

### <a name="create-a-dataset-for-source-azure-blob"></a>ソース Azure BLOB のためのデータセットを作成する
Azure BLOB データセットを作成する次のコードを Main メソッドに追加します。 Azure BLOB データセットのプロパティの詳細については、[Azure BLOB コネクタ](connector-azure-blob-storage.md#dataset-properties)に関する記事を参照してください。 

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>シンク Azure BLOB のためのデータセットを作成する
Azure BLOB データセットを作成する次のコードを Main メソッドに追加します。 Azure BLOB データセットのプロパティの詳細については、[Azure BLOB コネクタ](connector-azure-blob-storage.md#dataset-properties)に関する記事を参照してください。 

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

**コピー アクティビティが含まれているパイプライン**を作成する次のコードを **Main** メソッドに追加します。

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

**パイプラインの実行をトリガーする**次のコードを **Main** メソッドに追加します。

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>コードの実行
アプリケーションをビルドして起動し、パイプラインの実行を確認します。

コンソールは、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況を表示します。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、変数で指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ
プログラムによって実行するには、データ ファクトリを削除し、次のコード行をプログラムに追加します。 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
