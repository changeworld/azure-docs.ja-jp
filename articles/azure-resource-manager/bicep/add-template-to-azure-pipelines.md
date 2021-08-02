---
title: Azure Pipelines と Bicep ファイルを使用した CI/CD
description: Bicep ファイルを使用して、Azure Pipelines で継続的インテグレーションを構成する方法について説明します。 PowerShell スクリプトを使用する方法、またはファイルをステージングの場所にコピーしてそこからデプロイする方法を示します。
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/01/2021
ms.openlocfilehash: 663d7d74f292971c3421a6c853d7a0ee1325defe
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2021
ms.locfileid: "111528524"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>Bicep を Azure Pipelines に統合する

Bicep ファイルを Azure Pipelines に統合して、継続的インテグレーションと継続的デプロイ (CI/CD) を実現できます。 この記事では、Bicep ファイルを Azure Resource Manager テンプレート (ARM テンプレート) にビルドし、2 つの高度な方法を使用して Azure Pipelines でテンプレートをデプロイする方法について説明します。

## <a name="select-your-option"></a>オプションを選択する

この記事を読み進める前に、パイプラインから ARM テンプレートをデプロイするためのさまざまなオプションについて考えてみましょう。

* **Azure CLI タスクを使用する**。 このタスクを使用して `az bicep build` を実行し、ARM テンプレートをデプロイする前に Bicep ファイルをビルドします。

* **ARM テンプレート デプロイ タスクを使用する**。 このオプションは、最も簡単なオプションです。 この方法は、リポジトリから直接 ARM テンプレートをデプロイする場合に使用できます。 このオプションについては、この記事では説明しませんが、代わりに、「[チュートリアル: Azure Pipelines を使用した ARM テンプレートの継続的インテグレーション](../templates/deployment-tutorial-pipeline.md)」で説明されています。 [ARM テンプレート デプロイ タスク](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)を使用して、GitHub リポジトリからテンプレートをデプロイする方法が示されています。

* **Azure PowerShell スクリプトを実行するタスクを追加する**。 このオプションでは、ローカル テストの実行時に使用したものと同じスクリプトを使用するため、開発ライフ サイクル全体で一貫性が保たれるという利点があります。 スクリプトによってテンプレートがデプロイされますが、他の操作を実行することもできます (パラメーターとして使用する値の取得など)。 このオプションについては、こちらの記事で説明されています。 「[Azure PowerShell タスク](#azure-powershell-task)」を参照してください。

   Visual Studio には、PowerShell スクリプトを含む [Azure リソース グループ プロジェクト](../templates/create-visual-studio-deployment-project.md)が用意されています。 このスクリプトでは、プロジェクトの成果物が、Resource Manager からアクセスできるストレージ アカウントにステージされます。 成果物とはプロジェクト内の項目のことで、リンクされたテンプレート、スクリプト、アプリケーション バイナリなどが該当します。 プロジェクトのスクリプトを引き続き使用する場合は、この記事に示されている PowerShell スクリプト タスクを使用します。

* **コピー タスクとデプロイ タスクを追加する**。 このオプションでは、プロジェクト スクリプトに代わる便利な方法が提供されます。 パイプラインに 2 つのタスクを構成します。 1 つのタスクによって、成果物がアクセス可能な場所にステージングされます。 もう 1 つのタスクによって、その場所からテンプレートがデプロイされます。 このオプションについては、こちらの記事で説明されています。 「[コピーとデプロイのタスク](#copy-and-deploy-tasks)」を参照してください。

## <a name="prepare-your-project"></a>プロジェクトを準備する

この記事は、ARM テンプレートと Azure DevOps 組織でパイプラインを作成する準備ができていることを前提としています。 準備ができていることを確認する手順は次のとおりです。

* Azure DevOps 組織があること。 ない場合は、[無料で作成](/azure/devops/pipelines/get-started/pipelines-sign-up)してください。 チームに Azure DevOps 組織が既にある場合は、使用する Azure DevOps プロジェクトの管理者であることを確認します。

* Azure サブスクリプションへの[サービス接続](/azure/devops/pipelines/library/connect-to-azure)が構成済みであること。 パイプライン内のタスクは、サービス プリンシパルの ID で実行されます。 接続を作成する手順については、「[DevOps プロジェクトを作成する](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)」を参照してください。

* プロジェクトのインフラストラクチャを定義する [ARM テンプレート](../templates/quickstart-create-templates-use-visual-studio-code.md)があること。

## <a name="create-pipeline"></a>パイプラインの作成

1. まだパイプラインを追加していない場合は、新しいパイプラインを作成する必要があります。 Azure DevOps 組織から、 **[パイプライン]** 、 **[新しいパイプライン]** を選択します。

   ![新しいパイプラインの追加](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. コードの格納場所を指定します。 次の図では、 **[Azure Repos Git]** が選択されています。

   ![コードのソースの選択](./media/add-template-to-azure-pipelines/select-source.png)

1. そのソースから、プロジェクトのコードが含まれているリポジトリを選択します。

   ![リポジトリの選択](./media/add-template-to-azure-pipelines/select-repo.png)

1. 作成するパイプラインの種類を選択します。 **[スタート パイプライン]** を選択できます。

   ![パイプラインの選択](./media/add-template-to-azure-pipelines/select-pipeline.png)

Azure PowerShell タスクまたはファイルのコピーとデプロイのタスクのいずれかを追加する準備ができました。

## <a name="azure-cli-task"></a>Azure CLI タスク

このセクションでは、ARM テンプレートをデプロイする前に、Bicep ファイルをテンプレートにビルドする方法について説明します。

次の YAML ファイルは、[Azure CLI タスク](/azure/devops/pipelines/tasks/deploy/azure-cli)を使用して Bicep ファイルをビルドします。

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: 'script-connection'
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az bicep build --file ./azuredeploy.bicep
```

`azureSubscription` には、作成したサービス接続の名前を指定します。

`scriptType` には、**bash** を使用します。

`scriptLocation` には、**inlineScript** または **scriptPath** を使用します。 **scriptPath** を指定する場合は、`scriptPath` パラメーターも指定する必要があります。

`inlineScript` で、ご自分のスクリプト行を指定します。  このサンプルで提供されているスクリプトは、これにより *azuredeploy.bicep* という名前の Bicep ファイルがビルドされ、リポジトリのルートに存在します。

## <a name="azure-powershell-task"></a>Azure PowerShell タスク

このセクションでは、プロジェクト内の PowerShell スクリプトを実行する 1 つのタスクを使用して、継続的デプロイを構成する方法を示します。 テンプレートをデプロイする PowerShell スクリプトが必要な場合は、[Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) または [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1) を参照してください。

次の YAML ファイルでは、[Azure PowerShell タスク](/azure/devops/pipelines/tasks/deploy/azure-powershell)が作成されます。

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

タスクを `AzurePowerShell@5` に設定すると、パイプラインで [Az モジュール](/powershell/azure/new-azureps-module-az)が使用されます。

```yaml
steps:
- task: AzurePowerShell@3
```

`azureSubscription` には、作成したサービス接続の名前を指定します。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

`scriptPath` には、パイプライン ファイルからスクリプトへの相対パスを指定します。 パスはリポジトリで確認できます。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

`ScriptArguments` に、スクリプトで必要なパラメーターを指定します。 次の例では、スクリプトのパラメーターをいくつか示していますが、お使いのスクリプトではパラメーターをカスタマイズする必要があります。

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

**[保存]** を選択すると、ビルド パイプラインが自動的に実行されます。 ビルド パイプラインの概要に戻り、状態を監視します。

![結果の表示](./media/add-template-to-azure-pipelines/view-results.png)

現在実行中のパイプラインを選択し、タスクについての詳細を確認できます。 完了すると、各ステップの結果が表示されます。

## <a name="copy-and-deploy-tasks"></a>コピーとデプロイのタスク

このセクションでは、2 つのタスクを使用して継続的配置を構成する方法を示します。 最初のタスクでは成果物をストレージ アカウントにステージングし、2 番目のタスクではテンプレートをデプロイします。

ファイルをストレージ アカウントにコピーするには、サービス接続のサービス プリンシパルに、ストレージ BLOB データ共同作成者またはストレージ BLOB データ所有者のロールが割り当てられている必要があります。 詳細については、「[AzCopy を使ってみる](../../storage/common/storage-use-azcopy-v10.md)」を参照してください。

次の YAML は、[Azure ファイル コピー タスク](/azure/devops/pipelines/tasks/deploy/azure-file-copy)を示しています。

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

お使いの環境に合わせて、このタスクの複数の部分を変更します。 `SourcePath` は、パイプライン ファイルを基準とした成果物の場所を示します。

```yaml
SourcePath: '<path-to-artifacts>'
```

`azureSubscription` には、作成したサービス接続の名前を指定します。

```yaml
azureSubscription: '<your-connection-name>'
```

ストレージとコンテナーの名前には、成果物の格納に使用するストレージ アカウントとコンテナーの名前を指定します。 ストレージ アカウントは存在している必要があります。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

ファイル コピー タスクを作成したら、タスクを追加して、ステージング済みのテンプレートをデプロイすることができます。

次の YAML は、[Azure Resource Manager テンプレートのデプロイ タスク](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)を示しています。

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

このタスクのいくつかの部分を詳細に説明します。

* `deploymentScope`:デプロイのスコープを、`Management Group`、`Subscription`、および `Resource Group` のオプションから選択します。

* `azureResourceManagerConnection`:作成したサービス接続の名前を指定します。

* `subscriptionId`:ターゲット サブスクリプション ID を指定します。 このプロパティは、リソース グループのデプロイ スコープとサブスクリプションのデプロイ スコープにのみ適用されます。

* `resourceGroupName` および `location`: デプロイ先となるリソース グループの名前と場所を指定します。 リソース グループがない場合は、タスクによって作成されます。

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

* `csmFileLink`:ステージング済みのテンプレートのリンクを指定します。 値を設定する場合は、ファイル コピー タスクから返された変数を使用します。 次の例では、mainTemplate.json という名前のテンプレートにリンクしています。 **templates** という名前のフォルダーが含められています。このフォルダーは、ファイル コピー タスクによってファイルがコピーされた場所であるためです。 パイプラインで、テンプレートへのパスとテンプレートの名前を指定します。

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

パイプラインは次のようになります。

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

**[保存]** を選択すると、ビルド パイプラインが自動的に実行されます。 ビルド パイプラインの概要に戻り、状態を監視します。

## <a name="example"></a>例

次のパイプラインは、Bicep ファイルをビルドする方法と、コンパイルされたテンプレートをデプロイする方法を示しています。

:::code language="yml" source="~/resourcemanager-templates/bicep/azure-pipeline/deploy-bicep.yml":::

## <a name="next-steps"></a>次のステップ

* パイプラインで What-If 操作を使用するには、「[Test ARM templates with What-If in a pipeline](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)」 (パイプラインで What-If を使用して ARM テンプレートをテストする) を参照してください。
* GitHub Actions で Bicep ファイルを使用する方法については、「[GitHub Actions を使用した Bicep ファイルのデプロイ](./deploy-github-actions.md)」を参照してください。
