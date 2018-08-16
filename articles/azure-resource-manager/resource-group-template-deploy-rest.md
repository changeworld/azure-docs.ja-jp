---
title: REST API とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: ae2393d16d2c9c1000b00f5514e63c988303a83c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628513"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ

この記事では、Resource Manager REST API と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  

> [!TIP]
> デプロイ時のエラーのデバッグについては、以下を参照してください。
> 
> * 「[デプロイ操作の表示](resource-manager-deployment-operations.md)」。エラーのトラブルシューティングに役立つ情報を掲載しています。
> * [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」で、一般的なデプロイ エラーを解決する方法を知る
> 
> 

テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする
1. [一般的なパラメーターおよびヘッダー](/rest/api/azure/) (認証トークンを含む) を設定します。

2. 既存のリソース グループがない場合は、リソース グループを作成します。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。 詳細については、「[リソース グループの作成](/rest/api/resources/resourcegroups/createorupdate)」を参照してください。

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. [テンプレート デプロイを検証する](/rest/api/resources/deployments/validate)操作を実行して、デプロイを実行する前に検証します。 デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。

4. デプロイを作成します。 サブスクリプション ID、リソース グループの名前、デプロイの名前、テンプレートへのリンクを指定します。 テンプレート ファイルについては、「[パラメーター ファイル](#parameter-file)」を参照してください。 リソース グループを作成する REST API の詳細については、「[テンプレートのデプロイを作成する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate)」を参照してください。 **[モード]** が **[増分]** に設定されていることに注意してください。 完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    応答の内容、要求の内容、またはその両方を記録するには、要求に **debugSetting** を含めます。

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。 詳細については、「[Delegating Access with a Shared Access Signature (Shared Access Signature を使用したアクセスの委任)](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)」を参照してください。

5. テンプレートのデプロイの状態を取得します。 詳細については、「[テンプレートのデプロイに関する情報を取得](/rest/api/resources/deployments/get)」を参照してください。

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

失敗したデプロイについては、デプロイ履歴の以前のデプロイが自動的に再デプロイされるように指定できます。 このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。 このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

現在のデプロイが失敗した場合、前回の成功したデプロイを再デプロイするには、次を使用します。

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

現在のデプロイが失敗した場合、特定のデプロイを再デプロイするには、次を使用します。

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

指定したデプロイは成功している必要があります。

## <a name="parameter-file"></a>パラメーター ファイル

デプロイ中にパラメーター ファイルを使用してパラメーター値を渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

パラメーター ファイルのサイズは、64 KB 以下である必要があります。

パラメーターに機密性の高い値(パスワードなど) を提供する必要がある場合は、その値を Key Vault に追加します。 前の例で示したように、デプロイ中に Key Vault を取得します。 詳細については、「 [デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。 

## <a name="next-steps"></a>次の手順
* リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager deployment modes](deployment-modes.md)」(Azure Resource Manager デプロイ モード) を参照してください。
* 非同期 REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「 [Deploy resources using .NET libraries and a template](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。

