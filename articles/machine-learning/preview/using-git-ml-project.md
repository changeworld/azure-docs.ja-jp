---
title: "Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用 | Microsoft Docs"
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
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用
このドキュメントでは、Azure Machine Learning Workbench で Git を使用して、バージョン管理を提供し、データ サイエンス実験での再現性を確保する方法について説明します。 プロジェクトをクラウド Git リポジトリに関連付ける方法も示します。

## <a name="introduction"></a>はじめに
Azure Machine Learning Workbench は Git 統合で新規に設計されています。 新しいプロジェクトを作成する場合、プロジェクト フォルダーは、ローカルの Git リポジトリとして自動的に "Git 初期化" されます。 一方で、2 番目の隠しローカル Git リポジトリが _AzureMLHistory/<project_GUID >_ という名前のブランチとともに作成され、実行のたびにプロジェクト フォルダーの変更を追跡します。 

Azure ML プロジェクトを Visual Studio Team Service (VSTS) プロジェクト内にホストされている Git リポジトリに関連付けると、ローカルとリモートの両方で自動バージョン コントロールが有効になります。 このアソシエーションにより、リモート リポジトリへのアクセス権があれば、だれでも別のコンピューター (ローミング) に最新のソース コードをダウンロードできます。  

> [!NOTE]
> VSTS には、Azure Machine Learning 実験サービスから独立した固有のアクセス制御リストがあります。 ユーザー アクセスは、Git リポジトリと Azure ML ワークスペースまたはプロジェクトとで異なる場合があり、管理が必要な場合があります。 このため、単なるワークスペースの共有に加えて、コード レベルのアクセスを含め、Azure ML プロジェクトをチーム メンバーと共有する場合は、VSTS Git リポジトリに対する適切なアクセス権を明示的に付与する必要があります。 

Git では、_master_ ブランチを使用するかリポジトリに他のブランチを作成することで、バージョン コントロールを明示的に管理することもできます。 ローカル Git リポジトリを使用することができ、プロビジョニングされている場合はリモート Git リポジトリにプッシュすることもできます。

この図は、VSTS Git リポジトリと Azure ML プロジェクト間のリレーションシップを示しています。

![AML Git](media/using-git-ml-project/aml_git.png)

リモート Git リポジトリの使用を開始するには、次の基本的な手順に従います。

> [!NOTE]
> 現時点では、Azure Machine Learning では VSTS アカウントでのみ Git リポジトリがサポートされます。 (GitHub などの) 一般的な Git リポジトリのサポートは今後計画されています。

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>手順 1. Azure Machine Learning 実験アカウントを作成する
まだ行っていない場合は、Azure Machine Learning 実験アカウントを作成し、Azure ML Workbench アプリをインストールします。 詳細については、[インストールと作成のクイックスタート](quickstart-installation.md)に関する記事をご覧ください。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>手順 2. チーム プロジェクトを作成するか既存のチーム プロジェクトを使用する
[Azure Portal](https://portal.azure.com/) で、新しい**チーム プロジェクト**を作成します。
1. **[+]** をクリックします
2. **"チーム プロジェクト"** を検索します
3. 必要な情報を入力します。
    - 名前: チーム名。
    - バージョン コントロール: **Git**
    - サブスクリプション: Azure Machine Learning 実験アカウントがあるサブスクリプション。
    - 場所: Azure Machine Learning 実験リソースの近くのリージョン内が理想です。
4. **Create** をクリックしてください。 

![Azure Portal でチーム プロジェクトを作成する](media/using-git-ml-project/create_vsts_team.png)

Azure Machine Learning Workbench へのアクセスに使用しているのと同じ Azure Active Directory (AAD) アカウントでサインインしてください。 そうしないと、コマンドラインを使用して Azure ML プロジェクトを作成し、Git リポジトリにアクセスするための個人用アクセス トークンを提供しない限り、システムは AAD の資格情報を使用してアクセスできません。 詳細については、後述します。

チーム プロジェクトが作成されたら、次の手順に進む準備ができます。

先ほど作成したチーム プロジェクトに直接移動するための URL は `https://<team_project_name>.visualstudio.com` です。

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>手順 3. リモート Git リポジトリを持つ新しい Azure ML プロジェクトを作成する
Azure ML Workbench を起動し、新しいプロジェクトを作成します。 Git リポジトリのテキスト ボックスに、手順 2. から取得した VSTS Git リポジトリの URL を入力します。 通常、次のようになります。`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Git リポジトリを持つ Azure ML プロジェクトを作成する](media/using-git-ml-project/create_project_with_git_rep.png)

コマンド ライン ツールを使用してプロジェクトを作成することもできます。 個人用アクセス トークンを指定するオプションがあります。 Azure ML は、このトークンを使用して、AAD 資格情報に依存するのではなく、ユーザーの代わりに Git リポジトリにアクセスできます。

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> 空のプロジェクト テンプレートを選択する場合は、選択する Git リポジトリに既に _master_ ブランチがあっても大丈夫です。 Azure ML は単に _master_ ブランチをローカルに複製し、`aml_config` フォルダーおよびその他のプロジェクトのメタデータ ファイルを、ローカルのプロジェクト フォルダーに追加します。 しかしその他のプロジェクト テンプレートを選択する場合は、Git リポジトリに _master_ ブランチがあってはいけません。その場合エラーが表示されます。 代替手段としては、`az ml project create` コマンド ライン ツールを使用して、プロジェクトを作成し、`--force` スイッチを指定します。 これにより、元の master ブランチ上のファイルを削除し、選択したテンプレートの新しいファイルに置き換えます。

リモート Git リポジトリの統合が有効ですぐに使用できる新しい Azure ML プロジェクトが作成されます。 プロジェクト フォルダーは、常にローカル Git リポジトリとして Git 初期化されます。 Git _remote_ はリモート VSTS Git リポジトリに設定されているため、コミットはリモート Git リポジトリにプッシュできます。

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>手順 3a 既存の Azure ML プロジェクトを VSTS Git リポジトリに関連付ける
必要に応じて、VSTS Git リポジトリのない Azure ML プロジェクトを作成し、実行履歴のスナップショットにローカル Git リポジトリを使用することもできます。 また、次のコマンドを使用して、後でこの既存の Azure ML プロジェクトに VSTS Git リポジトリを関連付けることができます。

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> 関連付けられている Git リポジトリがない Azure ML プロジェクトで、update-repo 操作の実行のみができます。 いったん Git リポジトリが関連付けられると、削除できません。

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>手順 4. Git リポジトリ内のプロジェクト スナップショットをキャプチャする
これで、プロジェクトでいくつかのスクリプトを実行し、実行と実行の間にいくつか変更を行うことができます。 これはデスクトップ アプリから、または `az ml experiment submit` コマンドを使用して CLI から行うことができます。 詳細については、[アイリスの分類に関するチュートリアル](tutorial-classifying-iris-part-1.md)の記事をご覧ください。 実行のたびに、プロジェクト フォルダー内のファイルに加えた変更がある場合は、プロジェクト フォルダー全体のスナップショットがコミットされ、`AzureMLHistory/<Project_GUID>` という名前のブランチ下のリモート Git リポジトリにプッシュされます。 ブランチとコミットを表示するには、VSTS Git リポジトリの URL を参照しこのブランチを見つけます。 

> [!NOTE] 
> スナップショットはスクリプトの実行前にのみコミットされます。 現在、データ準備の実行または Notebook セル実行は、スナップショットをトリガーしません。

![実行履歴ブランチ](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Git コマンドを使用して自分で履歴ブランチで操作しないことをお勧めします。 そうしてしまうと、実行履歴が乱雑になる可能性があります。 ご自身での Git 操作には、master ブランチを使用するか、代わりに他のブランチを作成します。

## <a name="step-5-restore-a-previous-project-snapshot"></a>手順 5. 以前のプロジェクト スナップショットを復元する 
プロジェクト フォルダー全体を以前の実行履歴プロジェクト状態スナップショットの状態に復元するには、Azure ML Workbench で次の操作を行います。
1. アクティビティ バー (時計のアイコン) で **[実行]** をクリックします。
2. **[実行の一覧]** ビューで、復元する実行をクリックします。
3. **[Run Detail]\(実行の詳細\)** ビューで、**[復元]** をクリックします。

![実行履歴ブランチ](media/using-git-ml-project/restore_project.png)

または、Azure ML Workbench CLI ウィンドウから次のコマンドを使用することもできます。

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

このコマンドを実行すると、プロジェクト フォルダー全体が、その特定の実行の開始時に取得されたスナップショットで上書きされます。 しかしプロジェクトは現在のブランチ上にとどまります。 これは、現在のプロジェクト フォルダー内の**すべての変更が失われる**ことを意味します。 したがって、このコマンドを実行する場合は特に注意してください。 上記の操作を実行する前に、Git で現在のブランチに変更をコミットすることもあります。 詳細については、前述の箇所を参照してください。

## <a name="step-6-use-the-master-branch"></a>手順 6. master ブランチを使用する
現在のプロジェクト状態が誤って失われないようにする 1 つの方法は、プロジェクトを Git リポジトリの master ブランチ (または自分で作成した他のブランチ) にコミットすることです。 コマンド ライン (または他の使い慣れた Git クライアント ツール) から Git を直接使用して、master ブランチを操作できます。 For example:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

これで、master ブランチで行ったコミットにいつでも戻れるため、手順 5. に従ってプロジェクトを以前のスナップショットに安全に復元できます。

## <a name="authentication"></a>認証
プロジェクトのスナップショットの取得と復元を Azure ML の実行履歴機能にのみ依存している場合は、Git リポジトリの認証について心配する必要はありません。 これは実験サービス レイヤーで処理されます。

一方、独自の Git ツールを使用してバージョン コントロールを管理する場合は、VSTS 上のリモート Git リポジトリに対する認証を正しく処理する必要があります。 Azure ML では、リモート Git リポジトリは、HTTPS プロトコルを使用して Git リモートとしてローカル リポジトリに追加されます。 これは、Git コマンドをリモートに発行する場合 (プッシュまたはプルなど)、ユーザー名、およびパスワードまたは個人用アクセス トークンを指定する必要があることを意味します。 [これらの手順](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)に従って、VSTS Git リポジトリ内の個人用アクセス トークンを作成します。

## <a name="next-steps"></a>次のステップ
Team Data Science Process を使用してプロジェクト構造を整理する方法については、[TDSP 使用したプロジェクトの体系化](how-to-use-tdsp-in-azure-ml.md)に関する記事をご覧ください
