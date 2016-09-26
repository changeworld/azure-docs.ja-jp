<properties
	pageTitle="Azure DevTest Labs での所有者とユーザーの追加 | Microsoft Azure"
	description="Azure Portal または PowerShell を使用した Azure DevTest Labs での所有者とユーザーの追加"
	services="devtest-lab,virtual-machines"
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
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Azure DevTest Labs での所有者とユーザーの追加

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

Azure DevTest Labs へのアクセスは、[Azure のロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) で制御します。RBAC を使用して、チーム内で職務を*ロール*に分離し、職務に必要なアクセス許可のみをユーザーに付与します。RBAC ロールは*所有者*、*DevTest ラボ ユーザー*、および*の共同作成者*の 3 つです。この記事では、これら 3 つの主な RBAC ロールそれぞれで実行できるアクションについて説明します。ポータルを経由して、および PowerShell スクリプトを使用してラボにユーザーを追加する方法、およびサブスクリプション レベルでユーザーを追加する方法について説明します。

## 各ロールで実行できるアクション

次の 3 つの主なロールをユーザーを割り当てることができます。

- 所有者
- DevTest Labs ユーザー
- 共同作成者

次の表は、これらの各ロールのユーザーが実行できるアクションを示しています。

| **このロールのユーザーが実行できるアクション** | **DevTest Labs ユーザー** | **所有者** | **共同作成者** |
|---|---|---|---|
| **ラボのタスク** | | | |
| ラボへのユーザーの追加 | なし | あり | なし |
| コストの設定の更新 | なし | はい | はい |
| **VM ベースのタスク** | | | |
| カスタム イメージの追加と削除 | なし | はい | はい |
| 数式の追加、更新、および削除 | はい | はい | はい |
| Azure Marketplace のイメージのホワイトリスト | なし | はい | はい |
| **VM タスク** | | | |
| VM の作成 | はい | はい | はい |
| VM の開始、停止、および削除 | ユーザーによって作成された VM のみ | はい | はい |
| VM のポリシーの更新 | なし | はい | はい |
| データ ディスクの VM への追加と VM からの削除 | ユーザーによって作成された VM のみ | はい | はい |
| **アーティファクトのタスク** | | | |
| アーティファクトのリポジトリの追加と削除 | なし | はい | はい |
| アーティファクトの適用 | はい | はい | はい |

> [AZURE.NOTE] ユーザーが VM を作成すると、そのユーザーは作成された VM の**所有者**ロールに自動的に割り当てられます。

## ラボ レベルでの所有者またはユーザーの追加

所有者とユーザーは Azure Portal 経由で、ラボレベルで追加できます。これには、有効な [Microsoft アカウント (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account) の外部ユーザーも含まれます。次のステップでは、Azure DevTest Labs で所有者またはユーザーをラボに追加するプロセスについて説明します。

1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. ラボのブレードで、**[構成]** を選択します。

1. ラボの **[構成]** ブレードで、**ユーザー**を選択します。

1. **[ユーザー]** ブレードで、**[追加]** を選択します。

	![ユーザーの追加](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **[ロールの選択]** ブレードで、任意のロールを選択します。セクション「[各ロールで実行できるアクション](#actions-that-can-be-performed-in-each-role)」では、所有者、DevTest ユーザーおよび共同作成者ロールのユーザーが実行できるさまざまなアクションが一覧表示されます。

1. **[ユーザーの追加]** ブレードで、指定したロールに追加するユーザーの電子メール アドレスまたは名前を入力します。ユーザーが見つからない場合は、問題を示すエラー メッセージが表示されます。ユーザーが見つかった場合は、ユーザーが表示され、選択されます。

1. **[選択]** を選択します。

1. **[OK]** を選択して、**[アクセスを追加]** ブレードを閉じます。

1. **[ユーザー]** ブレードに戻ると、このユーザーが追加されています。

## PowerShell を使用して、ラボに外部ユーザーを追加します。

Azure Portal にユーザーを追加するだけでなく、PowerShell スクリプトを使用して、ラボに外部ユーザーを追加することもできます。次の例では、**Values to change** コメントの下のパラメーターの値を変更するだけです。`subscriptionId`、`labResourceGroup`、および `labName` の値は、Azure Portal のラボのブレードから取得することができます。

> [AZURE.NOTE]
サンプル スクリプトでは、指定したユーザーが Active Directory にゲストとして追加されていると想定しており、そうではない場合はスクリプトが失敗します。Active Directory にないユーザーをラボに追加するには、セクション「[ラボ レベルでの所有者またはユーザーの追加](#add-an-owner-or-user-at-the-lab-level)」で示されているように、Azure Portal を使用してユーザーをロールに割り当てます。

	# Add an external user in DevTest Labs user role to a lab
	# Ensure that guest users can be added to the Azure Active directory:
	# https://azure.microsoft.com/ja-JP/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

	# Values to change
	$subscriptionId = "<Enter Azure subscription ID here>"
	$labResourceGroup = "<Enter lab's resource name here>"
	$labName = "<Enter lab name here>"
	$userDisplayName = "<Enter user's display name here>"

	# Log into your Azure account
	Login-AzureRmAccount
	
	# Select the Azure subscription that contains the lab. 
	# This step is optional if you have only one subscription.
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	# Retrieve the user object
	$adObject = Get-AzureRmADUser -SearchString $userDisplayName
	
	# Create the role assignment. 
	$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
	New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## サブスクリプション レベルでの所有者またはユーザーの追加

Azure のアクセス許可は、Azure で親スコープから子スコープに反映されます。したがって、ラボを含む Azure サブスクリプションの所有者は、自動的にこれらのラボの所有者となります。また、これらの所有者は、VM やラボのユーザーによって作成されたその他のリソース、および Azure DevTest Labs サービスも所有します。

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) でラボのブレードを使用してラボに他の所有者を追加できます。ただし、追加された所有者の管理のスコープは、サブスクリプションの所有者のスコープよりも狭くなります。たとえば、追加された所有者には、DevTest Labs サービスによって、サブスクリプションで作成されたリソースの一部へのフル アクセスがありません。

Azure サブスクリプションに所有者を追加するには、次の手順を実行します。

1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[サブスクリプション]** を選択します。

1. 目的のサブスクリプションを選択します。

1. **[アクセス]** アイコンを選択します。

	![Access users](./media/devtest-lab-add-devtest-user/access-users.png)

1. **[ユーザー]** ブレードで、**[追加]** を選択します。

	![ユーザーの追加](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **[ロールの選択]** ブレードで、**[所有者]** を選択します。

1. **[ユーザーの追加]** ブレードで、所有者として追加するユーザーの電子メール アドレスまたは名前を入力します。ユーザーが見つからない場合は、問題を示すエラー メッセージが表示されます。ユーザーが見つかった場合は、**[ユーザー]** ボックスの下に表示されます。

1. 見つかったユーザー名を選択します。

1. **[選択]** を選択します。

1. **[OK]** を選択して、**[アクセスを追加]** ブレードを閉じます。

1. **[ユーザー]** ブレードに戻ると、このユーザーが所有者として追加されています。これで、このユーザーはこのサブスクリプションで作成されたすべてのラボの所有者となり、所有者タスクを実行できるようになります。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<!---HONumber=AcomDC_0914_2016-->