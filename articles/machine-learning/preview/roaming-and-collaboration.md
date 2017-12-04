---
title: "Azure Machine Learning Workbench のローミングとコラボレーション | Microsoft Docs"
description: "既知の問題の一覧とトラブルシューティングに役立つガイド"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench のローミングとコラボレーション
このドキュメントでは、Azure Machine Learning Workbench を使用した、複数のコンピューターにわたるプロジェクトのローミングや、他のチーム メンバーとのコラボレーションについて説明します。 

リモート Git リポジトリ (リポジトリ) リンクを使用して Azure Machine Learning プロジェクトを作成すると、プロジェクトのメタデータとスナップショットは、クラウドに保存されます。 クラウドのリンクを使って、別のコンピューターからプロジェクトにアクセス (ローミング) することができます。 また、同僚にアクセスを許可して、コラボレーションすることができます。 

## <a name="prerequisites"></a>前提条件
まず、実験アカウントにアクセス権のある Azure Machine Learning ワークベンチをインストールします。 詳細については、[インストール ガイド](quickstart-installation.md)を参照してください。

次に、[Visual Studio Team System](https://www.visualstudio.com) にアクセスして、プロジェクトをリンクするリポジトリを作成します。 Git の詳細については、[Azure Machine Learning Workbench プロジェクトで Git リポジトリを使用する](using-git-ml-project.md)に関する記事を参照してください。

## <a name="create-a-new-azure-machine-learning-project"></a>新しい Azure Machine Learning プロジェクトを作成する
Azure Machine Learning Workbench を起動し、新しいプロジェクトを作成します (たとえば、_iris_)。 **[Visualstudio.com GIT Repository URL]\(Visualstudio.com GIT リポジトリの URL\)** テキストボックスに有効な VSTS Git リポジトリの URL を入力します。 

> [!IMPORTANT]
> 空のプロジェクト テンプレートを選択する場合は、選択する Git リポジトリに既に _master_ 分岐があっても大丈夫です。 Azure ML は単に _master_ 分岐をローカルに複製し、`aml_config` フォルダーおよびその他のプロジェクトのメタデータ ファイルを、ローカルのプロジェクト フォルダーに追加します。 しかしその他のプロジェクト テンプレートを選択する場合は、Git リポジトリに _master_ 分岐があってはいけません。その場合エラーが表示されます。 代替手段としては、`az ml project create` コマンド ライン ツールを使用して、プロジェクトを作成し、`--force` スイッチを指定します。 これにより、元の master 分岐上のファイルを削除し、選択したテンプレートの新しいファイルに置き換えます。

プロジェクトが作成されたら、プロジェクト内で任意のスクリプトを何回か実行します。 この操作により、リモート Git リポジトリの実行履歴の分岐に、プロジェクトの状態がコミットされます。 

> [!NOTE] 
> 実行履歴の分岐へのコミットをトリガーするのは、スクリプトの実行だけです。 データ準備の実行や Notebook の実行では、実行履歴の分岐でのプロジェクト スナップショットはトリガーされません。

Git の認証を設定した場合も、明示的にマスター分岐で操作したり、新しい分岐を作成できます。 

例 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>ローミング
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Azure Machine Learning Workbench を 2 番目のコンピューターで開く
VSTS Git リポジトリがプロジェクトにリンクされると、Azure Machine Learning ワークベンチがインストールされている任意のコンピューターから _iris_ プロジェクトにアクセスできるようになります。 

別のコンピューターにある iris プロジェクトにアクセスするには、プロジェクトの作成中に使用されたものと同じ資格情報で、アプリにログインする必要があります。 さらに、同じ実験アカウントとワークスペースに移動する必要があります。 _iris_ プロジェクトは、ワークスペース内で他のプロジェクトと共にアルファベット順に一覧表示されます。 

### <a name="download-project-on-second-machine"></a>2 番目のコンピューターにプロジェクトをダウンロードします。
2 番目のコンピューターでワークスペースを開くと、一般的なフォルダーのアイコンとは異なるアイコンが _iris_ プロジェクトの横に表示されます。 ダウンロード アイコンは、プロジェクトのコンテンツがクラウド上にあり、現在のコンピューターにダウンロードする必要があることを示します。 

![プロジェクトの作成](./media/roaming-and-collaboration/downloadable-project.png)

_iris_ プロジェクトをクリックすると、ダウンロード操作が開始されます。 ダウンロードが完了するまで少し待つと、2 番目のコンピューターのプロジェクトにアクセスできるようになります。 

Windows では `C:\Users\<username>\Documents\AzureML` にダウンロードされます。

macOS では、`/home/<username>/Documents/AzureML` にダウンロードされます。

今後のリリースで、ダウンロード先のフォルダーを選択できるように機能を拡張する予定です。 

> [!NOTE]
> プロジェクトとまったく同じ名前のフォルダーが Azure ML ディレクトリ内にあると、ダウンロードが失敗します。 機能が拡張されるまで、この問題を回避するには、既存のフォルダーの名前を変更する必要があります。


### <a name="work-on-the-downloaded-project"></a>ダウンロードしたプロジェクトでの作業 
新しくダウンロードしたプロジェクトには、プロジェクトの前回の実行時点でプロジェクトの状態が反映されます。 プロジェクトの状態のスナップショットは、実行を送信するたびに VSTS Git リポジトリの実行履歴分岐に対し自動的にコミットされます。 2 番目のコンピューターでプロジェクトをインスタンス化するときに、最新の実行に関連付けられているスナップショットを使用します。 
 

## <a name="collaboration"></a>コラボレーション
VSTS の Git リポジトリにリンクされているプロジェクトの他のチーム メンバーとコラボレーションできます。 実験アカウント、ワークスペース、およびプロジェクトのユーザーに権限を割り当てることができます。 この時点で、Azure CLI を使用して Azure Resource Manager のコマンドを実行できます。 [Azure ポータル](https://portal.azure.com)を使用することもできます。 [次のセクション](#portal)を参照してください。    

### <a name="using-command-line-to-add-users"></a>コマンドラインを使用してユーザーを追加する
例を見てましょう。 たとえば、e_Iris_ プロジェクトの所有者であるアリスは、ボブとアクセスを共有する必要があります。 

アリスは、**[ファイル]** メニューをクリックし、**[コマンド プロンプト]** メニュー項目を選択して、_iris_ プロジェクトに構成されているコマンド プロンプトを起動します。 アリスは、次のコマンドを実行して、ボブに付与するレベルのアクセス権限を決定できます。  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

ロールの割り当て後に、直接または継承により、ボブは Workbench プロジェクトの一覧で、プロジェクトを参照できます。 プロジェクトを表示するために、アプリケーションを再起動する必要がある場合があります。 ボブは、[ローミング セクション](#roaming)に説明されているとおり、プロジェクトをダウンロードしてアリスとコラボレーションできます。 

プロジェクトでコラボレーションを行うすべてのユーザーの実行履歴は、同じリモート Git リポジトリにコミットされます。 アリスが実行すると、ボブは、それを Workbench アプリのプロジェクトの実行履歴セクションで確認できます。 ボブは、アリスによって開始された実行を含む、任意の実行状態にプロジェクトを復元することもできます。 

プロジェクトのリモート Git リポジトリを共有することにより、アリスとボブは、マスター分岐でもコラボレーションできます。 必要な場合は、個人用分岐を作成して Git プル要求や結合を使用してコラボレーションすることもできます。 

### <a name="using-azure-portal-to-add-users"></a>Azure ポータルを使用してユーザーを追加する
<a name="portal"></a>

Azure Machine Learning の実験アカウント、ワークスペース、およびプロジェクトは、Azure Resource Manager のリソースです。 [Azure ポータル](https://portal.azure.com)では、[アクセス制御] リンクを使用して、ロールを割り当てることができます。 

[All Resources]\(すべてのリソース\) ビューから、ユーザーを追加するリソースを特定します。 ページ内のアクセス制御 (IAM) リンクをクリックします。 Add users 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>コラボレーションのワークフローの例
コラボレーションのフローを、例を使って説明します。 Contoso の従業員である Alice と Bob は、Azure ML Workbench を使ってデータ サイエンス プロジェクトでコラボレーションを行うことを考えています。 2 人の ID は、同じ Contoso Azure AD テナントに属しています。

1. 最初に、Alice が、VSTS プロジェクトで空の Git リポジトリを作成します。 この VSTS プロジェクトは、Contoso AAD テナントで作成された Azure サブスクリプション内に存在する必要があります。 

2. その後、Alice は、Azure ML の実験アカウント、ワークスペース、および Azure ML Workbench プロジェクトを、自分のコンピューターで作成します。 プロジェクトを作成するとき、彼女は Git リポジトリの URL を指定します。

3. Alice は、プロジェクトでの作業を始めます。 彼女は、スクリプトをいくつか作成して何回か実行します。 実行のたびに、プロジェクト フォルダー全体のスナップショットが、Workbench によって作成された VSTS Git リポジトリの実行履歴の分岐に、コミットとして自動的にプッシュされます。

4. Alice は作業の進行に満足しています。 彼女は、変更をローカルな _master_ 分岐にコミットし、それを VSTS Git リポジトリの _master_ 分岐にプッシュしようと考えます。 そのために、彼女はプロジェクトを開き、Azure ML Workbench からコマンド プロンプト ウィンドウを起動して、次のコマンドを実行します。
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. その後、Alice は共同作成者として Bob をワークスペースに追加します。 彼女はこれを、Azure Portal から、または上で示した `az role assignment` コマンドを使って、行うことができます。 また彼女は、VSTS Git リポジトリへの読み取り/書き込みアクセス権を Bob に与えます。

6. Bob は、自分のコンピューターで Azure ML Workbench にログインします。 彼は、Alice から共有を受けているワークスペースと、そのワークスペースに表示されたプロジェクトを見ることができます。 

7. Bob がプロジェクト名をクリックすると、プロジェクトが彼のコンピューターにダウンロードされます。
    
    a. ダウンロードされるプロジェクト ファイルは、実行履歴に記録された最新の実行のスナップショットの複製です。 master 分岐に対する最新のコミットではありません。
    
    b. ローカルのプロジェクト フォルダーは、ステージングされていない変更を含む _master_ 分岐に設定されています。

8. Bob は、Alice によって行われた実行を参照し、以前の任意の実行のスナップショットを復元できるようになります。

9. Bob は、Alice によってプッシュされた最新の変更を取得し、別の分岐で作業を始めようと考えます。 そこで、彼は Azure ML Workbench からコマンド プロンプト ウィンドウを開き、次のコマンドを実行します。

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob はプロジェクトを変更し、新しい実行を送信します。 _bob_ 分岐で変更が行われます。 Bob の実行が、Alice からも見えるようになります。

11. Bob は、自分の変更をリモート Git リポジトリにプッシュする準備ができました。 Alice が作業している _master_ 分岐との競合を回避するため、彼は自分の作業をやはり _bob_ という名前の新しいリモート分岐にプッシュすることにします。

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob は自分のコードでの新しいすばらしい手法について Alice に伝え、_bob_ 分岐から _master_ 分岐へのプル要求をリモート Git リポジトリに作成します。 その後、Alice はプル要求を _master_ 分岐にマージできます。

## <a name="next-steps"></a>次のステップ
Azure ML Workbench での Git の使用について詳しくは、「[Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用](using-git-ml-project.md)」をご覧ください。