<properties
	pageTitle="Azure Active Directory の AD ロールベースのアクセス制御 | Microsoft Azure"
	description="この記事では、Azure のロールベースのアクセス制御について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="12/04/2015"
	ms.author="inhenk"/>

# Azure Active Directory のロールベースのアクセス制御

## ロールベースのアクセス制御
Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。RBAC を使用して、開発チーム内で職務を分離し、職務に必要なアクセス権のみをユーザーに付与します。

### Azure でのアクセス管理の基礎
各 Azure サブスクリプションは Azure Active Directory にあります。そのディレクトリからのユーザー、グループ、およびアプリケーションのみが、Azure クラシック ポータル、Azure コマンドライン ツール、および Azure 管理 API を使用して、Azure のサブスクリプションでリソースを管理するためのアクセス権を付与されます。

正しいスコープで、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを割り当てることで、アクセス権が付与されます。サブスクリプション全体にアクセス権を付与するには、サブスクリプションのスコープでロールを割り当てます。サブスクリプション内の特定のリソース グループへのアクセス権を付与するには、リソース グループのスコープでロールを割り当てます。Web サイト、仮想マシン、およびサブネットのように、リソースのみにアクセス権を付与するよう、特定のリソースでロールを割り当てる場合もあります。

![](./media/role-based-access-control-configure/overview.png)

ユーザー、グループ、およびアプリケーションに割り当てる RBAC ロールでは、そのスコープ内でユーザー (またはアプリケーション) が管理できるリソースを決定します。

### Azure RBAC の組み込みのロール
Azure RBAC には、すべてのリソースの種類に適用される 3 つの基本的なロール(所有者、作成協力者、および閲覧者) があります。所有者は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。作成協力者は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。閲覧者は、既存の Azure リソースを表示できるのみです。残りの Azure RBAC ロールでは、特定の Azure リソースの管理が許可されます。たとえば、仮想マシンの作成協力者ロールでは、仮想マシンの作成と管理は許可されますが、仮想マシンが接続する仮想ネットワークまたはサブネットの管理は許可されません。

[RBAC の組み込みのロール](role-based-access-built-in-roles.md)は、Azure で使用できる組み込みの RBAC ロールの一覧を表示します。各ロールに対しては、組み込みのロールがアクセス権を付与する操作を指定します。

### Azure のリソース階層とアクセス権の継承
Azure の各サブスクリプションは、1 つのディレクトリのみに属しており、各リソース グループは 1 つのサブスクリプションのみに属します。さらに、各リソースは、1 つのリソース グループにのみ属しています。親スコープで付与されたアクセス権は、子スコープに継承されます。閲覧者のロールをサブスクリプションのスコープで、Azure AD グループに付与すると、そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示することができるようになります。リソース グループのスコープで、アプリケーションに作成協力者のロールを付与すると、そのアプリケーションは、そのリソース グループのすべてのタイプのリソースを管理することができるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

### Azure RBAC と従来のサブスクリプションの管理者と共同管理者の比較
従来のサブスクリプションの管理者と共同管理者は、Azure サブスクリプションへのフル アクセス権を持ちます。Azure クラシック ポータル (https://manage.windowsazure.com)、および Azure Service Manager API の両方に加えて、Azure ポータル (https://portal.azure.com)、および新しい Azure リソース マネージャー API を利用してリソースを管理できます。RBAC モデルで、従来の管理者は、サブスクリプション スコープで所有者ロールを割り当てられます。

Azure の詳細な承認モデル (Azure RBAC) は、Azure ポータル (https://portal.azure.com) と Azure リソース マネージャー API でのみサポートされます。RBAC ロールを (サブスクリプション、リソース グループ、リソース スコープで) 割り当てられているユーザーとアプリケーションは、従来の管理ポータル (http://manage.windowsazure.com) と Azure Service Management API を使用できません。

### 管理用の承認とデータ操作
Azure の詳細な承認モデル (Azure RBAC) は、Azure クラシック ポータルと Azure リソース マネージャー API における Azure リソースの管理操作専用です。RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。たとえば、ストレージ アカウントの作成/読み取り/更新/削除は RBAC によって制御できますが、ストレージ アカウント内の BLOB やテーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。同様に、SQL DB の作成/読み取り/更新/削除は RBAC によって制御できますが、DB 内の SQL テーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。

## Azure クラシック ポータルを使用したアクセス権の管理
### アクセス許可の表示
リソース グループ ブレードの [essentials] セクションでは、[access settings] を選択します。**[ユーザー]** ブレードには、リソース グループへのアクセス権が付与されたすべてのユーザー、グループ、およびアプリケーションが一覧表示されます。アクセス権は、リソース グループに割り当てられているか、親のサブスクリプションへの割り当てから継承されています。

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]従来のサブスクリプションの管理者と共同管理者は、新しい RBAC モデルのサブスクリプションの有効な所有者です。

### アクセス権の追加
1. **[追加]** アイコンを **[ユーザー]** ブレードでクリックします。 ![](./media/role-based-access-control-configure/grant-access1.png)
2. 割り当てるロールを選択します。
3. アクセス権を付与するユーザー、グループ、またはアプリケーションを検索して、選択します。
4. ユーザー、グループ、およびアプリケーションのディレクトリを表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索します。![](./media/role-based-access-control-configure/grant-access2.png)

### アクセス権の削除
1. **[ユーザー]** ブレードで、削除するロールの割り当てを選択します。
2. [割り当ての詳細] ブレードで、**[削除]** アイコンをクリックします。
3. **[はい]** をクリックして削除を確定します。

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]継承された割り当ては、子スコープから削除できません。親スコープへ移動し、割り当てを削除します。


![](./media/role-based-access-control-configure/remove-access2.png)

## Azure PowerShell を使用したアクセス権の管理
アクセス権は、Azure PowerShell ツールで Azure RBAC コマンドを使用することで管理できます。

-	割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、`Get-AzureRmRoleDefinition` を使用します。

-	指定したサブスクリプション、リソース グループ、またはリソースで有効な RBAC のアクセス権の割り当てを表示するには、`Get-AzureRmRoleAssignment` を使用します。指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを表示するには、`ExpandPrincipalGroups` パラメーターを使用します。従来のサブスクリプションの管理者と共同管理者の一覧も表示するには、`IncludeClassicAdministrators` パラメーターを使用します。

-	ユーザー、グループ、およびアプリケーションにアクセス権を付与するには、`New-AzureRmRoleAssignment` を使用します。

-	アクセス権を削除するには、`Remove-AzureRmRoleAssignment` を使用します。

Azure PowerShell を使用したアクセス権の管理のより詳細な例を確認するには、「[Azure PowerShell を使用したアクセス権の管理](role-based-access-control-manage-access-powershell.md)」を参照してください。

## Azure コマンドライン インターフェイスを使用したアクセス権の管理
アクセス権は、Azure コマンドライン インターフェイスで、Azure RBAC コマンドを使用することで管理できます。

-	割り当てに使用できる RBAC ロールを一覧表示するには、`azure role list` を使用します。アクセス権を付与する操作を調査するには、[azure role show] を使用します。

-	指定したサブスクリプション、リソース グループ、またはリソースで有効な RBAC のアクセス権の割り当てを表示するには、`azure role assignment list` を使用します。指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを表示するには、`expandPrincipalGroups` オプションを使用します。従来のサブスクリプションの管理者と共同管理者の一覧も表示するには、`includeClassicAdministrators` パラメーターを使用します。

-	ユーザー、グループ、およびアプリケーションにアクセス権を付与するには、`azure role assignment create` を使用します。

-	アクセス権を削除するには、`azure role assignment delete` を使用します。

Azure CLI を使用したアクセス権の管理のより詳細な例を確認するには、「[Azure CLI を使用したアクセス権の管理](role-based-access-control-manage-access-azure-cli.md)」を参照してください。

## アクセス変更履歴レポートの使用
Azure サブスクリプションで発生したすべてのアクセス変更は、Azure イベントに記録されます。

### Azure PowerShell を使用したレポートの作成
Azure サブスクリプションで、誰が、どのような種類のアクセス権を、どこから、どこへ、どのスコープで付与または破棄したかといったレポートを作成するには、以下の PowerShell コマンドを使用します。

    `Get-AzureAuthorizationChangeLog`

### Azure CLI を使用したレポートの作成
Azure サブスクリプションで、誰が、どのような種類のアクセス権を、どこから、どこへ、どのスコープで付与または破棄したかといったレポートを作成するには、以下の Azure コマンドライン インターフェイス (CLI) コマンドを使用します。

    `azure authorization changelog`

> [AZURE.NOTE]アクセス変更は、過去 90 日間に対し、(15 日分を) 照会することができます。

次の例では、サブスクリプションで過去 7 日間のアクセス変更をすべて一覧表示します。

![](./media/role-based-access-control-configure/access-change-history.png)

### スプレッドシートへのアクセス変更のエクスポート
確認のためにスプレッドシートへアクセス変更の内容をエクスポートすると便利です。

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Azure RBAC のカスタム ロール
アクセス権に関して実際の要件を満たす組み込みのロールが存在しない場合は、Azure RBAC でカスタム ロールを作成します。カスタム ロールは、Azure PowerShell の RBAC のコマンドライン ツールで作成できるほか、Azure コマンドライン インターフェイスで作成することができます。組み込みのロールと同様、カスタム ロールは、ユーザーやグループ、アプリケーションに対し、サブスクリプション スコープ、リソース グループ スコープ、リソース スコープで割り当てることができます。

以下に示したのは、仮想マシンの監視と再起動を許可するカスタム ロール定義の例です。

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### アクション
カスタム ロールでアクセス権を付与する Azure の操作は、そのロールの actions プロパティで指定します。このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。操作文字列にワイルドカード (*) を指定すると、その文字列と一致するすべての操作にアクセス権が与えられます。次に例を示します。

-	`*/read` は、すべての Azure リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。
-	`Microsoft.Network/*/read` は、Azure の Microsoft.Network リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。
-	`Microsoft.Compute/virtualMachines/*` は、Virtual Machines とその子リソース タイプを対象にすべての操作のアクセス権を付与します。
-	`Microsoft.Web/sites/restart/Action` は、Web サイトを再起動するためのアクセス権を付与します。

Azure リソース プロバイダーの操作を一覧表示するには、`Get-AzureRmProviderOperation` コマンドまたは `azure provider operations show` コマンドを使用します。これらのコマンドを使って、操作文字列が有効であるかどうかを確認したり、操作文字列のワイルドカードを展開した結果を表示したりすることもできます。

![](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### not actions
許可の対象となる一連の操作を指定する際、特定の操作を除外した方が、その操作を除くすべての操作を指定するよりも容易である場合は、カスタム ロールの **NotActions** プロパティを使用してください。カスタム ロールによって実際に付与されるアクセス権は、Actions の操作から **NotActions** の操作を除外することによって計算されます。

**NotActions** で特定の操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにその操作へのアクセスを許可する別のロールを割り当てた場合、その操作の実行がユーザーに許可されることに注意してください。**NotActions** は拒否ルールとは異なり、特定の操作を除外する必要があるときに、許可の対象となる一連の操作を指定しやすくすることを目的としたものに過ぎません。

### AssignableScopes
カスタム ロールの **AssignableScopes** プロパティは、ユーザー、グループ、アプリケーションへの割り当てにカスタム ロールを利用できるスコープ (サブスクリプション、リソース グループ、リソースのいずれか) を指定します。**AssignableScopes** を使用すると、必要なサブスクリプションまたはリソース グループにカスタム ロールの割り当てを限定して、それ以外のサブスクリプションやリソース グループについては元のユーザー エクスペリエンスを保ち、不要な混乱を避けることができます。カスタム ロールの表示、更新、削除をだれに許可するかという点も **AssignableScopes** の制御対象となります。有効な AssignableScopes の例を次に示します。

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”: 対象となるロールの割り当てを 2 つのサブスクリプションに許可します。
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”: 対象となるロールの割り当てを 1 つのサブスクリプションに許可します。
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”: 対象となるロールの割り当てを Network リソース グループにのみ許可します。

### カスタム ロールのアクセス制御
カスタム ロールの **AssignableScopes** プロパティは、そのロールをだれが表示、変更、削除できるかという点に影響を及ぼします。

**カスタム ロールを作成できるユーザー: ** カスタム ロールを正常に作成できるのは、そのロールを作成するユーザーに、指定されたすべての **AssignableScopes** についてカスタム ロールの作成が許可されている場合だけです。カスタム ロールを正常に作成するためには、そのロールを作成するユーザーが、そのすべての **AssignableScopes** に対して `Microsoft.Authorization/roleDefinition/write operation` を実行できることが必須の条件となります。したがって、サブスクリプション、リソース グループ、リソースの Owners (および User Access Administrators) は、それぞれのスコープで使用するカスタム ロールを作成することができます。

**カスタム ロールに変更を加えることができるユーザー: ** カスタム ロールの更新が、ロールのすべての **AssignableScopes** について許可されているユーザーは、そのカスタム ロールに変更を加えることができます。カスタム ロールのすべての **AssignableScopes** に対して `Microsoft.Authorization/roleDefinition/write` 操作を実行できるユーザーが、そのカスタム ロールに変更を加えることができます。たとえば、カスタム ロールの割り当てが 2 つの Azure サブスクリプションに許可されている場合 (そのロールの **AssignableScopes** プロパティに 2 つのサブスクリプションが存在する場合)、ユーザーがカスタム ロールに変更を加えるためには、その両方のサブスクリプションの Owner (または User Access Administrators) である必要があります。

**特定のスコープで割り当て可能なカスタム ロールを表示できるユーザー: ** 特定のスコープで `Microsoft.Authorization/roleDefinition/read` 操作を実行できるユーザーが、そのスコープで割り当て可能な RBAC ロールを表示できます。Azure RBAC の組み込みロールについてはいずれも、割り当て可能なロールの表示対象として許可されています。

<!---HONumber=AcomDC_1210_2015-->