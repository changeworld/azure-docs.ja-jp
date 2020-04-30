---
title: Azure CLI とテンプレートを使用してリソースをデプロイする
description: Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685701"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>ARM テンプレートと Azure CLI でリソースをデプロイする

この記事では、Azure CLI と Azure Resource Manager (ARM) テンプレートを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくない場合、「[テンプレートのデプロイの概要](overview.md)」をご覧ください。

デプロイ コマンドは Azure CLI バージョン 2.2.0 で変更されました。 この記事の例では、Azure CLI バージョン 2.2.0 以降が必要です。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、[Cloud Shell](#deploy-template-from-cloud-shell) を使用できます。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 多くの場合、リソース グループをデプロイの対象にします。 より大きなスコープでポリシーとロールの割り当てを適用するには、サブスクリプション、管理グループ、またはテナントのデプロイを使用します。 サブスクリプションにデプロイする際には、リソース グループを作成してそこにリソースをデプロイすることができます。

使用するコマンドは、デプロイのスコープに応じて異なります。

**リソース グループ**にデプロイするには、[az deployment group create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create) を使用します。

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

**サブスクリプション**にデプロイするには、[az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create) を使用します。

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

**管理グループ**にデプロイするには、[az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create) を使用します。

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

**テナント**にデプロイするには、[az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create) を使用します。

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

この記事の例では、リソース グループ デプロイを使用します。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

リソースを Azure にデプロイするときは、以下の手順に従います。

1. Azure アカウントへのサインイン
2. デプロイ済みのリソースのコンテナーとして機能するリソース グループを作成します。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。
3. 作成するリソースを定義するテンプレートをリソース グループにデプロイする

テンプレートには、デプロイをカスタマイズできるパラメーターを含めることができます。 たとえば、特定の環境 (開発、テスト、運用など) 向けにカスタマイズした値を指定できます。 サンプル テンプレートでは、ストレージ アカウント SKU のパラメーターを定義します。

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

ARM テンプレートをローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](secure-template-with-sas-token.md)に関するページをご覧ください。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Cloud Shell で次のコマンドを使用します。

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>パラメーター

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`parameters` に値を指定します。 たとえば、Bash シェルで文字列と配列をテンプレートに渡すには、以下を使用します。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用している場合は、`exampleArray="['value1','value2']"` という形式で配列を渡します。

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../../virtual-machines/linux/using-cloud-init.md)を指定できます。

arrayContent.json 形式は次のようになります。

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルはローカル ファイルである必要があります。 外部パラメーター ファイルは、Azure CLI ではサポートされていません。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](parameter-files.md)」を参照してください。

ローカル パラメーター ファイルを渡すには、`@` を使用して storage.parameters.json という名前のローカル ファイルを指定します。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>拡張 JSON 形式を処理する

バージョン 2.3.0 以下の Azure CLI を使用して、複数行の文字列またはコメントを含むテンプレートをデプロイするには、`--handle-extended-json-format` スイッチを使用する必要があります。  次に例を示します。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>テンプレートのデプロイをテストする

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[az deployment group validate](/cli/azure/group/deployment) を使用します。

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

エラーが検出されなかった場合は、テスト デプロイに関する情報が返されます。 具体的には、**error** 値が null であることに注目してください。

```output
{
  "error": null,
  "properties": {
      ...
```

エラーが検出された場合は、エラー メッセージが返されます。 たとえば、ストレージ アカウント SKU について間違った値を渡した場合は、次のエラーが返されます。

```output
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

```output
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

## <a name="next-steps"></a>次のステップ

- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
- 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](secure-template-with-sas-token.md)」を参照してください。
- 複数のリージョン間で、サービスを安全にロールアウトするには、[Azure Deployment Manager](deployment-manager-overview.md) に関する記事を参照してください。
