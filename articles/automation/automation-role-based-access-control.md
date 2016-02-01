<properties 
   pageTitle="Azure Automation におけるロールベースのアクセス制御 | Microsoft Azure"
   description="Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。この記事では、Azure Automation における RBAC の設定方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="sngun"/>

# Azure Automation におけるロールベースのアクセス制御

## ロールベースのアクセス制御

Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。[RBAC](../active-directory/role-based-access-control-configure.md) を使用して、チーム内の職務を分離し、それぞれの職務に必要なアクセス権のみをユーザー、グループ、アプリケーションに付与することができます。ロールベースのアクセス権をユーザーに付与するには、Azure ポータル、Azure コマンドライン ツール、Azure Management API を使用します。

## Automation アカウントの RBAC

Azure Automation でアクセス権を付与するには、Automation アカウント スコープのユーザー、グループ、アプリケーションに適切な RBAC ロールを割り当てます。Automation アカウントでは次の組み込みロールがサポートされています。

|**ロール** | **説明** |
|:--- |:---|
| 所有者 | Automation アカウント内のすべてのリソースおよびアクションへのアクセスは、所有者ロールによって許可されます。Automation アカウントを管理するためのアクセス権を他のユーザー、グループ、アプリケーションに付与することもできます。 |
| 共同作成者 | Automation アカウントに対する他のユーザーのアクセス権に変更を加えることを除くすべての作業は共同作成者ロールで行うことができます。 |
| 閲覧者 | 閲覧者ロールでは、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。 |
| Automation オペレーター | ジョブの開始、停止、一時停止、再開、スケジュール設定といった運用タスクは、Automation オペレーター ロールで実行できます。Automation アカウントのリソース (資格情報アセットや Runbook など) を閲覧したり改変したりできないよう保護したうえで、同じ組織のメンバーにのみ、それらの Runbook の実行を許可する必要がある場合、このロールを活用できます。Automation アカウントとそのリソースに対して Automation オペレーター ロールでサポートされる一連の操作については、[「Automation オペレーター」の「アクション」](../active-directory/role-based-access-built-in-roles.md#automation-operator)を参照してください。 |
| ユーザーアクセスの管理者 | Azure Automation アカウントに対するユーザー アクセスは、ユーザー アクセスの管理者ロールで管理できます。 |

この記事では、Azure Automation における RBAC の設定方法について説明します。

## Azure ポータルを使用して Automation アカウントの RBAC を構成する

1.	[Azure ポータル](http://portal.azure.com/)にログインし、[Automation アカウント] ブレードから、ご利用の Automation アカウントを開きます。  

2.	右上隅にある **[アクセス]** コントロールをクリックします。表示された **[ユーザー]** ブレードで、ご利用の Automation アカウントを管理するための新しいユーザー、グループ、アプリケーションを追加できるほか、その Automation アカウント用に構成できる既存のロールを確認できます。

    ![Access button](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]**[サブスクリプション管理]** は、既定のユーザーとして最初から存在します。Active Directory のサブスクリプション管理グループには、Azure サブスクリプションのサービス管理者と共同管理者が含まれます。サービス管理者は、Azure サブスクリプションとそのリソースの所有者であり、Automation アカウントにも所有者ロールが継承されます。つまり、サブスクリプションの**サービス管理者と共同管理者**のアクセス権が**継承**されます。それ以外のユーザーについてはアクセス権を**割り当てる**ことになります。**[サブスクリプション管理]** をクリックすると、その権限の詳細が表示されます。

### 新しいユーザーの追加とロールの割り当て

1.	[ユーザー] ブレードの **[追加]** をクリックして **[アクセスを追加]** ブレードを開きます。このブレードでは、ユーザー、グループ、アプリケーションを追加し、それらにロールを割り当てることができます。  

    ![ユーザーの追加](media/automation-role-based-access-control/automation-02-add-user.png)

2.	利用可能なロールの一覧からロールを選択します。ここでは**閲覧者**ロールを選択しますが、Automation アカウントでサポートされている任意の組み込みロールを選択してもかまいません。また、自分で定義したカスタム ロールを選択することもできます。

    ![Select role](media/automation-role-based-access-control/automation-03-select-role.png)

3.	**[ユーザーの追加]** をクリックして **[ユーザーの追加]** ブレードを開きます。サブスクリプションを管理するためのユーザー、グループ、またはアプリケーションを既に追加してある場合は、それらのユーザーが一覧に表示されるので、それらを選択してアクセス権を追加することができます。ユーザーがまったく表示されない場合、または追加しようとするユーザーが表示されない場合は、**[招待]** をクリックして **[ゲストを招待する]** ブレードを開きます。有効な Microsoft アカウントの電子メール アドレス (Outlook.com、OneDrive、Xbox Live の ID など) を持つユーザーを招待できます。ユーザーの電子メール アドレスを入力したら、**[選択]** をクリックしてユーザーを追加し、**[OK]** をクリックします。

    ![Add users](media/automation-role-based-access-control/automation-04-add-users.png)
 
**[ユーザー]** ブレードに目的のユーザーが追加され、**閲覧者**ロールが割り当てられていることを確認できます。

![List users](media/automation-role-based-access-control/automation-05-list-users.png)

**[ロール]** ブレードから、ユーザーにロールを割り当てることもできます。[ユーザー] ブレードから **[ロール]** をクリックして **[ロール]** ブレードを開きます。このブレードでは、ロールの名前と、そのロールに割り当てられているユーザー数およびグループ数を確認できます。

![Assign role from users blade](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE]ロールベースのアクセス制御は、Automation アカウント レベルでのみ設定できます。Automation アカウントより下のリソース レベルで設定することはできません。

ロールは、単一のユーザー、グループ、またはアプリケーションに複数割り当てることができます。たとえば、**Automation オペレーター** ロールと**閲覧者ロール**をユーザーに追加した場合、そのユーザーは、すべての Automation リソースを表示することに加え、Runbook ジョブを実行することができます。ドロップダウンを展開すると、ユーザーに割り当てられているロールの一覧を表示できます。

![View multiple roles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### ユーザーの削除

Automation アカウントの管理に関与しないユーザーや既に退社したユーザーについては、アクセス権を削除することができます。ユーザーを削除する手順を次に示します。

1.	**[ユーザー]** ブレードで、削除するロールの割り当てを選択します。

2.	[割り当ての詳細] ブレードで、**[削除]** ボタンをクリックします。

3.	**[はい]** をクリックして削除を確定します。

    ![Remove users](media/automation-role-based-access-control/automation-08-remove-users.png)

## ロールが割り当てられているユーザー

ロールに割り当てられているユーザーがその Automation アカウントにログインすると、**既定のディレクトリ**の一覧に所有者のアカウントが表示されていることに気付きます。自分が追加されている Automation アカウントを確認するには、既定のディレクトリを所有者の既定のディレクトリに切り替える必要があります。

![既定のディレクトリ](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Automation オペレーター ロールのユーザー エクスペリエンス

Automation オペレーター ロールに割り当てられているユーザーが、その割り当て先となっている Automation アカウントを表示したときに見えるのは、その Automation アカウントで作成された Runbook、Runbook ジョブ、およびスケジュールの一覧のみで、それらの定義は表示されません。これらのユーザーは、Runbook ジョブの開始、停止、一時停止、再開、スケジュール設定を実行することができます。Automation の構成、ハイブリッド worker グループ、DSC ノードなど、他のリソースにアクセスすることはできません。

![No access to resourcres](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Automation オペレーター ロールのユーザーが Runbook をクリックしても、ソースを表示したり Runbook を編集したりするためのコマンドは利用できません。Automation オペレーター ロールでは、それらにアクセスすることが許可されていないためです。

![No access to edit runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

スケジュールを表示および作成することはできますが、その他の種類のアセットは利用できません。

![No access to assets](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Runbook に関連付けられている Webhook を表示するアクセス権もこのユーザーにはありません。

![No access to webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Azure PowerShell を使用して Automation アカウントの RBAC を構成する

Automation アカウントに対するロールベースのアクセス制御は、次の [Azure PowerShell コマンドレット](../active-directory/role-based-access-control-manage-access-powershell.md)を使用して構成することもできます。

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx): Azure Active Directory RBAC で利用できるすべてのロールを一覧表示します。このコマンドに **Name** プロパティを組み合わせることで、特定のロールに割り当てられているすべてのユーザーを一覧表示できます。**例:** ![Get role definition](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx): 特定のスコープにおける Azure RBAC ロールの割り当てを一覧表示します。このコマンドにパラメーターを指定しなかった場合、対象サブスクリプションで行われたすべてのロールの割り当てが返されます。指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを一覧表示するには、**ExpandPrincipalGroups** パラメーターを使用します。**例:** Automation アカウント内のすべてのユーザーとそのロールを一覧表示するには、次のコマンドを使用します。

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Get role assignment](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx): 特定のスコープのユーザー、グループ、アプリケーションにアクセス権を付与します。**例:** Automation アカウント スコープのユーザーに対して新しいロール "Automation Operator" を作成するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![New role assignment](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx): 特定のスコープの指定したユーザー、グループ、またはアプリケーションのアクセス権を削除します。**例:** Automation アカウント スコープのユーザーに対して新しいロール "Automation Operator" を作成するには、次のコマンドを使用します。

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

上記のコマンドレットのサインイン名、サブスクリプション ID、リソース グループ名、Automation アカウント名は、実際のアカウント情報に置き換えてください。ロールの割り当てを削除するかどうかの確認を求められるので、続行する場合は **yes** を選択してください。


## 次のステップ
-  Azure Automation に対して RBAC を構成する各種の方法については、「[Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理](../active-directory/role-based-access-control-manage-access-powershell.md)」を参照してください。
- Runbook を開始するさまざまな方法については、「[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)」を参照してください。
- 種類については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。

<!---HONumber=AcomDC_0121_2016-->