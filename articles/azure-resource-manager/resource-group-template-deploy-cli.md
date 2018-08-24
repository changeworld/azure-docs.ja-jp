---
title: Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2018
ms.author: tomfitz
ms.openlocfilehash: c9595b0e6313dc4620b48296fdca6dc2c6ae6413
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628139"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ

この記事では、Azure CLI と Resource Manager テンプレートを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理に関する概念に精通していない場合は、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。  

デプロイする Resource Manager テンプレートとして、コンピューター上のローカル ファイル、または GitHub などのリポジトリに配置した外部ファイルを使用できます。 この記事でデプロイするテンプレートは、「[サンプル テンプレート](#sample-template)」セクションから、または [GitHub のストレージ アカウントのテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)として入手できます。

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、[Cloud Shell](#deploy-template-from-cloud-shell) を使用できます。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

リソースを Azure にデプロイするときは、以下の手順に従います。

1. Azure アカウントへのサインイン
2. デプロイ済みのリソースのコンテナーとして機能するリソース グループを作成します。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。
3. 作成するリソースを定義するテンプレートをリソース グループにデプロイする

テンプレートには、デプロイをカスタマイズできるパラメーターを含めることができます。 たとえば、特定の環境 (開発、テスト、運用など) 向けにカスタマイズした値を指定できます。 サンプル テンプレートでは、ストレージ アカウント SKU のパラメーターを定義します。 

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>外部テンプレートのデプロイ

Resource Manager テンプレートは、ローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。
   
```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-cli-sas-token.md)に関するページをご覧ください。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Cloud Shell で次のコマンドを使用します。

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>複数のリソース グループまたはサブスクリプションにデプロイする

テンプレートに含まれているリソースはすべて 1 つのリソース グループにデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで、複数のリソース グループまたはサブスクリプションにデプロイしたい状況もあります。 単一のデプロイにデプロイできるリソース グループは 5 つまでです。 詳細については、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](resource-manager-cross-resource-group-deployment.md)」を参照してください。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

失敗したデプロイについては、デプロイ履歴の以前のデプロイが自動的に再デプロイされるように指定できます。 このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。 このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

最後に成功したデプロイを再デプロイするには、`--rollback-on-error` パラメーターをフラグとして追加します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

特定のデプロイを再デプロイするには、`--rollback-on-error` パラメーターを使用してデプロイの名前を指定します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

指定したデプロイは成功している必要があります。

## <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは次の形式にする必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

parameters セクションに、テンプレートで定義したパラメーター (storageAccountType) に一致するパラメーター名が含まれていることに注目してください。 パラメーター ファイルに、このパラメーターの値が含まれます。 この値は、デプロイの際に、テンプレートに自動的に渡されます。 さまざまなデプロイ シナリオに合わせて複数のパラメーター ファイルを作成し、適切なパラメーター ファイルを渡すことができます。 

前の例をコピーし、`storage.parameters.json` という名前のファイルとして保存します。

ローカル パラメーター ファイルを渡すには、`@` を使用して storage.parameters.json という名前のローカル ファイルを指定します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>テンプレートのデプロイをテストする

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate) を使用します。 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

エラーが検出されなかった場合は、テスト デプロイに関する情報が返されます。 具体的には、**error** 値が null であることに注目してください。

```azurecli
{
  "error": null,
  "properties": {
      ...
```

エラーが検出された場合は、エラー メッセージが返されます。 たとえば、ストレージ アカウント SKU について間違った値を渡そうとした場合は、次のエラーが返されます。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

テンプレートに構文エラーがある場合は、テンプレートを解析できなかったことを示すエラー メッセージが返されます。 このメッセージには、解析エラーの行番号と位置が表示されます。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="sample-template"></a>サンプル テンプレート

次のテンプレートをこの記事の例として使います。 このテンプレートをコピーして、storage.json という名前のファイルとして保存します。 このテンプレートを作成する方法にについては、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」を参照してください。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>次の手順
* この記事の例では、既定のサブスクリプションのリソース グループにリソースをデプロイしています。 別のサブスクリプションを使用するには、「[Manage multiple Azure subscriptions (複数の Azure サブスクリプションの管理)](/cli/azure/manage-azure-subscriptions-azure-cli)」を参照してください。
* リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager deployment modes](deployment-modes.md)」(Azure Resource Manager デプロイ モード) を参照してください。
* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-cli-sas-token.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
