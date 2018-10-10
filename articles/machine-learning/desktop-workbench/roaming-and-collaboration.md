---
title: Azure Machine Learning Workbench のローミングとコラボレーション | Microsoft Docs
description: Azure Machine Learning Workbench でのローミングとコラボレーションをセットアップする方法を説明します。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 07a9d46dff17b43d01a6b411292cf240c32476f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983720"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench のローミングとコラボレーション

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

この記事では、Azure Machine Learning Workbench を使って、コンピューター間のローミングやチーム メンバーとのコラボレーション用にプロジェクトを設定する方法について説明します。 

リモート Git リポジトリ (リポジトリ) とのリンクがある Azure Machine Learning プロジェクトを作成すると、プロジェクトのメタデータとスナップショットがクラウドに保存されます。 クラウドのリンクを使って、別のコンピューターからプロジェクトにアクセス (ローミング) することができます。 また、チーム メンバーにプロジェクトへのアクセスを許可して共同作業することもできます。 

## <a name="prerequisites"></a>前提条件
1. Machine Learning Workbench アプリをインストールします。 Azure Machine Learning 実験アカウントにアクセスできることを確認します。 詳しくは、[インストール ガイド](quickstart-installation.md)をご覧ください。

2. [Azure DevOps](https://www.visualstudio.com) にアクセスし、プロジェクトをリンクするリポジトリを作成します。 詳しくは、「[Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用](using-git-ml-project.md)」をご覧ください。

## <a name="create-a-new-machine-learning-project"></a>新しい Machine Learning プロジェクトを作成する
Azure Machine Learning Workbench を開き、新しいプロジェクトを作成します (たとえば、iris という名前のプロジェクト)。 **[Visualstudio.com GIT Repository URL]\(Visualstudio.com GIT リポジトリの URL\)** ボックスに、Azure DevOps Git リポジトリの有効な URL を入力します。 

> [!IMPORTANT]
> 空のプロジェクト テンプレートを選んだ場合、選んだ Git リポジトリに既に master ブランチがある場合があります。 Machine Learning は、単に master ブランチをローカルに複製します。 そして、aml_config フォルダーと他のプロジェクト メタデータ ファイルを、ローカルのプロジェクト フォルダーに追加します。 
>
> 他のプロジェクト テンプレートを選ぶ場合は、Git リポジトリに master ブランチが既に存在していては "*なりません*"。 存在していると、エラーが表示されます。 代替手段としては、`az ml project create` コマンドを使い、`--force` スイッチを指定して、プロジェクトを作成します。 これにより、元の master ブランチ上のファイルが削除されて、選んだテンプレートの新しいファイルに置き換わります。

プロジェクトを作成した後、プロジェクト内で任意のスクリプトを何回か実行します。 この操作により、リモート Git リポジトリの実行履歴のブランチに、プロジェクトの状態がコミットされます。 

> [!NOTE] 
> 実行履歴の分岐へのコミットをトリガーするのは、スクリプトの実行だけです。 データ準備の実行や Notebook の実行では、実行履歴のブランチでのプロジェクト スナップショットはトリガーされません。

Git の認証を設定した場合も、master ブランチで操作することができます。 または、新しいブランチを作成できます。 

例: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>ローミング
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Machine Learning Workbench を 2 番目のコンピューターで開く
Azure DevOps Git リポジトリがプロジェクトにリンクされると、Machine Learning Workbench がインストールされている任意のコンピューターから iris プロジェクトにアクセスできるようになります。 

別のコンピューターにある iris プロジェクトにアクセスするには、プロジェクトの作成に使ったものと同じ資格情報を使って、アプリにサインインする必要があります。 また、Machine Learning 実験アカウントとワークスペースも同じである必要があります。 iris プロジェクトは、ワークスペース内の他のプロジェクトと共にアルファベット順に一覧表示されます。 

### <a name="download-the-project-on-a-second-computer"></a>2 番目のコンピューターでプロジェクトをダウンロードする
2 番目のコンピューターでワークスペースを開くと、一般的なフォルダーのアイコンとは異なるアイコンが iris プロジェクトの横に表示されます。 ダウンロード アイコンは、プロジェクトのコンテンツがクラウドにあり、現在のコンピューターにプロジェクトをダウンロードする準備ができていることを示します。 

![Create project](./media/roaming-and-collaboration/downloadable-project.png)

iris プロジェクトを選んでダウンロードを始めます。 ダウンロードが完了すると、2 番目のコンピューターのプロジェクトにアクセスできるようになります。 

Windows では、プロジェクトは C:\Users\\<ユーザー名\>\Documents\AzureML にあります。

macOS では、プロジェクトは /home/\<ユーザー名\>/Documents/AzureML にあります。

今後のリリースでは、ダウンロード先のフォルダーを選ぶことができるように機能を拡張する予定です。 

> [!NOTE]
> プロジェクトとまったく同じ名前のフォルダーが Machine Learning ディレクトリ内にあると、ダウンロードが失敗します。 この問題を回避するには、既存のフォルダーの名前を一時的に変更します。


### <a name="work-on-the-downloaded-project"></a>ダウンロードしたプロジェクトでの作業 
新しくダウンロードしたプロジェクトには、プロジェクトを最後に実行したときのプロジェクトの状態が反映されています。 プロジェクトの状態のスナップショットは、実行を送信するたびに Azure DevOps Git リポジトリの実行履歴分岐に対し自動的にコミットされます。 最新の実行に関連付けられているスナップショットを使って、2 番目のコンピューターのプロジェクトがインスタンス化されます。 
 

## <a name="collaboration"></a>コラボレーション
Azure DevOps Git リポジトリにリンクされているプロジェクトを使って、チーム メンバーとコラボレーションできます。 Machine Learning 実験アカウント、ワークスペース、プロジェクトに対するアクセス許可を、ユーザーに割り当てることができます。 この段階で、Azure CLI を使って Azure Resource Manager のコマンドを実行できます。 [Azure Portal](https://portal.azure.com) を使用することもできます。 詳しくは、「[Azure Portal を使ってユーザーを追加する](#portal)」をご覧ください。    

### <a name="use-the-command-line-to-add-users"></a>コマンド ラインを使ってユーザーを追加する
例として、Alice は iris プロジェクトの所有者であるものとします。 Alice は、プロジェクトへのアクセスを Bob と共有することを望んでいます。 

Alice は、**[ファイル]** メニューの **[コマンド プロンプト]** メニュー項目を選びます。 iris プロジェクトでコマンド プロンプト ウィンドウが開きます。 Alice は、Bob に与えるアクセス レベルを決定できます。 彼女は、次のコマンドを実行してアクセス許可を付与します。  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

直接または継承によりロールを割り当てた後、Bob は Machine Learning Workbench のプロジェクト一覧でプロジェクトを見ることができるようになります。 Bob は、プロジェクトを表示するには、アプリケーションの再起動が必要な場合があります。 Bob は、「[ローミング](#roaming)」で説明されているようにしてプロジェクトをダウンロードし、Alice とのコラボレーションを始めることができます。きます。 

プロジェクトでコラボレーションしているすべてのユーザーの実行履歴は、同じリモート Git リポジトリにコミットされます。 Alice が行った実行を、Bob は、Machine Learning Workbench アプリのプロジェクトの実行履歴セクションで確認できます。 Bob は、Alice が開始した実行も含めて、任意の実行状態にプロジェクトを復元することもできます。 

プロジェクトのリモート Git リポジトリを共有することにより、Alice と Bob は master ブランチでもコラボレーションできます。 必要な場合は、個人用ブランチを作成し、Git のプル要求や結合を使ってコラボレーションすることもできます。 

### <a name="use-the-azure-portal-to-add-users"></a>Azure Portal を使ってユーザーを追加する
<a name="portal"></a>

Machine Learning の実験アカウント、ワークスペース、およびプロジェクトは、Azure Resource Manager のリソースです。 ロールの割り当ては、[Azure Portal](https://portal.azure.com) の **[アクセス制御]** リンクを使って行うことができます。 

ユーザーを追加するリソースを探すには、**[すべてのリソース]** ビューを使います。 **[アクセス制御 (IAM)]** リンクを選び、**[ユーザーの追加]** を選びます。 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>コラボレーションのワークフローの例
コラボレーションのワークフローを、例を使って説明します。 Contoso の従業員である Alice と Bob は、Machine Learning Workbench を使ってデータ サイエンス プロジェクトでコラボレーションを行うことを考えています。 2 人の ID は、同じ Contoso Azure Active Directory (Azure AD) テナントに属しています。 Alice と Bob に必要な手順は次のとおりです。

1. Alice が Azure DevOps プロジェクトに空の Git リポジトリを作成します。 Azure DevOps プロジェクトは、Contoso Azure AD テナントの下に作成された Azure サブスクリプション内に存在する必要があります。 

2. Alice は、Machine Learning 実験アカウント、ワークスペース、および Machine Learning Workbench プロジェクトを、自分のコンピューターに作成します。 Alice は、プロジェクトを作成するとき、Azure DevOps Git リポジトリの URL を入力します。

3. Alice は、プロジェクトでの作業を始めます。 彼女は、スクリプトをいくつか作成して何回か実行します。 実行のたびに、プロジェクト フォルダー全体のスナップショットが、Machine Learning Workbench によって作成された Azure DevOps Git リポジトリの実行履歴ブランチに、コミットとして自動的にプッシュされます。

4. Alice は作業の進行に満足しています。 彼女は、変更をローカルな master ブランチにコミットした後、それを Azure DevOps Git リポジトリの master ブランチにプッシュしようと考えます。 プロジェクトを開き、Machine Learning Workbench でコマンド プロンプト ウィンドウを開いて、次のコマンドを入力します。
    
    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Azure DevOps.
    $ git push
    ```

5. Alice は共同作成者として Bob をワークスペースに追加します。 これは、Azure Portal で、または前に示したように `az role assignment` コマンドを使って、行うことができます。 また、Alice は、Bob に Azure DevOps Git リポジトリへの読み取り/書き込みアクセス許可も付与します。

6. Bob は、自分のコンピューターで Machine Learning Workbench にサインインします。 彼は、Alice から共有されているワークスペースを見ることができます。 Bob は、そのワークスペースに一覧表示されている iris プロジェクトを参照できます。 

7. Bob は、プロジェクト名を選びます。 プロジェクトが、Bob のコンピューターにダウンロードされます。
    * ダウンロードされるプロジェクト ファイルは、実行履歴に記録された最新の実行のスナップショットのコピーです。 master 分岐に対する最新のコミットではありません。
    * ローカルのプロジェクト フォルダーは、ステージングされていない変更を含む master ブランチに設定されています。

8. Bob は、Alice によって行われた実行を見ることができます。 彼は、以前の実行のスナップショットをどれでも復元できます。

9. Bob は、Alice がプッシュした最新の変更を取得し、異なるブランチで作業を始めようと考えます。 そこで Bob は、Machine Learning Workbench でコマンド プロンプト ウィンドウを開き、次のコマンドを実行します。

    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Azure DevOps Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob はプロジェクトを変更して、新しい実行を送信します。 bob ブランチで変更が行われます。 Bob の実行が、Alice からも見えるようになります。

11. Bob は、自分の変更をリモート Git リポジトリにプッシュする準備ができました。 Alice が作業している master ブランチとの競合を回避するため、Bob は自分の作業をやはり bob という名前の新しいリモート ブランチにプッシュします。

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Azure DevOps Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Bob は、自分のコードでのすばらしい新手法について Alice に伝えるために、bob ブランチから master ブランチへのプル要求をリモート Git リポジトリに作成します。 その後、Alice はプル要求を master ブランチにマージできます。

## <a name="next-steps"></a>次の手順
- [Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用](using-git-ml-project.md)について、詳しく学習してください。
