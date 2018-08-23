---
title: Azure Machine Learning プレビュー機能のチュートリアル記事 - コマンド ライン インターフェイス | Microsoft Docs
description: このチュートリアルでは、コマンド ライン インターフェイスを使って Iris 分類を始めから終わりまで実行するために必要なすべての手順について説明します。
services: machine-learning
author: jpe316
ms.author: jordane
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 10fe861682da6c1d1ac701a565cef11f9b44cd1e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41918685"
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>チュートリアル: コマンド ライン インターフェイスを使用して Iris を分類する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルでは、Azure Machine Learning プレビュー機能でコマンド ライン インターフェイス (CLI) ツールを使って次のことを行う方法を説明します。 
> [!div class="checklist"]
> * 実験アカウントを設定してワークスペースを作成する
> * プロジェクトの作成
> * 複数のコンピューティング ターゲットに実験を送信する
> * トレーニング済みのモデルを昇格させて登録する
> * 新しいデータをスコア付けする Web サービスをデプロイする

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次のものが必要です。
- Azure サブスクリプションへのアクセス権と、そのサブスクリプションにリソースを作成するアクセス許可があること。 
  
  Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- [Azure Machine Learning サービスのインストールと基本操作のクイック スタート](../service/quickstart-installation.md)の説明に従って Azure Machine Learning Workbench アプリケーションがインストールされていること。 

  >[!IMPORTANT]
  >Azure Machine Learning サービス アカウントは作成しないでください。サービス アカウントの作成は、この記事の中で CLI を使用して行います。
 
## <a name="getting-started"></a>使用の開始
Azure Machine Learning コマンド ライン インターフェイス (CLI) では、データ サイエンス ワークフローの開始から終了までに必要なすべてのタスクを実行することができます。 CLI ツールには次の方法でアクセスできます。

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>方法 1. Azure ML Workbench ログイン ダイアログ ボックスから Azure ML CLI を起動する
Azure ML Workbench を初めて起動してログインするとき、実験アカウントへのアクセス権がまだない場合は、次の画面が表示されます。

![アカウントが見つかりませんでした](media/tutorial-iris-azure-cli/no_account_found.png)

ダイアログ ボックスの **[Command Line Window]\(コマンド ライン ウィンドウ\)** リンクをクリックして、コマンド ライン ウィンドウを起動します。

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>方法 2. Azure ML Workbench アプリから Azure ML CLI を起動する
実験アカウントへのアクセス権が既にある場合は、正常にログインすることができます。 その後、**[ファイル]** --> **[コマンド プロンプトを開く]** メニューをクリックして、コマンド ライン ウィンドウを開くことができます。

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>方法 3. 任意のコマンド ライン ウィンドウで Azure ML CLI を有効にする
任意のコマンド ライン ウィンドウで Azure ML CLI を有効にすることもできます。 その場合は、コマンド ウィンドウを開き、次のコマンドを入力してください。

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
変更を永続的にするには、Windows では `SETX` を使うことができます。 macOS の場合は、`setenv` を使うことができます。

>[!TIP]
>前出の環境変数を設定することにより、好みのターミナル ウィンドウで Azure CLI を有効にできます。

## <a name="step-1-log-in-to-azure"></a>手順 1. Azure にログインする
最初に、AMLWorkbench アプリから CLI を開きます ([ファイル] > [Open Command Prompt]\(コマンド プロンプトを開く\))。 適切な Python 環境が存在していて ML CLI コマンドを利用できることが、これにより確認できます。 

次に、Azure リソースにアクセスして管理するための適切なコンテキストを CLI で設定する必要があります。
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>手順 2. 新しい Azure Machine Learning 実験アカウントとワークスペースを作成する

この手順では、新しい実験アカウントと新しいワークスペースを作成します。 実験アカウントとワークスペースについて詳しくは、「[Azure Machine Learning の概念](overview-general-concepts.md)」をご覧ください。

> [!NOTE]
> 実験アカウントにはストレージ アカウントが必要です。ストレージ アカウントは、実験の実行の出力を格納するために使われます。 ストレージ アカウント名は、関連付けられる URL があるため、Azure 内でグローバルに一意である必要があります。 既存のストレージ アカウントを指定しない場合、実験用のアカウント名を使用して新しいストレージ アカウントを作成します。 必ず一意名を使ってください。一意でないと、"_\<storage_account_name> という名前のストレージ アカウントは既に取得されています_" のようなエラーが発生します。 または、`--storage` 引数を使って既存のストレージ アカウントを使うこともできます。

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>手順 2.a (省略可能) ワークスペースを同僚と共有する
ここでは、ワークスペースへのアクセスを同僚と共有する方法を示します。 実験アカウントでもプロジェクトでも、アクセスを共有する手順は同じです。 Azure リソース ID を取得する方法だけを更新する必要があります。

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> 前述のコマンドの `bob@contoso.com` は、現在のサブスクリプションが属するディレクトリで有効な Azure AD ID である必要があります。

## <a name="step-3-create-a-new-project"></a>手順 3. 新しいプロジェクトを作成する
次に、新しいプロジェクトを作成します。 新しいプロジェクトを始めるにはいくつかの方法があります。

### <a name="create-a-new-blank-project"></a>空のプロジェクトを新しく作成する

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>既定のプロジェクト テンプレートで新しいプロジェクトを作成する
既定のテンプレートで新しいプロジェクトを作成することができます。

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>クラウド Git リポジトリに関連付けられた新しいプロジェクトを作成する
VSTS (Visual Studio Team Service) Git リポジトリに関連付けられた新しいプロジェクトを作成できます。 実験を送信するたびに、プロジェクト フォルダー全体のスナップショットがリモートの Git リポジトリにコミットされます。 詳しくは、「[Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用](using-git-ml-project.md)」をご覧ください。

> [!NOTE]
> Azure Machine Learning は、VSTS で作成された空の Git リポジトリのみをサポートしています。

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> "Repository url might be invalid or user might not have access" (リポジトリの URL が正しくないか、ユーザーにアクセス権がない可能性があります) というエラーが発生する場合は、VSTS でセキュリティ トークンを作成し (_[セキュリティ]_ の _[Add personal access tokens]\(個人用アクセス トークンを追加\)_ メニュー)、プロジェクトを作成するときに `--vststoken` 引数を使うことができます。 

### <a name="sample_create"></a>サンプルから新しいプロジェクトを作成する
この例では、テンプレートとしてサンプル プロジェクトを使用して新しいプロジェクトを作成します。

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
プロジェクトを作成したら、`cd` コマンドを使用してプロジェクト ディレクトリを入力します。

## <a name="step-4-run-the-training-experiment"></a>手順 4. トレーニング実験を実行する 
次の手順では、Iris サンプルのプロジェクトがあるものとします (「[オンライン サンプルから新しいプロジェクトを作成する](#sample_create)」を参照)。

### <a name="prepare-your-environment"></a>環境を準備する 
Iris サンプルでは、matplotlib をインストールする必要があります。

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>実験を送信する

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>正則化項を降順にして実験を繰り返す
少し工夫すれば、異なる正則化項で実験を送信する Python スクリプトと組み合わせることは簡単です  (ファイルを編集して、適切なプロジェクト パスをポイントすることが必要になる場合があります)。

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>手順 5. 実行履歴を表示する
次のコマンドは、以前のすべての実行を一覧表示します。 

```azure-cli
$ az ml history list -o table
```
上のコマンドを実行すると、このプロジェクトに属しているすべての実行の一覧が表示されます。 精度と正則化項のメトリックも表示されます。 これにより、一覧から最善の実行を簡単に識別できます。

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>手順 5.a 特定の実行によって作成された添付ファイルを表示する 
特定の実行に関連付けられている添付ファイルを表示するには、実行履歴の info コマンドを使います。 前述の一覧から、特定の実行の実行 ID を見つけます。

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

実行からアーティファクトをダウンロードするには、次のコマンドを使います。

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>手順 6. 実行のアーティファクトを昇格させる 
相対的に AUC の優れている実行が 1 つあるので、運用環境にデプロイするスコア付け Web サービスを作成する際は、その実行を使うことになります。 そのためには最初に、アーティファクトを資産に昇格させる必要があります。

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

これで、プロジェクト ディレクトリの `assets` フォルダーと `model.pkl.link` ファイルが作成されます。 このリンク ファイルは、プロンプトに表示された資産を参照するために使用されます。

## <a name="step-7-download-the-files-to-be-operationalized"></a>手順 7. 運用可能にするファイルをダウンロードする
昇格させたモデルを使って予測 Web サービスを作成できるように、そのモデルをダウンロードします。 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>手順 8. モデル管理環境を設定する 
Web サービスをデプロイするための環境を作成します。 Docker を使ってローカル コンピューターで Web サービスを実行できます。 または、高スケールの運用には ACS クラスターにデプロイします。 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>手順 9. モデル管理アカウントを作成する 
運用環境にモデルをデプロイして追跡するには、モデル管理アカウントが必要です。 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>手順 10. Web サービスの作成
デプロイしたモデルを使って予測を返す Web サービスを作成します。 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>手順 11. Web サービスを実行する
前の手順で出力された Web サービス ID を使うことで、Web サービスを呼び出してテストします。 

```azure-cli
# Get web service usage information 
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>手順 12. すべてのリソースを削除する 
学習を続ける場合を除き、最後に、作成したすべてのリソースを削除します。 

それらのリソースが属しているリソース グループを削除すれば、そのようにすることができます。 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Machine Learning を使って以下のことを行う方法を学習しました。 
> [!div class="checklist"]
> * 実験アカウントを設定してワークスペースを作成する
> * プロジェクトを作成する
> * 複数のコンピューティング ターゲットに実験を送信する
> * トレーニング済みのモデルを昇格させて登録する
> * モデル管理用のモデル管理アカウントを作成する
> * Web サービスをデプロイするための環境を作成する
> * Web サービスをデプロイして新しいデータでスコア付けする
