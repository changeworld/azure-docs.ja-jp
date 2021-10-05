---
title: Azure Pipelines と Bicep ファイルを使用した CI/CD
description: Bicep ファイルを使用して、Azure Pipelines で継続的インテグレーションを構成する方法について説明します。 Azure CLI タスクを使用して Bicep ファイルをデプロイする方法を示します。
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/23/2021
ms.openlocfilehash: 1c8817fcc6a0cf24a0983f2df60f02cc42b79200
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764376"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>Bicep を Azure Pipelines に統合する

Bicep ファイルを Azure Pipelines に統合して、継続的インテグレーションと継続的デプロイ (CI/CD) を実現できます。 この記事では、Azure CLI パイプライン タスクを使用して Bicep ファイルをデプロイする方法について説明します。

## <a name="prepare-your-project"></a>プロジェクトを準備する

この記事は、Bicep ファイルと Azure DevOps 組織でパイプラインを作成する準備ができていることを前提としています。 準備ができていることを確認する手順は次のとおりです。

* Azure DevOps 組織があること。 ない場合は、[無料で作成](/azure/devops/pipelines/get-started/pipelines-sign-up)してください。 チームに Azure DevOps 組織が既にある場合は、使用する Azure DevOps プロジェクトの管理者であることを確認します。

* Azure サブスクリプションへの[サービス接続](/azure/devops/pipelines/library/connect-to-azure)が構成済みであること。 パイプライン内のタスクは、サービス プリンシパルの ID で実行されます。 接続を作成する手順については、「[DevOps プロジェクトを作成する](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)」を参照してください。

* プロジェクトのインフラストラクチャを定義する [Bicep ファイル](./quickstart-create-bicep-use-visual-studio-code.md)があること。

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

次の YAML ファイルによってリソース グループが作成され、[Azure CLI タスク](/azure/devops/pipelines/tasks/deploy/azure-cli)を使用して Bicep ファイルがデプロイされます。

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: '<your-resource-group-name>'
  location: '<your-resource-group-location>'
  templateFile: './azuredeploy.bicep'
pool:
  vmImage: $(vmImageName)

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: $(azureServiceConnection)
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az group create --name $(resourceGroupName) --location $(location)
      az deployment group create --resource-group $(resourceGroupName) --template-file $(templateFile)
```

Azure CLI タスクでは、次の入力を受け取ります。

* `azureSubscription`。作成したサービス接続の名前を指定します。  「[プロジェクトを準備する](#prepare-your-project)」を参照してください。
* `scriptType`。**bash** を使用します。
* `scriptLocation`。**inlineScript** または **scriptPath** を使用します。 **scriptPath** を指定する場合は、`scriptPath` パラメーターも指定する必要があります。
* `inlineScript`。ご自分のスクリプト行を指定します。  このサンプルで提供されているスクリプトは、これにより *azuredeploy.bicep* という名前の Bicep ファイルがビルドされ、リポジトリのルートに存在します。

## <a name="next-steps"></a>次のステップ

* Azure Pipelines での Bicep の使用方法の詳細と、ハンズオン ガイダンスについては、**Microsoft Learn** で「[Azure Pipelines を使用して最初の Bicep デプロイ パイプラインを作成する](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/)」を参照してください。
* パイプラインで What-If 操作を使用するには、「[Test ARM templates with What-If in a pipeline](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)」 (パイプラインで What-If を使用して ARM テンプレートをテストする) を参照してください。
* GitHub Actions で Bicep ファイルを使用する方法については、「[GitHub Actions を使用した Bicep ファイルのデプロイ](./deploy-github-actions.md)」を参照してください。