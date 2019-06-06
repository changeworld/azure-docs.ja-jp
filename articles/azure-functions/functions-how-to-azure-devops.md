---
title: Azure DevOps を使用して、関数コードの更新プログラムを継続的に提供する
description: Azure Functions をターゲットとする、Azure DevOps パイプラインを設定する方法について学習します。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 27b5dc9ccee8647d4fbb617063865df18b80bc5d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990273"
---
# <a name="continuous-delivery-using-azure-devops"></a>Azure DevOps を使用した継続的デリバリー

[Azure Pipelines](/azure/devops/pipelines/) を使用して、関数を Azure 関数アプリに自動的にデプロイできます。
パイプラインを定義するには、次を使用することができます。

- YAML ファイル:このファイルは、パイプラインを記述します。これにはビルド ステップ セクションとリリース セクションが含まれている場合があります。 YAML ファイルは、アプリと同じリポジトリにある必要があります。

- テンプレート:テンプレートは、アプリをビルドまたはデプロイする既製のタスクです。

## <a name="yaml-based-pipeline"></a>YAML ベースのパイプライン

### <a name="build-your-app"></a>アプリの構築

Azure Pipelines でのアプリのビルドは、アプリのプログラミング言語に依存します。
言語にはそれぞれ、Azure で関数アプリのデプロイに使用できるデプロイ成果物を作成するための固有のビルド ステップがあります。

#### <a name="net"></a>.NET

次のサンプルを使用して、.NET アプリをビルドする YAML ファイルを作成できます。

```yaml
jobs:
  - job: Build
    pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

次のサンプルを使用して、JavaScript アプリをビルドする YAML ファイルを作成できます。

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

次のサンプルを使用して、Python アプリをビルドする YAML ファイルを作成できます。Python は Linux の Azure Functions でのみサポートされます。

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>アプリをデプロイする

ホストしている OS によっては、YAML ファイルに次の YAML サンプルを含める必要があります。

#### <a name="windows-function-app"></a>Windows の関数アプリ

次のスニペットは、Windows の関数アプリへのデプロイに使用できます。

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Linux の関数アプリ

次のスニペットは、Linux の関数アプリへのデプロイに使用できます。

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>テンプレート ベースのパイプライン

Azure DevOps ではテンプレートは、アプリをビルドまたはデプロイするタスクの定義済みのグループです。

### <a name="build-your-app"></a>アプリの構築

Azure Pipelines でのアプリのビルドは、アプリのプログラミング言語に依存します。 言語にはそれぞれ、Azure で関数アプリの更新に使用できるデプロイ成果物を作成するための固有のビルド ステップがあります。
新しいビルド パイプラインを作成するときに、組み込みのビルド テンプレートを使用するには、 **[従来のエディターを使用する]** を選択して、デザイナー テンプレートを使用してパイプラインを作成します。

![Azure Pipelines の従来のエディター](media/functions-how-to-azure-devops/classic-editor.png)

コードのソースを構成したら、Azure Functions のビルド テンプレートを検索し、アプリの言語と一致するテンプレートを選択します。

![Azure Functions ビルド テンプレート](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>JavaScript アプリ

JavaScript アプリに Windows のネイティブ モジュールへの依存関係がある場合は、次を更新する必要があります。

- エージェント プールのバージョンを **Hosted VS2017** にする

  ![ビルド エージェントの OS を変更](media/functions-how-to-azure-devops/change-agent.png)

- テンプレート内の**ビルドの拡張**ステップのスクリプトを `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin` にする

  ![変更スクリプト](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>アプリをデプロイする

新しいリリース パイプラインを作成するときに、Azure Functions のリリース テンプレートを検索します。

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Azure CLI を使用して Azure パイプラインを作成する

`az functionapp devops-pipeline create` [コマンド](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)を使用すると、リポジトリ内の任意のコード変更をビルドおよびリリースする Azure パイプラインが作成されます。 このコマンドにより、ビルドとリリース パイプラインを定義し、それをリポジトリにコミットする新しい YAML ファイルが生成されます。
このコマンドの前提条件は、コードの場所によって異なります。

- コードが GitHub にある場合:

    - サブスクリプションへの**書き込み**アクセス許可が必要です。

    - Azure DevOps でのプロジェクト管理者になります。

    - 十分なアクセス許可を持つ GitHub 個人用アクセス トークンを作成するアクセス許可を持ちます。 [GitHub PAT アクセス許可の要件。](https://aka.ms/azure-devops-source-repos)

    - 自動生成された YAML ファイルをコミットする GitHub リポジトリのマスター ブランチにコミットするためのアクセス許可があります。

- コードが Azure Repos にある場合:

    - サブスクリプションへの**書き込み**アクセス許可が必要です。

    - Azure DevOps でのプロジェクト管理者になります。

## <a name="next-steps"></a>次の手順

+ [Azure Functions の概要](functions-overview.md)
+ [Azure DevOps の概要](/azure/devops/pipelines/)
