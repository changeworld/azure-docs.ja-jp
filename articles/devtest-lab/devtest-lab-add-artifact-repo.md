<properties
	pageTitle="ラボへの Git アーティファクト リポジトリの追加 | Microsoft Azure"
	description="DevTest ラボでカスタム アーティファクト用に GitHub または Visual Studio Team Services Git リポジトリを追加します"
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
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# ラボへの Git アーティファクト リポジトリの追加

> [AZURE.NOTE] この記事の付属のビデオ「[How to add your private Artifacts Repository in a DevTest Lab (DevTest Labs でプライベート アーティファクト リポジトリを追加する方法)](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab)」をご覧ください

## Overview

既定では、公式の Azure DevTest Labs アーティファクト リポジトリからのアーティファクトがラボに含まれます。ラボに Git アーティファクト リポジトリを追加することで、チームが作成したアーティファクトを含めることができます。リポジトリは、[GitHub](https://github.com) または [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上にホストできます。

- GitHub リポジトリを作成する方法については、「[GitHub Bootcamp (GitHub ブートキャンプ)](https://help.github.com/categories/bootcamp/)」を参照してください。
- Git リポジトリを使用して、Team Services プロジェクトを作成する方法については、「[Connect to Visual Studio Team Services (Visual Studio Team Services への接続)](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

次のスクリーン ショットでは、GitHub でアーティファクトを含むリポジトリがどのように表示されるかの例を示します。 ![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## GitHub のアーティファクト リポジトリのラボへの追加

ラボに GitHub のアーティファクト リポジトリを追加するには、最初に HTTPS クローン URL と個人用アクセス トークンをアーティファクト リポジトリから取得し、次にラボでその情報を入力します。

### GitHub リポジトリのクローン URL と個人用アクセス トークンの取得

1. 後で使用するために、チームのアーティファクトを含む GitHub リポジトリのホーム ページで **HTTPS クローンの URL** を保存します。

1. 右上隅にあるプロファイルの画像を選択し、**[設定]** を選択します。

1. **[個人設定]** メニューの左側で、**[個人用アクセス トークン]** を選択します。

1. **[新しいトークンの生成]** を選択します。

1. **新しい個人用アクセス トークン**ページで、**トークンの説明**を入力し、**[範囲の選択]** で既定の項目をそのまま使用します。次に **[Generate Token]** を選択します。

1. 後で必要になるため、生成されたトークンを保存します。

1. これで、GitHub を閉じることができます。

###GitHub リポジトリに、ラボを接続します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボのブレードで、**[設定]** を選択します。

1. ラボの **[設定]** ブレードで、**[Artifacts Repository (アーティファクト リポジトリ)]** を選択します。

1. **[アーティファクト リポジトリ]** ブレードで、次を実行します。

    1. リポジトリの**名前**を入力します。
    1. 保存された **Git クローン Url** を入力します。
    2. アーティファクトが格納されているアーティファクト リポジトリの**フォルダー パス**を入力します。
    3. アーティファクト リポジトリに保存された**個人用アクセス トークン**を入力します。
    4. [**保存**] を選択します。

これで、**[Add Artifacts]** ブレードにリポジトリ内のアーティファクトが一覧表示されます。

## ラボへの Visual Studio Git のアーティファクト リポジトリの追加

ラボに Visual Studio Git のアーティファクト リポジトリを追加するには、最初に HTTPS クローン URL と個人用アクセス トークンをアーティファクト リポジトリから取得し、次にラボでその情報を入力します。

### アーティファクト プロジェクトの Visual Studio Web ページ

1. チーム コレクションのホーム ページ (例: `https://contoso-web-team.visualstudio.com`) を開き、アーティファクト プロジェクトを選択します。

2. プロジェクトのホーム ページで、**[コード]** を選択します。

1. クローン URL を表示するには、プロジェクト **[コード]** ページで、**[クローン]** を選択します。

1. このチュートリアルで後ほど必要になるため、URL を保存します。

1. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューから **[マイ プロファイル]** を選択します。

1. プロファイル情報ページで、**[セキュリティ]** を選択します。

1. **[セキュリティ]** タブで **[追加]** を選択します。

1. **[個人用アクセス トークンの作成]** ページで、次を実行します。

    1. トークンの**説明**を入力します。
    2. **[Expires In]** の一覧から **[180 日]** を選択します。
    3. **[アカウント]** の一覧から **[アクセス可能なすべてのアカウント]** を選択します。
    4. **[All scopes]** オプションを選択します。
    5. **[トークンの作成]** を選択します。

1. 完了すると、新しいトークンが **[個人用アクセス トークン]** の一覧に表示されます。**[トークンのコピー]** を選択し、すぐに使用するため、トークンの値を保存します。

### ラボ

1. ラボのブレードで、**[設定]** を選択します。

    ![設定の選択](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. **[設定]** ブレードで、**[Artifacts Repository (アーティファクト リポジトリ)]** を選択します。

1. **[アーティファクト リポジトリ]** ブレードで、次を実行します。

    1. リポジトリの表示**名**を入力します。
    1. 保存された **Git クローン Url** を入力します。
    2. アーティファクトが格納されているアーティファクト リポジトリの**フォルダー パス**を入力します。
    3. アーティファクト リポジトリに保存された**個人用アクセス トークン**を入力します。
    4. [**保存**] を選択します。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 関連するブログ記事
- [How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs でアーティファクトの失敗をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Azure Dev Test Lab で ARM テンプレートを使用して既存の AD ドメインに VM を参加させる)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0831_2016-->