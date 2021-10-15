---
title: Azure CLI と Bicep ファイルを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Bicep ファイルに定義されています。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/01/2021
ms.custom: devx-track-azurecli, seo-azure-cli
ms.openlocfilehash: ab371ec2f4cb59b602e953be0043f4dbe49dfb9e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389060"
---
# <a name="how-to-deploy-resources-with-bicep-and-azure-cli"></a>Bicep と Azure CLI を使用してリソースをデプロイする方法

この記事では、Azure CLI と Bicep ファイルを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくない場合は、[Bicep 概要](./overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

デプロイする Bicep ファイルが必要です。 ファイルはローカルである必要があります。

Azure CLI、および Azure に接続されていることが必要です。

- **ご利用のローカル コンピューターに Azure CLI コマンドをインストールします。** Bicep ファイルをデプロイするには、[Azure CLI](/cli/azure/install-azure-cli) バージョン **2.20.0 以降** が必要です。
- **[az login](/cli/azure/reference-index#az_login) を使用して Azure に接続します**。 Azure サブスクリプションが複数ある場合は、[az account set](/cli/azure/account#az_account_set) を実行する必要が生じることもあります。

Azure CLI のサンプルは、`bash` シェル用に記述されています。 このサンプルを Windows PowerShell またはコマンド プロンプトで実行するには、スクリプトの要素を変更する必要があります。

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

現在、Azure CLI ではリモート Bicep ファイルのデプロイはサポートされていません。 [Bicep CLI](./install.md#vs-code-and-bicep-extension) を使用して、Bicep ファイルを JSON テンプレートにコンパイルし、その JSON ファイルをリモートの場所に読み込みます。

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

変数を使用してパラメーター値を格納できます。 Bash では、変数をすべてのパラメーター値に設定し、それをデプロイ コマンドに追加します。

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
```

しかし、Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用する場合は、変数を JSON 文字列に設定します。 引用符をエスケープします (`$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`)。

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

現在、Azure CLI では、Bicep ファイルを指定してテンプレート スペックを作成することはサポートされていません。 ただし、[Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) リソースを使用して Bicep ファイルを作成し、テンプレート スペックをデプロイできます。Bicep ファイルでテンプレート スペックを作成する方法については、[テンプレート スペックの作成サンプル](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep)を参照してください。 また、Bicep CLI を使用して Bicep ファイルを JSON に組み込み、JSON テンプレートを使用してテンプレート スペックを作成することもできます。

## <a name="deployment-name"></a>デプロイ名

Bicep ファイルをデプロイするときに、デプロイに名前を付けることができます。 この名前は、デプロイ履歴からデプロイを取得するのに役立ちます。 デプロイの名前を指定しない場合は、Bicep ファイルの名前が使用されます。 たとえば、`main.bicep` という名前の Bicep ファイルをデプロイするときにデプロイ名を指定しなかった場合、デプロイの名前は `main` になります。

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

* ファイルでパラメーターを定義する方法については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
