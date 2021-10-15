---
title: PowerShell と Bicep を使用してリソースをデプロイする
description: Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは Bicep ファイルに定義されています。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: cc6c8e05f5e6f37a8ac832ac5ee8fae386a627f1
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387717"
---
# <a name="deploy-resources-with-bicep-and-azure-powershell"></a>Bicep と Azure PowerShell を使用してリソースをデプロイする

この記事では、Azure PowerShell と Bicep ファイルを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくない場合は、[Bicep 概要](overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

デプロイする Bicep ファイルが必要です。 ファイルはローカルである必要があります。

Azure PowerShell と、Azure に接続されている必要があります。

- **ローカル コンピューターに Azure PowerShell コマンドレットをインストールします。** Bicep ファイルをデプロイするには、[Azure PowerShell](/powershell/azure/install-az-ps) バージョン **5.6.0 以降** が必要です。 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。
- **[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を使用して Azure に接続します**。 Azure サブスクリプションが複数ある場合は、[Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) を実行する必要が生じることもあります。 詳しくは、「[Use multiple Azure subscriptions (複数の Azure サブスクリプションを使用する)](/powershell/azure/manage-subscriptions-azureps)」をご覧ください。

PowerShell がインストールされていない場合は、Azure Cloud Shell を使用できます。 詳細については、「[Azure Cloud Shell から Bicep ファイルをデプロイする](./deploy-cloud-shell.md)」を参照してください。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 使用するコマンドは、デプロイのスコープに応じて異なります。

- **リソース グループ** にデプロイするには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-bicep>
  ```

- **サブスクリプション** にデプロイするには、`New-AzDeployment` コマンドレットの別名である [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

- **管理グループ** にデプロイするには、[新しい AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)を使用します。

  ```azurepowershell
  New-AzManagementGroupDeployment -ManagementGroupId <management-group-id> -Location <location> -TemplateFile <path-to-bicep>
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

- **テナント** にデプロイするには、[新しい AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

各スコープに対して、テンプレートをデプロイするユーザーにはリソースを作成するためのアクセス許可が必要です。

## <a name="deploy-local-bicep-file"></a>ローカルの Bicep ファイルをデプロイする

ローカル コンピューターの Bicep ファイル、または外部に格納されているものをデプロイできます。 このセクションでは、ローカルの Bicep ファイルのデプロイについて説明します。

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

ローカルの Bicep ファイルをデプロイするには、デプロイ コマンドで `-TemplateFile` パラメーターを使用します。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-bicep>
```

デプロイが完了するまでに数分かかる場合があります。

## <a name="deploy-remote-bicep-file"></a>リモートの Bicep ファイルをデプロイする

現在、Azure PowerShell ではリモート Bicep ファイルのデプロイはサポートされていません。 [Bicep CLI](./install.md#vs-code-and-bicep-extension) を使用して、Bicep ファイルを JSON テンプレートにコンパイルし、その JSON ファイルをリモートの場所に読み込みます。

## <a name="parameters"></a>パラメーター

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`New-AzResourceGroupDeployment` コマンドでパラメーターの名前を指定します。 たとえば、文字列と配列を Bicep ファイルに渡すには、以下を使用します。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../../virtual-machines/linux/using-cloud-init.md)を指定できます。

オブジェクトの配列を渡す必要がある場合、PowerShell でハッシュ テーブルを作成し、そのテーブルを配列に追加します。 デプロイの際に、その配列をパラメーターとして渡します。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは、ローカル ファイルでも、アクセス可能な URI を持つ外部ファイルでもかまいません。 Bicep ファイルでは、JSON パラメーターファイルを使用します。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](./parameter-files.md)」を参照してください。

ローカル パラメーター ファイルを渡すには、`TemplateParameterFile` パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\BicepFiles\storage.bicep `
  -TemplateParameterFile c:\BicepFiles\storage.parameters.json
```

外部パラメーター ファイルを渡すには、`TemplateParameterUri` パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\BicepFiles\storage.bicep `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.parameters.json
```

## <a name="preview-changes"></a>変更のプレビュー

Bicep ファイルをデプロイする前に、Bicep ファイルが環境に与える変更をプレビューすることができます。 [what-if 操作](./deploy-what-if.md)を使用して、必要な変更が Bicep ファイルによって行われるかどうかを確認します。 また、Bicep ファイルのエラーも what-if で検証されます。

## <a name="deploy-template-specs"></a>テンプレート スペックをデプロイする

現在、Azure PowerShell では、Bicep ファイルを指定してテンプレート スペックを作成することはサポートされていません。 ただし、[Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) リソースを使用して Bicep ファイルを作成し、テンプレート スペックをデプロイできます。Bicep ファイルでテンプレート スペックを作成する方法については、[テンプレート スペックの作成サンプル](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep)を参照してください。 また、Bicep CLI を使用して Bicep ファイルを JSON に組み込み、JSON テンプレートを使用してテンプレート スペックを作成することもできます。

## <a name="deployment-name"></a>デプロイ名

Bicep ファイルをデプロイするときに、デプロイに名前を付けることができます。 この名前は、デプロイ履歴からデプロイを取得するのに役立ちます。 デプロイの名前を指定しない場合は、Bicep ファイルの名前が使用されます。 たとえば、`main.bicep` という名前の Bicep をデプロイし、デプロイ名を指定しなかった場合、デプロイは `main` という名前になります。

デプロイを実行するたびに、リソース グループのデプロイ履歴にデプロイ名のエントリが追加されます。 別のデプロイを実行するときに同じ名前を付けると、現在のデプロイによって前のエントリが置き換えられます。 デプロイ履歴に一意のエントリを保持する場合は、デプロイごとに一意の名前を付けます。

一意の名前を作成するために、ランダムな数値を割り当てることができます。

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

または、日付の値を追加します。

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

同じリソース グループに対して同じ名前のデプロイを同時に実行した場合は、最後のデプロイのみが完了します。 完了していない同じ名前のデプロイは、最後のデプロイによって置き換えられます。 たとえば、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを同時に実行した場合は、1 つのストレージ アカウントのみがデプロイされます。 結果のストレージ アカウントの名前は `storage2` になります。

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントは 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="next-steps"></a>次のステップ

- ファイルでパラメーターを定義する方法については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
