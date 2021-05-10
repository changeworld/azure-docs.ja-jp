---
title: クイック スタート:Python を使用して Azure データ ファクトリを作成する
description: データ ファクトリを使用して、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 04/12/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 534b5b3aca86cc2f6d7ee2d703939420f80abb8e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365095"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>クイック スタート:Python を使用してデータ ファクトリとパイプラインを作成する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このクイックスタートでは、Python を使用してデータ ファクトリを作成します。 このデータ ファクトリのパイプラインは、データを Azure Blob Storage 内の 1 つのフォルダーから別のフォルダーにコピーします。

Azure Data Factory は、データドリブン型のワークフローを作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使用すると、パイプラインと呼ばれるデータ駆動型ワークフローを作成し、スケジュールを設定できます。

パイプラインは、さまざまなデータ ストアからデータを取り込むことができます。 パイプラインは、Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning などのコンピューティング サービスを使ってデータを処理または変換します。 パイプラインは、ビジネス インテリジェンス (BI) アプリケーションで使用できるように、Azure Synapse Analytics などのデータ ストアに出力データを公開します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.6 以降](https://www.python.org/downloads/)。

* [Azure Storage アカウント](../storage/common/storage-account-create.md)。

* [Azure Storage Explorer](https://storageexplorer.com/) (省略可)。

* [Azure Active Directory のアプリケーション](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 このリンク先の手順に従ってアプリケーションを作成し、同じ記事の手順に従ってこのアプリケーションを **共同作成者** ロールに割り当てます。 記事に示されているように後の手順で使用するために、**アプリケーション ID (以下のサービス プリンシパル ID)、認証キー (以下のクライアント シークレット)、およびテナント ID** の値をメモしておきます。

## <a name="create-and-upload-an-input-file"></a>入力ファイルを作成およびアップロードする

1. メモ帳を起動します。 次のテキストをコピーし、**input.txt** ファイルとしてディスクに保存します。

    ```text
    John|Doe
    Jane|Doe
    ```
2.  [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して **adfv2tutorial** コンテナーを作成し、このコンテナーに **input** フォルダーを作成します。 次に、**input** フォルダーに **input.txt** ファイルをアップロードします。

## <a name="install-the-python-package"></a>Python パッケージをインストールする

1. 管理者特権でターミナルまたはコマンド プロンプトを開きます。 
2. まず、Azure 管理リソースの Python パッケージをインストールします。

    ```python
    pip install azure-mgmt-resource
    ```
3. Data Factory 用の Python パッケージをインストールするには、次のコマンドを実行します。

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Data Factory 用の Python SDK](https://github.com/Azure/azure-sdk-for-python) では、Python 2.7 および 3.6 以降がサポートされています。

4. Azure Identity Authentication 用の Python パッケージをインストールするには、次のコマンドを実行します。

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "azure-identity" パッケージは、いくつかの共通の依存関係に関して、"azure-cli" と競合する可能性があります。 認証の問題が発生した場合は、"azure-cli" とその依存関係を削除するか、"azure-cli" パッケージがインストールされていないクリーン マシンを使用して解決してください。
    > ソブリン クラウドの場合は、適切なクラウド固有の定数を使用する必要があります。  ソブリン クラウドで Python を使用して接続する手順については、[Python マルチクラウド用 Azure ライブラリを使用したすべてのリージョンへの接続](https://docs.microsoft.com/azure/developer/python/azure-sdk-sovereign-domain)に関する記事をご覧ください。
    
    
## <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

  
1. **datafactory.py** という名前のファイルを作成します。 次のステートメントを追加して、名前空間への参照を追加します。

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
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

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. DataFactoryManagementClient クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。 **subscription_id** 変数を、ご使用の Azure サブスクリプションの ID に設定します。 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

        
    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Data Factory の作成

**データ ファクトリ** を作成する次のコードを **Main** メソッドに追加します。 リソース グループが既に存在する場合は、最初の `create_or_update` ステートメントをコメント アウトします。

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

**Azure Storage のリンクされたサービス** を作成する次のコードを **Main** メソッドに追加します。

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、コピー ソースとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、サンプルでは "AzureStorageLinkedService" という名前です。 `<storageaccountname>` と `<storageaccountkey>` を、Azure ストレージ アカウントの名前とキーで置き換えます。

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>データセットを作成する

このセクションでは、ソース用とシンク用の 2 つのデータセットを作成します。

### <a name="create-a-dataset-for-source-azure-blob"></a>ソース Azure BLOB のためのデータセットを作成する

Azure BLOB データセットを作成する次のコードを Main メソッドに追加します。 Azure BLOB データセットのプロパティの詳細については、[Azure BLOB コネクタ](connector-azure-blob-storage.md#dataset-properties)に関する記事を参照してください。

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>シンク Azure BLOB のためのデータセットを作成する

Azure BLOB データセットを作成する次のコードを Main メソッドに追加します。 Azure BLOB データセットのプロパティの詳細については、[Azure BLOB コネクタ](connector-azure-blob-storage.md#dataset-properties)に関する記事を参照してください。

Azure BLOB 内のソース データを表すデータセットを定義します。 この BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```


## <a name="create-a-pipeline"></a>パイプラインを作成する

**コピー アクティビティが含まれているパイプライン** を作成する次のコードを **Main** メソッドに追加します。

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    
    #Note1: To pass parameters to the pipeline, add them to the json string params_for_pipeline shown below in the format { “ParameterName1” : “ParameterValue1” } for each of the parameters needed in the pipeline.
    #Note2: To pass parameters to a dataflow, create a pipeline parameter to hold the parameter name/value, and then consume the pipeline parameter in the dataflow parameter in the format @pipeline().parameters.parametername.
    
    p_name = 'copyPipeline'
    params_for_pipeline = {}

    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

**パイプラインの実行をトリガーする** 次のコードを **Main** メソッドに追加します。

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

パイプラインの実行を監視するには、次のコードを **Main** メソッドに追加します。

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

プログラムの実行時に **main** メソッドを呼び出す次のステートメントを追加します。

```python
# Start the main method
main()
```

## <a name="full-script"></a>完全なスクリプト

完全な Python コードを次に示します。

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

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

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、パイプラインの実行を確認します。

コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、変数で指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。

出力例を次に示します。

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

データ ファクトリを削除するには、プログラムに次のコードを追加します。

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>次のステップ

このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。
