---
title: 実験またはモデル管理データをエクスポートまたは削除する - Azure Machine Learning | Microsoft Docs
description: Azure Machine Learning では、実験またはモデル管理に関連するアカウントを、Azure Portal、CLI、SDK、および認証済み REST API を使用してエクスポートまたは削除できます。 この記事では、その方法について説明します。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 5475ce3be24321b15ab78a078b758c25843f0ed3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723684"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Machine Learning で 実験またはモデル管理データをエクスポートまたは削除する

Azure Machine Learning では、実験またはモデル管理に関連するアカウントを、認証済みの REST API を使用してエクスポートまたは削除できます。 この記事では、その方法について説明します。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>アカウント データを管理する
Azure Machine Learning の実験とモデル管理によって格納された製品内データは、Azure Portal、CLI、SDK、および認証済み REST API を介してエクスポートと削除を実行できます。 テレメトリ データには、Azure Privacy Portal を介してアクセスできます。 

Azure Machine Learning では、個人データは、実行履歴ドキュメント内のユーザー情報と、ユーザーのサービスとの対話のテレメトリ レコードで構成されます。

## <a name="delete-account-data-with-the-rest-api"></a>REST API を使用してアカウント データを削除する 

データを削除するには、HTTP DELETE 動詞を使用して次の API 呼び出しを実行できます。 これらは、要求内に `Authorization: Bearer <arm-token>` ヘッダー があることで承認されます (`<arm-token>` は `https://management.core.windows.net/` エンドポイントの AAD アクセス トークンです)。  

このトークンの取得と Azure エンドポイントの呼び出し方法については、[Azure REST API のドキュメント](https://docs.microsoft.com/rest/api/azure/)を参照してください。  

以下の例では、{} 内のテキストを、関連付けられているリソースを決定するインスタンス名に置き換えてください。

## <a name="delete-from-a-hosting-account"></a>ホスティング アカウントから削除する

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>モデル管理サービスから削除する

### <a name="model-document"></a>モデル ドキュメント
モデルとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

個々のモデルは、次の呼び出しで削除できます。  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>マニフェスト ドキュメント
すべてのマニフェストとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

個々のマニフェストは、次の呼び出しで削除できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>サービス ドキュメント
すべてのサービスとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

個々のサービスは、次の呼び出しで削除できます。    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>イメージ ドキュメント
すべてのイメージとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

個々のイメージは、次の呼び出しで削除できます。  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>実行履歴、成果物、および通知データを削除する
プロジェクトの実行履歴、成果物、および通知ストアは、対応するプロジェクトのドキュメントの削除後にすべて削除されます。

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>実験アカウント リソース プロバイダーから削除する
プロジェクトのドキュメントは、次の呼び出しで削除されます。

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

ワークスペースのドキュメントは、次の呼び出しで削除されます。

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

実験アカウント全体は、次の呼び出しで削除されます。
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>REST API を使用してサービス データをエクスポートする
データをエクスポートするには、HTTP GET 動詞を使用して次の API 呼び出しを実行できます。 これらは、要求内に `Authorization: Bearer <arm-token>` ヘッダー があることで承認されます (`<arm-token>` は `https://management.core.windows.net/` エンドポイントの AAD アクセス トークンです)。  

このトークンの取得と Azure エンドポイントの呼び出し方法については、[Azure REST API のドキュメント](https://docs.microsoft.com/rest/api/azure/)を参照してください。   

以下の例では、{} 内のテキストを、関連付けられているリソースを決定するインスタンス名に置き換えてください。

## <a name="export-hosting-account-data"></a>ホスティング アカウント データをエクスポートする

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>モデル管理サービス データをエクスポートする
### <a name="model-document"></a>モデル ドキュメント

モデルとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

個々のモデルは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>マニフェスト
すべてのマニフェストとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

個々のマニフェストは、次の呼び出しで取得できます。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>サービス
すべてのサービスとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

個々のサービスは、次の呼び出しで取得できます。 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>イメージ
すべてのイメージとその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

個々のサービスは、次の呼び出しで取得できます。 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>コンピューティング データをエクスポートする
### <a name="compute-clusters"></a>コンピューティング クラスター
すべてのコンピューティング クラスターとその名前の一覧を取得するには、次の呼び出しを使用します。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

個々のクラスターは、次の呼び出しで取得できます。

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>運用化クラスター
すべてのクラスターとその名前の一覧を取得するには、次の呼び出しを使用します。

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

個々のクラスターは、次の呼び出しで取得できます。

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>実行履歴データをエクスポートする
すべての実行とその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

すべての実験とその ID の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

実行履歴項目は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

実行メトリック項目は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

実行実験は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

実行履歴の成果物は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

実行履歴の成果物の URI は、次の呼び出しで取得できます。

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>成果物をエクスポートする
資産とその名前の一覧を取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

成果物とそのパスの一覧を取得するには、次の呼び出しを使用します。

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>成果物の内容

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>成果物のドキュメント

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>資産

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>通知をエクスポートする

1. [Azure Portal の [ユーザー] セクション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/)に移動し、**[名前]** 列からユーザーを選択します。 
2. **[オブジェクト ID]** を書き留め、それを次の呼び出しで使用します。     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>実験アカウント情報をエクスポートする
### <a name="experimentation-account-information"></a>実験アカウント情報

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>ワークスペース情報

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>プロジェクト情報

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
