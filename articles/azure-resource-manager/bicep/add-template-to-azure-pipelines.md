---
title: Azure Pipelines と Bicep ファイルを使用した CI/CD
description: このクイックスタートでは、Bicep ファイルを使用して、Azure Pipelines で継続的インテグレーションを構成する方法について説明します。 Azure CLI タスクを使用して Bicep ファイルをデプロイする方法を示します。
author: mumian
ms.topic: quickstart
ms.author: jgao
ms.date: 11/16/2021
ms.openlocfilehash: dbca9d692c96d65fa172e9d810e224427c9f5bfc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717020"
---
# <a name="quickstart-integrate-bicep-with-azure-pipelines"></a>クイックスタート: Bicep を Azure Pipelines に統合する

このクイックスタートでは、継続的インテグレーションと継続的デリバリー (Ci/CD) のために Bicep ファイルを Azure Pipelines に統合する方法について説明します。

Bicep ファイルをデプロイするために必要なパイプライン タスクの概要を簡単に示します。 パイプラインとプロジェクトの設定に関する詳細な手順が必要な場合は、**Microsoft Learn** の「[Bicep と Azure Pipelines を使用して Azure リソースをデプロイ](/learn/paths/bicep-azure-pipelines/)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

Azure DevOps 組織が必要です。 ない場合は、[無料で作成](/azure/devops/pipelines/get-started/pipelines-sign-up)してください。 チームに Azure DevOps 組織が既にある場合は、使用する Azure DevOps プロジェクトの管理者であることを確認します。

Azure サブスクリプションへの[サービス接続](/azure/devops/pipelines/library/connect-to-azure)が構成済みである必要があります。 パイプライン内のタスクは、サービス プリンシパルの ID で実行されます。 接続を作成する手順については、「[DevOps プロジェクトを作成する](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)」を参照してください。

プロジェクトのインフラストラクチャを定義する [Bicep ファイル](./quickstart-create-bicep-use-visual-studio-code.md)が必要です。 このファイルはリポジトリにあります。

## <a name="create-pipeline"></a>パイプラインの作成

1. Azure DevOps 組織から、 **[パイプライン]** 、 **[新しいパイプライン]** を選択します。

   ![新しいパイプラインの追加](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. コードの格納場所を指定します。

   ![コードのソースの選択](./media/add-template-to-azure-pipelines/select-source.png)

1. プロジェクトのコードが含まれているリポジトリを選択します。

   ![リポジトリの選択](./media/add-template-to-azure-pipelines/select-repo.png)

1. 作成するパイプラインの種類として **[スタート パイプライン]** を選びます。

   ![パイプラインの選択](./media/add-template-to-azure-pipelines/select-pipeline.png)

## <a name="azure-cli-task"></a>Azure CLI タスク

スタート パイプラインを次の YAML に置き換えます。 これによりリソース グループが作成され、[Azure CLI タスク](/azure/devops/pipelines/tasks/deploy/azure-cli)を使用して Bicep ファイルがデプロイされます。

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: 'exampleRG'
  location: '<your-resource-group-location>'
  templateFile: './main.bicep'
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

* `azureSubscription`。作成したサービス接続の名前を指定します。  「[前提条件](#prerequisites)」を参照してください。
* `scriptType`。**bash** を使用します。
* `scriptLocation`。**inlineScript** または **scriptPath** を使用します。 **scriptPath** を指定する場合は、`scriptPath` パラメーターも指定する必要があります。
* `inlineScript`。ご自分のスクリプト行を指定します。  サンプルで提供されているスクリプトは、*main.bicep* という名前の Bicep ファイルをデプロイします。

**[保存]** を選択します。 ビルド パイプラインが自動的に実行されます。 ビルド パイプラインの概要に戻り、状態を監視します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、Azure CLI または Azure PowerShell を使用してクイックスタートのリソース グループを削除します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub Actions を使用したBicep ファイルのデプロイ](deploy-github-actions.md)
