---
title: "Azure Machine Learning Workbench プロジェクトで Git リポジトリを使用する | Microsoft Docs"
description: "この記事では、Azure Machine Learning Workbench プロジェクトと組み合わせて Git リポジトリを使用する方法について説明します。"
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: f4f1112fe68bdb2a26f68b3da08fe97f9d22d3b7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Machine Learning Workbench プロジェクトで Git リポジトリを使用する
Azure Machine Learning Workbench で Git を使って、バージョン コントロールを提供し、データ サイエンス実験での再現性を確保する方法について説明します。 プロジェクトをクラウド Git リポジトリに関連付ける方法も示します。

Machine Learning Workbench は、Git 統合対応に設計されています。 新しいプロジェクトを作成する場合、プロジェクト フォルダーは、ローカルの Git リポジトリとして自動的に "Git 初期化" されます。 AzureMLHistory/\<プロジェクト GUID\> というブランチ名で、第 2 の非表示のローカル Git リポジトリも作成されます。 このブランチは、各実行に対するプロジェクト フォルダーの変更を追跡します。 

Azure Machine Learning プロジェクトを Git リポジトリと関連付けると、ローカルおよびリモートでの自動バージョン コントロールが有効になります。 Git リポジトリは、Visual Studio Team Services (Team Services) でホストされます。 Machine Learning プロジェクトは Git リポジトリと関連付けられているため、リモート リポジトリへのアクセス権を持つすべてのユーザーが、最新のソース コードを別のコンピューターにダウンロードできます (ローミング)。  

> [!NOTE]
> Team Services には、Azure Machine Learning 実験サービスから独立した固有のアクセス制御リスト (ACL) があります。 Git リポジトリと Machine Learning ワークスペースまたはプロジェクトの間では、ユーザーのアクセスが異なる場合があります。 アクセスの管理が必要になる場合があります。 
> 
> チーム メンバーに、Machine Learning プロジェクトへのコード レベルのアクセスを許可するか、またはワークスペースの共有だけにするかに関係なく、Team Services Git リポジトリにアクセスするための適切なアクセス許可をユーザーに付与する必要があります。 

Git でバージョン コントロールを管理するには、master ブランチを使うか、またはリポジトリに他のブランチを作成することができます。 また、ローカル Git リポジトリを使い、リモート Git リポジトリがプロビジョニングされている場合はそこにプッシュすることもできます。

次の図では、Team Services Git リポジトリと Machine Learning プロジェクトの間の関係を示します。

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

リモート Git リポジトリを使い始めるには、次のセクションで説明する手順を実行します。

> [!NOTE]
> 現在、Azure Machine Learning では Team Services アカウントの Git リポジトリだけがサポートされています。 GitHub のような一般的な Git リポジトリのサポートは、今後提供される予定です。

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>手順 1. Machine Learning 実験アカウントを作成する
Machine Learning 実験アカウントを作成し、Azure Machine Learning Workbench アプリをインストールします。 詳しくは、[インストールと作成のクイックスタート](quickstart-installation.md)に関するページをご覧ください。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>手順 2. チーム プロジェクトを作成するか、既存のチーム プロジェクトを使う
[Azure Portal](https://portal.azure.com/) で、新しいチーム プロジェクトを作成します。
1. **[+]** を選びます。
2. "**チーム プロジェクト**" を検索します。
3. 必要な情報を入力します。
    - **[名前]**: チーム名。
    - **[Version Control]\(バージョン コントロール\)**: **[Git]** を選びます。
    - **[サブスクリプション]**: Machine Learning 実験アカウントのあるサブスクリプションを選びます。
    - **[場所]**: できる限り、Machine Learning 実験リソースに近いリージョンを選びます。
4. **[作成]**を選択します。 

![Azure Portal でチーム プロジェクトを作成する](media/using-git-ml-project/create_vsts_team.png)

Machine Learning Workbench へのアクセスに使うのと同じ Azure Active Directory (Azure AD) アカウントを使って、サインインしてください。 そうしないと、システムは Azure AD の資格情報を使って Machine Learning Workbench にアクセスできません。 例外は、コマンド ラインを使って Machine Learning プロジェクトを作成する場合であり、Git リポジトリにアクセスするための個人用アクセス トークンを指定します。 これについては、記事の後半で詳しく説明します。

作成したチーム プロジェクトに直接移動するには、https://\<チーム プロジェクト名\>.visualstudio.com という URL を使います。

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>手順 3. Machine Learning プロジェクトと Git リポジトリをセットアップする

Machine Learning プロジェクトを設定するには、次の 2 つのオプションがあります。
- リモート Git リポジトリを備えた Machine Learning プロジェクトを作成する
- 既存の Machine Learning プロジェクトを Team Services の Git リポジトリと関連付ける

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>リモート Git リポジトリを備えた Machine Learning プロジェクトを作成する
Machine Learning Workbench を開き、新しいプロジェクトを作成します。 **Git リポジトリ**のボックスに、手順 2 で作成した Team Services の Git リポジトリの URL を入力します。 通常は、https://\<Team Services アカウント名\>.visualstudio.com/_git/\<プロジェクト名\> のようになります

![Git リポジトリを備えた Machine Learning プロジェクトを作成する](media/using-git-ml-project/create_project_with_git_rep.png)

Azure コマンド ライン ツール (Azure CLI) を使ってプロジェクトを作成することもできます。 個人用アクセス トークンを入力できます。 Machine Learning は、Azure AD 資格情報の代わりにこのトークンを使って、Git リポジトリにアクセスできます。

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> 空のプロジェクト テンプレートを選んだ場合、選んだ Git リポジトリに既に master ブランチがある場合があります。 Machine Learning は、単に master ブランチをローカルに複製します。 そして、aml_config フォルダーと他のプロジェクト メタデータ ファイルを、ローカルのプロジェクト フォルダーに追加します。 
>
> 他のプロジェクト テンプレートを選ぶ場合は、Git リポジトリに master ブランチが既に存在していては "*なりません*"。 存在していると、エラーが表示されます。 代替手段としては、`az ml project create` コマンドを使い、`--force` スイッチを指定して、プロジェクトを作成します。 これにより、元の master ブランチ上のファイルが削除されて、選んだテンプレートの新しいファイルに置き換わります。

リモート Git リポジトリとの統合が有効になった新しい Machine Learning プロジェクトが作成されます。 プロジェクト フォルダーは、常にローカル Git リポジトリとして Git 初期化されます。 Git リモートはリモート Team Services Git リポジトリに設定されるため、リモート Git リポジトリにコミットをプッシュできます。

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>既存の Machine Learning プロジェクトを Team Services の Git リポジトリと関連付ける
Team Services Git リポジトリを持たない Machine Learning プロジェクトを作成し、実行履歴のスナップショットにローカル Git リポジトリを使うこともできます。 後で、次のコマンドを使って、Team Services Git リポジトリをこの既存の Machine Learning プロジェクトと関連付けることができます。

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Git リポジトリが関連付けられていない Machine Learning プロジェクトでは、リポジトリ更新操作だけを実行できます。 また、Git リポジトリを Machine Learning と関連付けた後では、リポジトリを削除できません。

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>手順 4. Git リポジトリ内のプロジェクト スナップショットをキャプチャする
プロジェクトでいくつかのスクリプトを実行し、実行と実行の間にいくつか変更を行うことができます。 これはデスクトップ アプリで、または `az ml experiment submit` コマンドを使って Azure CLI から、行うことができます。 詳しくは、[あやめの分類のチュートリアル](tutorial-classifying-iris-part-1.md)をご覧ください。 実行のたびに、プロジェクト フォルダー内のファイルに行われた変更がある場合は、プロジェクト フォルダー全体のスナップショットがコミットされ、AzureMLHistory/\<プロジェクト GUID\> という名前のブランチのリモート Git リポジトリにプッシュされます。 AzureMLHistory/\<プロジェクト GUID\> ブランチを含むブランチとコミットを表示するには、Team Services Git リポジトリの URL に移動します。 

> [!NOTE] 
> スナップショットは、スクリプト実行の前にのみコミットされます。 現在は、データ準備の実行または Notebook セルの実行では、スナップショットはトリガーされません。

![実行履歴のブランチ](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Git コマンドを使って履歴のブランチで操作しないことをお勧めします。 実行履歴と干渉する可能性があります。 代わりに、master ブランチを使うか、自分だけの Git 操作用に他のブランチを作成します。

## <a name="step-5-restore-a-previous-project-snapshot"></a>手順 5. 以前のプロジェクト スナップショットを復元する 
プロジェクト フォルダー全体を以前の実行履歴スナップショットの状態に復元するには、Machine Learning Workbench で次の操作を行います。
1. アクティビティ バー (砂時計のアイコン) で、**[実行]** を選びます。
2. **[実行の一覧]** ビューで、復元する実行を選びます。
3. **[実行の詳細]** ビューで、**[復元]** を選びます。

![実行履歴のブランチ](media/using-git-ml-project/restore_project.png)

必要に応じて、Machine Learning Workbench の Azure CLI ウィンドウで、次のコマンドを使うことができます。

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

このコマンドを実行するときは注意してください。 このコマンドを実行すると、プロジェクト フォルダー全体が、その特定の実行の開始時に取得されたスナップショットで上書きされます。 プロジェクトは現在のブランチにとどまります。 つまり、現在のプロジェクト フォルダー内の**すべての変更が失われます**。  

この操作を実行する前に、Git を使って現在のブランチに変更をコミットする必要があります。

## <a name="step-6-use-the-master-branch"></a>手順 6. master ブランチを使用する
現在のプロジェクト状態が誤って失われないようにする 1 つの方法は、プロジェクトを Git リポジトリの master ブランチ (または自分で作成した他のブランチ) にコミットすることです。 コマンド ラインまたは他の使い慣れた Git クライアント ツールから Git を使って、master ブランチを操作できます。 For example:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

これで、手順 5 を実行して以前のスナップショットにプロジェクトを安全に復元できます。 master ブランチで直前に行ったコミットにいつでも戻ることができます。

## <a name="authentication"></a>認証
プロジェクトのスナップショットの取得と復元を Machine Learning の実行履歴機能にのみ依存している場合は、Git リポジトリの認証について心配する必要はありません。 認証は、Machine Learning 実験サービス レイヤーで処理されます。

一方、独自の Git ツールを使ってバージョン コントロールを管理する場合は、Team Services のリモート Git リポジトリに対する認証を処理する必要があります。 Machine Learning では、リモート Git リポジトリは、HTTPS プロトコルを使って Git リモートとしてローカル リポジトリに追加されます。 これは、Git コマンドをリモートに発行する場合 (プッシュまたはプルなど)、ユーザー名、およびパスワードまたは個人用アクセス トークンを指定する必要があることを意味します。 Team Services Git リポジトリで個人用アクセス トークンを作成する方法については、[認証への個人用アクセス トークンの使用](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
- [Team Data Science Process を使ってプロジェクトの構造を整理する](how-to-use-tdsp-in-azure-ml.md)方法について学習してください。
