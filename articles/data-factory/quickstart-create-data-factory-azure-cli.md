---
title: 'クイックスタート: Azure CLI を使用して Azure データ ファクトリを作成する'
description: このクイックスタートでは、リンク サービス、データセット、パイプラインを含む Azure データ ファクトリを作成します。 パイプラインを実行して、ファイル コピー アクションを実行できます。
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937956"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure データ ファクトリを作成する

このクイックスタートでは、Azure CLI を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure Blob Storage 内のあるフォルダーから別のフォルダーにコピーします。 Azure Data Factory を使用してデータを変換する方法については、「[Azure Data Factory でデータを変換する](transform-data.md)」を参照してください。

Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、共同作成者ロールまたは所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。 詳細については、[Azure のロール](quickstart-create-data-factory-powershell.md#azure-roles)に関するページをご覧ください。

## <a name="prepare-a-container-and-test-file"></a>コンテナーとテスト ファイルを準備する

このクイックスタートでは、Azure ストレージ アカウントを使用して、コンテナーとファイルを追加します。

1. `ADFQuickStartRG` という名前のリソース グループを作成するには、[az group create](/cli/azure/group#az_group_create) コマンドを使用します。

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. [az storage account create](/cli/azure/storage/container#az_storage_container_create) コマンドを使用してストレージ アカウントを作成します。

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. [az storage container create](/cli/azure/storage/container#az_storage_container_create) コマンドを使用して、`adftutorial` という名前のコンテナーを作成します。

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. ローカル ディレクトリに、`emp.txt` という名前で、アップロードするファイルを作成します。 Azure Cloud Shell で作業している場合、現在の作業ディレクトリは、`echo $PWD` Bash コマンドを使用して調べることができます。 ファイルの作成には、標準的な Bash コマンド (`cat`) を使用できます。

   ```console
   cat > emp.txt
   This is text.
   ```

   **Ctrl + D** キーを使用して、新しいファイルを保存します。

1. Azure Storage コンテナーに新しいファイルをアップロードするには、[az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) コマンドを使用します。

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   このコマンドでは、`input` という名前の新しいフォルダーにアップロードしています。

## <a name="create-a-data-factory"></a>Data Factory の作成

Azure データ ファクトリを作成するには、[az datafactory factory create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) コマンドを実行します。

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> `ADFTutorialFactory` は、グローバルに一意のデータ ファクトリ名に置き換えてください (ADFTutorialFactorySP1127 など)。

作成したデータ ファクトリは、[az datafactory factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) コマンドを使用して確認できます。

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>リンク サービスとデータセットを作成する

次に、リンク サービスと 2 つのデータセットを作成します。

1. [az storage account show-connection-string](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) コマンドを使用してストレージ アカウントの接続文字列を取得します。

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. 作業ディレクトリに、この内容の JSON ファイルを作成し、前の手順で取得した実際の接続文字列を追加します。 このファイルに `AzureStorageLinkedService.json` という名前を付けます。

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. [az datafactory linked-service create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) コマンドを使用して、`AzureStorageLinkedService` という名前のリンク サービスを作成します。

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. 作業ディレクトリに、この内容の JSON ファイルを `InputDataset.json` という名前で作成します。

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. [az datafactory dataset create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) コマンドを使用して、`InputDataset` という名前の入力データセットを作成します。

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. 作業ディレクトリに、この内容の JSON ファイルを `OutputDataset.json` という名前で作成します。

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. [az datafactory dataset create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) コマンドを使用して、`OutputDataset` という名前の出力データセットを作成します。

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>パイプラインを作成して実行する

最後に、パイプラインを作成して実行します。

1. 作業ディレクトリに、この内容の JSON ファイルを `Adfv2QuickStartPipeline.json` という名前で作成します。

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. [az datafactory pipeline create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) コマンドを使用して、`Adfv2QuickStartPipeline` という名前のパイプラインを作成します。

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. [az datafactory pipeline create-run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) コマンドを使用してパイプラインを実行します。

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   このコマンドからは、実行 ID が返されます。 これをコピーしてください。次のコマンドで使用します。

1. [az datafactory pipeline-run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) コマンドを使用して、パイプラインが正常に実行されたことを確認します。

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

パイプラインが想定どおりに実行されたことは、[Azure portal](https://portal.azure.com/) を使用して確認することもできます。 詳細については、「[デプロイされているリソースを確認する](quickstart-create-data-factory-powershell.md#review-deployed-resources)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタート内のリソースはすべて、同じリソース グループに属しています。 それらをすべて削除するには、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用します。

```azurecli
az group delete --name ADFQuickStartRG
```

このリソース グループを何か他の目的で使用する場合は、リソースを個別に削除してください。 たとえば、リンク サービスを削除するには、[az datafactory linked-service delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) コマンドを使用します。

このクイックスタートでは、次の JSON ファイルを作成しました。

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

これらは、標準的な Bash コマンドを使用して削除してください。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)
- [Azure Data Factory のリンクされたサービス](concepts-linked-services.md)
- [Azure Data Factory のデータセット](concepts-datasets-linked-services.md)
- [Azure Data Factory でデータを変換する](transform-data.md)
