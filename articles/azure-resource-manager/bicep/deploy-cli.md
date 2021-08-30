---
title: Azure CLI と Bicep ファイルを使用してリソースをデプロイする
description: Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Bicep ファイルに定義されています。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 5e46b920359615c34864d670363b11c451b416b3
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295123"
---
# <a name="deploy-resources-with-bicep-and-azure-cli"></a>Bicep と Azure CLI を使用してリソースをデプロイする

この記事では、Azure CLI と Bicep ファイルを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくない場合は、[Bicep 概要](./overview.md)に関する記事を参照してください。

Bicep ファイルをデプロイするには、[Azure CLI version 2.20.0 以降](/cli/azure/install-azure-cli)が必要です。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、Azure Cloud Shell を使用できます。 詳細については、「[Azure Cloud Shell から Bicep ファイルをデプロイする](./deploy-cloud-shell.md)」を参照してください。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 使用するコマンドは、デプロイのスコープに応じて異なります。

* **リソース グループ** にデプロイするには、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* **サブスクリプション** にデプロイするには、[az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) を使用します。

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

* **管理グループ** にデプロイするには、[az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) を使用します。

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

* **テナント** にデプロイするには、[az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create) を使用します。

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

各スコープに対して、Bicep ファイルをデプロイするユーザーにはリソースを作成するために必要なアクセス許可が付与されていなければなりません。

## <a name="deploy-local-bicep-file"></a>ローカルの Bicep ファイルをデプロイする

ローカル コンピューターの Bicep ファイル、または外部に格納されているものをデプロイできます。 このセクションでは、ローカルの Bicep ファイルのデプロイについて説明します。

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

ローカルの Bicep ファイルをデプロイするには、デプロイ コマンドで `--template-file` パラメーターを使用します。 次の例では、パラメーター値を設定する方法も示しています。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>リモートの Bicep ファイルをデプロイする

現在、Azure CLI ではリモート Bicep ファイルのデプロイはサポートされていません。 [Bicep CLI](./install.md#development-environment) を使用して、Bicep ファイルを JSON テンプレートにコンパイルし、その JSON ファイルをリモートの場所に読み込みます。

## <a name="parameters"></a>パラメーター

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`parameters` に値を指定します。 たとえば、Bash シェルで文字列と配列を Bicep ファイルに渡すには、以下を使用します。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用している場合は、`exampleArray="['value1','value2']"` という形式で配列を渡します。

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../../virtual-machines/linux/using-cloud-init.md)を指定できます。

_arrayContent.json_ 形式は次のようになります。

```json
[
    "value1",
    "value2"
]
```

タグの設定などを目的にオブジェクトを渡すには、JSON を使用します。 たとえば、Bicep ファイルには、次のようなパラメーターを含めることができます。

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

この場合は、次の Bash スクリプトに示すように、JSON 文字列を渡してパラメーターを設定できます。

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

オブジェクトに渡す JSON を二重引用符で囲みます。

変数を使用して、パラメーター値を格納することができます。 Bash で、変数をすべてのパラメーター値に設定し、それをデプロイ コマンドに追加します。

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
``` 

ただし、Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用する場合は、変数を JSON 文字列に設定します。 引用符をエスケープします: `$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`。

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルはローカル ファイルである必要があります。 外部パラメーター ファイルは、Azure CLI ではサポートされていません。 Bicep ファイルでは、JSON パラメーターファイルを使用します。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](./parameter-files.md)」を参照してください。

ローカル パラメーター ファイルを渡すには、`@` を使用して _storage.parameters.json_ という名前のローカル ファイルを指定します。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>変更のプレビュー

Bicep ファイルをデプロイする前に、Bicep ファイルが環境に与える変更をプレビューすることができます。 [what-if 操作](./deploy-what-if.md)を使用して、必要な変更が Bicep ファイルによって行われるかどうかを確認します。 また、Bicep ファイルのエラーも what-if で検証されます。

## <a name="deploy-template-specs"></a>テンプレート スペックをデプロイする

現在、Azure CLI では、Bicep ファイルを指定してテンプレート スペックを作成することはサポートされていません。 ただし、[Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) リソースを使用して Bicep ファイルを作成し、テンプレート スペックをデプロイできます。これがその[例](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)です。 また、Bicep CLI を使用して Bicep ファイルを ARM テンプレート JSON に組み込み、JSON テンプレートを使用してテンプレート スペックを作成することもできます。

## <a name="deployment-name"></a>デプロイ名

Bicep ファイルをデプロイするときに、デプロイに名前を付けることができます。 この名前は、デプロイ履歴からデプロイを取得するのに役立ちます。 デプロイの名前を指定しない場合は、Bicep ファイルの名前が使用されます。 たとえば、`azuredeploy.bicep` という名前の Bicep ファイルをデプロイするときにデプロイ名を指定しなかった場合、デプロイの名前は `azuredeploy` になります。

デプロイを実行するたびに、リソース グループのデプロイ履歴にデプロイ名のエントリが追加されます。 別のデプロイを実行するときに同じ名前を付けると、現在のデプロイによって前のエントリが置き換えられます。 デプロイ履歴に一意のエントリを保持する場合は、デプロイごとに一意の名前を付けます。

一意の名前を作成するために、ランダムな数値を割り当てることができます。

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

または、日付の値を追加します。

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

同じリソース グループに対して同じ名前のデプロイを同時に実行した場合は、最後のデプロイのみが完了します。 完了していない同じ名前のデプロイは、最後のデプロイによって置き換えられます。 たとえば、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを同時に実行した場合は、1 つのストレージ アカウントのみがデプロイされます。 結果のストレージ アカウントの名前は `storage2` になります。

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントは 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="next-steps"></a>次のステップ

* エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](../templates/rollback-on-error.md)」を参照してください。
- ファイルでパラメーターを定義する方法については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../templates/common-deployment-errors.md)」を参照してください。