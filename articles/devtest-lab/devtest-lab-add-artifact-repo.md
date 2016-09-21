<properties
	pageTitle="Azure DevTest Labs でラボに Git アーティファクト リポジトリを追加する | Microsoft Azure"
	description="Azure DevTest Labs でカスタム アーティファクト ソースに GitHub または Visual Studio Team Services Git リポジトリを追加する"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Azure DevTest Labs でラボに Git アーティファクト リポジトリを追加する

> [AZURE.VIDEO how-to-add-your-private-artifacts-repository-in-a-devtest-lab]

Azure DevTest Labs で、アーティファクトに *アクション* を選択します。 - VM 作成時に、ソフトウェアをインストールしたり、スクリプトやコマンドを実行します。既定では、公式の Azure DevTest Labs アーティファクト リポジトリからのアーティファクトがラボに含まれます。ラボに Git アーティファクト リポジトリを追加することで、チームが作成したアーティファクトを含めることができます。リポジトリは、[GitHub](https://github.com) または [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上にホストできます。

- GitHub リポジトリを作成する方法については、「[GitHub Bootcamp (GitHub ブートキャンプ)](https://help.github.com/categories/bootcamp/)」を参照してください。
- Git リポジトリを使用して、Team Services プロジェクトを作成する方法については、「[Connect to Visual Studio Team Services (Visual Studio Team Services への接続)](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

次のスクリーン ショットでは、GitHub でアーティファクトを含むリポジトリがどのように表示されるかの例を示します。 ![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)


## リポジトリ情報および資格情報を取得する

アーティファクト リポジトリをラボに追加するには、最初にリポジトリから特定の情報を取得する必要があります。次のセクションでは、GitHub および Visual Studio Team Services 上にホストされたアーティファクト レポジトリの情報取得について説明します。

### GitHub リポジトリのクローン URL と個人用アクセス トークンの取得

GitHub リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. チームのアーティファクトを含む GitHub リポジトリのホーム ページを参照します。

1. **[複製またはダウンロード]**を選択します。

1. ボタンを選択して **HTTPS クローンの URL** をクリップボードにコピーし、後で使用するために URL を保存します。

1. GitHub の右上隅にあるプロファイルの画像を選択し、**[設定]** を選択します。

1. **[個人設定]** メニューの左側で、**[個人用アクセス トークン]** を選択します。

1. **[新しいトークンの生成]** を選択します。

1. **新しい個人用アクセス トークン**ページで、**トークンの説明**を入力し、**[範囲の選択]** で既定の項目をそのまま使用します。次に **[Generate Token]** を選択します。

1. 後で必要となるため、生成されたトークンを保存します。

1. これで、GitHub を閉じることができます。

1. [アーティファクト リポジトリにラボを接続する](#connect-your-lab-to-the-artifact-repository) のセクションに続きます。

### Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンの取得

Visual Studio Team Services リポジトリのクローン URL と個人用アクセス トークンを取得するには、次の手順に従います:

1. チーム コレクションのホーム ページ (例: `https://contoso-web-team.visualstudio.com`) を開き、アーティファクト プロジェクトを選択します。

1. プロジェクトのホーム ページで、**[コード]** を選択します。

1. クローン URL を表示するには、プロジェクト **[コード]** ページで、**[クローン]** を選択します。

1. このチュートリアルで後ほど必要になるため、URL を保存します。

1. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューから **[マイ プロファイル]** を選択します。

1. プロファイル情報ページで、**[セキュリティ]** を選択します。

1. **[セキュリティ]** タブで **[追加]** を選択します。

1. **[個人用アクセス トークンの作成]** ページで、次を実行します。

    - トークンの**説明**を入力します。
    - **[Expires In]** の一覧から **[180 日]** を選択します。
    - **[アカウント]** の一覧から **[アクセス可能なすべてのアカウント]** を選択します。
    - **[All scopes]** オプションを選択します。
    - **[トークンの作成]** を選択します。

1. 完了すると、新しいトークンが **[個人用アクセス トークン]** の一覧に表示されます。**[トークンのコピー]** を選択し、後ほど必要になるため、トークンの値を保存します。

1. [アーティファクト リポジトリにラボを接続する](#connect-your-lab-to-the-artifact-repository) のセクションに続きます。

##アーティファクト リポジトリに、ラボを接続する

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボのブレードで、**[構成]** を選択します。

1. ラボの **[構成]** ブレードで、**[アーティファクト リポジトリ]** を選択します。

1. **[アーティファクト リポジトリ]** ブレードで、**[+ 追加]** を選択します。

	![アーティファクト リポジトリ ボタンを追加する](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
 
1. **[アーティファクト リポジトリ]** ブレードで、次の値を指定します:

    - **名前** - リポジトリの名前を入力します。
    - **Git クローン URL** - 先ほど GitHub または Visual Studio Team Services からコピーした Git HTTPS クローン URL を入力します。
    - **フォルダー パス** - アーティファクト定義ファイルを含むクローン URL に相対フォルダー パスを入力します。
    - **ブランチ** - アイテム定義ファイルを取得する分岐を入力します。
    - **個人用アクセス トークン** - 先ほど GitHub、または Visual Studio Team Services で取得した個人用アクセス トークンを入力します。
     
	![アーティファクト リポジトリのブレード](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)

1. [**保存**] を選択します。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 関連するブログ記事
- [How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs でアーティファクトの失敗をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Azure Dev Test Lab で ARM テンプレートを使用して既存の AD ドメインに VM を参加させる)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0907_2016-->