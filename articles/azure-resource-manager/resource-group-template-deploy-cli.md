---
title: "Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ

このトピックでは、[Azure CLI 2.0](/cli/azure/install-az-cli2) と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。

これらの例で使用するテンプレート (storage.json) は、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md#final-template)」から取得できます。 このテンプレートを例で使用するには、JSON ファイルを作成し、コピーしたコンテンツを追加します。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

デプロイを速やかに開始するには、次のコマンドを使用してインライン パラメーターを含むローカル テンプレートをデプロイします。

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```azurecli
"provisioningState": "Succeeded",
```

前の例では、既定のサブスクリプションでリソース グループを作成しました。 別のサブスクリプションを使用するには、ログインの後に [az account set](/cli/azure/account#set) コマンドを追加します。

## <a name="deploy-external-template"></a>外部テンプレートのデプロイ

外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。 外部テンプレートの場所は、パブリックにアクセスできる URI (ストレージ アカウント内のファイルなど) であればどこでもかまいません。
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

アクセスの際に Shared Access Signature (SAS) トークンを要求することで、テンプレートを保護することができます。 SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-cli-sas-token.md)」を参照してください。

## <a name="parameter-files"></a>パラメーター ファイル

前の例では、パラメーターをインライン値として渡す方法を示しました。 ファイル内にパラメーター値を指定し、デプロイ時にそのファイルを渡すことができます。 

パラメーター ファイルは次の形式にする必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

ローカル パラメーター ファイルを渡すには、次のコマンドを使用します。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>デプロイのテスト

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[az group deployment validate](/cli/azure/group/deployment#validate) を使用します。 ローカル ファイルを使用する場合も、リモート ファイルを使用する場合も、オプションは同じです。

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>デバッグ

失敗したデプロイの操作についての情報を確認するには、次のコマンドを使用します。
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。


## <a name="export-resource-manager-template"></a>Resource Manager テンプレートのエクスポート
(Azure CLI または、ポータルなど、その他の方法でデプロイされた) 既存のリソース グループの場合、そのリソース グループの Resource Manager テンプレートを参照できます。 テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。
2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

リソース グループのテンプレートを表示するには、[az group export](/cli/azure/group#export) コマンドを実行します。

```azurecli
az group export --name ExampleGroup
```

詳細については、「[Export an Azure Resource Manager template from existing resources (既存のリソースから Azure Resource Manager テンプレートをエクスポートする)](resource-manager-export-template.md)」を参照してください。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

完全モードを使用するには、`mode` パラメーターを使用します。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>次のステップ
* テンプレートのデプロイ用の完全なサンプル スクリプトについては、[Resource Manager テンプレートのデプロイ用のスクリプト](resource-manager-samples-cli-deploy.md)に関するページを参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-cli-sas-token.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。

