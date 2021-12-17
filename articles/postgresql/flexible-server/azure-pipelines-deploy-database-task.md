---
title: Azure Database for PostgreSQL フレキシブル サーバーのための Azure Pipelines タスク
description: Azure Pipelines で使用するために Azure Database for PostgreSQL フレキシブル サーバーの CLI タスクを有効にする
ms.topic: quickstart
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.service: postgresql
ms.date: 08/09/2021
ms.openlocfilehash: 2f26ac8b2bea54bc49398b46eae02131991aac1b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363716"
---
# <a name="azure-pipelines-task-for-azure-database-for-postgresql-flexible-server"></a>Azure Database for PostgreSQL フレキシブル サーバーのための Azure Pipelines タスク

**Azure Pipelines** を使用すると、ビルドが成功するたびに、データベース更新プログラムを Azure Database for PostgreSQL フレキシブル サーバーに自動的にデプロイできます。  Azure CLI タスクを使用すれば、データベースに対して SQL ファイルまたはインライン SQL スクリプトのいずれかを使用してデータベースを更新できます。 このタスクは、Linux、macOS、または Windows オペレーティング システムで実行されているクロスプラットフォーム エージェント上で実行できます。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 所有していない場合は、[無料試用版を入手](https://azure.microsoft.com/free/)してください。
- お使いの Azure アカウントへの [Azure Resource Manager サービス接続](/azure/devops/pipelines/library/connect-to-azure)
- Microsoft ホステッド エージェントには Azure CLI が事前にインストールされています。 ただし、プライベート エージェントを使用する予定の場合は、ビルドおよびリリースのエージェントを実行するコンピューターに [Azure CLI をインストール](/cli/azure/install-azure-cli)します。 Azure CLI がインストールされているマシンでエージェントが既に実行中の場合は、関連するすべてのステージ変数が確実に更新されるように、エージェントを再起動します。
- [Azure portal](./quickstart-create-server-portal.md) もしくは [Azure CLI](./quickstart-create-server-cli.md) を使用して Azure Database for PostgreSQL フレキシブル サーバーを作成する


## <a name="use-sql-file"></a>SQL ファイルを使用する

次の例は、データベース引数を渡して ```execute``` コマンドを実行する方法を示しています  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az postgres flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>インライン SQL スクリプトを使用する

次の例は、```execute``` コマンドを使用してインライン SQL スクリプトを実行する方法を示しています。

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription 
      az postgres flexible-server execute --name $(SERVERNAME) --admin-user $(DBUSER) \
      --admin-password '$(DBPASSWORD)'  --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>タスク入力

Azure Pipelines で Azure CLI タスクを使用する際のすべてのタスク入力を示した完全な一覧を参照できます。 

| パラメーター            | 説明         | 
| :------------------- | :-------------------|
| azureSubscription| (必須) デプロイのための Azure Resource Manager サブスクリプションを指定します。 Azure CLI タスク v1.0 でサポートされているのは Azure Resource Manager サブスクリプションのみであるため、このパラメーターは、選択したタスクのバージョンが 0.* の場合にのみ表示されます。 |
|scriptType| (必須) スクリプトの種類を指定します。 サポートされているスクリプトは、PowerShell、PowerShell Core、バッチ、シェル、スクリプトです。 **Linux エージェント** で実行する場合は、```bash``` または ```pscore``` のいずれかを選択します。 **Windows エージェント** で実行する場合は、```batch```、```ps```、```pscore``` のいずれかを選択します。 |
|sriptLocation| (必須) 実際のファイル パスなど、スクリプトへのパスを指定します。または、スクリプトをインラインで指定するときには ```Inline script``` を使用します。 既定値は ```scriptPath``` です。 |
|scriptPath| (必須) スクリプトの完全修飾パス (Windows ベースのエージェントを使用する場合は .ps1、.bat、.cmd で、Linux ベースのエージェントを使用する場合は <code>.ps1 </code> または <code>.sh </code>)、または既定の作業ディレクトリに対する相対パス。 |
|inlineScript|(必須) ここでスクリプトをインラインで記述できます。 Windows エージェントを使用する場合は、PowerShell、PowerShell Core、またはバッチ スクリプトを使用します。一方、Linux ベースのエージェントを使用する場合は PowerShell Core またはシェル スクリプトを使用します。 バッチ ファイルの場合は、すべての Azure コマンドの前にプレフィックス \"call\" を使用します。 引数を使用して、定義済みの変数やカスタム変数をこのスクリプトに渡すこともできます。 <br/>PowerShell/PowerShellCore/シェルの場合の例: ``` az --version az account show``` <br/>バッチの場合の例: ``` call az --version call az account show```。 |
| 引数| (省略可能) スクリプトに渡すすべての引数を指定します。 例: ```-SERVERNAME mydemoserver```。 |
|powerShellErrorActionPreference| (省略可能) PowerShell または PowerShell Core スクリプトの先頭に、<b>$ErrorActionPreference = 'VALUE'</b> という行を付加します。 既定値は stop です。 サポートされている値は、stop、continue、silentlyContinue です。 |
|addSpnToEnvironment|(省略可能) 選択した Azure エンドポイントのサービス プリンシパル ID とキーをスクリプトの実行環境に追加します。 スクリプトでは、変数 <b>$env:servicePrincipalId、$env:servicePrincipalKey、$env:tenantId</b> を使用できます。 これが有効なのは、Azure エンドポイントにサービス プリンシパル認証スキームがある場合のみです。 既定値は false です。|
|useGlobalConfig|(省略可能) これが false の場合、このタスクでは独自の異なる <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">Azure CLI 構成ディレクトリ</a>が使用されます。 これは、"<b>並列</b>リリース" で Azure CLI タスクを実行するために使用できます <br/>既定値: false</td>
|workingDirectory| (省略可能) スクリプトが実行される現在の作業ディレクトリ。  未指定であればリポジトリ (ビルド) または成果物 (リリース) のルートであり、$(System.DefaultWorkingDirectory) です。 |
|failOnStandardError|(省略可能) これが true の場合、このタスクは、StandardError ストリームに何らかのエラーが書き込まれると失敗します。 標準的なエラーを無視し、終了コードに基づいて状態を決定するには、このチェック ボックスをオフにします。 既定値は false です。|
|powerShellIgnoreLASTEXITCODE| (省略可能) これが false の場合、スクリプトの末尾に <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> という行が追加されます。 これにより、外部コマンドの最後の終了コードが、PowerShell の終了コードとして反映されます。 そうでなければ、この行はスクリプトの末尾に追加されません。 既定値は false です。 |

CLI のタスクで問題が発生する場合は、[ビルドとリリースのトラブルシューティングを行う方法](/azure/devops/pipelines/troubleshooting/troubleshooting)に関するページを参照してください。

## <a name="next-steps"></a>次の手順 
Azure Piplelines を使用してデプロイするために使用できる関連タスクを次に示します。

- [Azure リソース グループのデプロイ](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
- [Azure Web アプリのデプロイ](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment)
