<properties
	pageTitle="Windows PowerShell を使用した役割ベースのアクセス制御の管理"
	description="Windows PowerShell を使用した役割ベースのアクセス制御の管理"
	services="azure-portal"
	documentationCenter="na"
	authors="Justinha"
	manager="terrylan"
	editor="mollybos"/>

<tags
	ms.service="azure-portal"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="justinha"/>

# Windows PowerShell を使用した役割ベースのアクセス制御の管理 #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli.md)


Azure ポータルと Azure リソース管理 API のロール ベースの Access Control (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

このチュートリアルでは、Windows PowerShell を使って RBAC を管理する方法について学習します。役割の割り当てを作成して確認するプロセスについて説明します。

**推定所要時間:** 15 分

## 前提条件

Windows PowerShell を使って RBAC を管理するには、その前に以下が必要です。

- Windows PowerShell バージョン 3.0 または 4.0。Windows PowerShell のバージョンを調べるには、`$PSVersionTable` と入力して、`PSVersion` の値が 3.0 か 4.0 かを確認します。互換バージョンをインストールするには、「[￼Windows Management Framework 3.0￼](http://www.microsoft.com/download/details.aspx?id=34595)」または「[￼Windows Management Framework 4.0￼](http://www.microsoft.com/download/details.aspx?id=40855)」をご覧ください。

- Azure PowerShell バージョン 0.8.8 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](../install-configure-powershell.md)」をご覧ください。

このチュートリアルは、Windows PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。Windows PowerShell の詳細については、「[Getting Started with Windows PowerShell (Windows PowerShell の概要)](http://technet.microsoft.com/library/hh857337.aspx)」を参照してください。

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

	Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

	Get-Help Add-AzureAccount -Detailed

また、次のチュートリアルをお読みになり、Windows PowerShell での Azure リソース マネージャーのセット アップと使用について詳しく理解してください。

- [Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)
- [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)


## サブスクリプションへの接続

RBAC は Azure リソース マネージャーのみと協働するので、最初に以下のように入力して Azure リソース マネージャー モードに切り替えます。

    PS C:\> Switch-AzureMode -Name AzureResourceManager

詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

ご使用の Azure サブスクリプションに接続するには、以下のように入力します。

    PS C:\> Add-AzureAccount

ポップアップ ブラウザー コントロールで、Azure アカウントのユーザー名とパスワードを入力します。PowerShell は、このアカウントのサブスクリプションをすべて入手し、最初のサブスクリプションを既定として使うと想定します。RBAC の使用時には、共同管理者であるか役割が割り当てられていることによってアクセス許可があるサブスクリプションのみを入手できることに注意してください。

サブスクリプションが複数ある場合に、別のサブスクリプションに切り替えるには、以下のように入力します。

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

詳細については、「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)」を参照してください。

## 既存の役割の割り当ての確認

ここで、既にサブスクリプションに存在している役割の割り当てを確認しましょう。次のコマンドを入力します。

    PS C:\> Get-AzureRoleAssignment

サブスクリプション内の役割の割り当てがすべて返されます。注意点が 2 つあります。

1. サブスクリプション レベルの読み取りアクセス権が必要です。
2. サブスクリプションに役割の割り当てが多数ある場合は、すべて取得するまでに時間がかかることがあります。

特定の役割の定義向けに、特定のスコープで、特定のユーザーに対して行われた既存の役割の割り当てを確認することもできます。次のコマンドを入力します。

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

AD テナント内の特定のユーザー (リソース グループ「group1」に関する「所有者」役割の割り当てを持つユーザー) に関する役割の割り当てがすべて返されます。役割は、次の 2 つの場所から割り当てられます。

1. ユーザーに対する、リソース グループに関する「所有者」の役割の割り当て。
2. ユーザーに対する、このリソース グループの親 (この例ではサブスクリプション) に関する「所有者」役割の割り当て。特定のレベルのアクセス許可がある場合、そのすべての子に対する同じアクセス許可も付与されているからです。

このコマンドレットのパラメーターはすべて省略可能です。パラメーターを組み合わせて、さまざまなフィルターで役割の割り当てを確認することができます。

## 役割の割り当ての作成

役割の割り当てを作成するには、以下の点について考慮する必要があります。

役割を割り当てるユーザー: 次の Azure Active Directory コマンドレットを使用して、AD テナントにいるユーザー、グループおよびサービス プリンシパルを確認できます。

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

割り当てる役割: 次のコマンドレットを使用して、サポートされているロールの定義を表示できます。

    PS C:\> Get-AzureRoleDefinition

割り当てるスコープ: スコープには 3 つのレベルがあります。

    - The current subscription
    - A resource group, to get a list of resource groups, type `PS C:\> Get-AzureResourceGroup`
    - A resource, to get a list of resources, type `PS C:\> Get-AzureResource`

次に、`New-AzureRoleAssignment` ￼￼￼ を使ってロールの割り当てを作成します。次に例を示します。


次を使用すると、ユーザーに閲覧者として現在のサブスクリプション レベルでの役割の割り当てが作成されます。

	 PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

次を使用すると、リソース グループ レベルでのロールの割り当てが作成されます。

	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

次を使用すると、リソース グループ レベルでのグループへのロールの割り当てが作成されます。

	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

次を使用すると、リソース レベルでのロールの割り当てが作成されます。

	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## アクセス許可の確認

ご使用のアカウントに役割の割り当てがあることを確認してから、次を実行することで、実際に役割の割り当てが付与したアクセス許可を確認することができます。

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

これらの 2 つのコマンドレットでは、読み取りのアクセス許可があるリソース グループまたはリソースのみが返されます。また、持っているアクセス許可も表示します。

他のコマンドレット (`New-AzureResourceGroup` など) を実行する場合、アクセス許可がないとアクセス拒否エラーが表示されます。

## 次のステップ

Windows PowerShell を使用した役割ベースのアクセス制御の管理の詳細と、関連トピックについては、以下を参照してください。

- [Azure のロール ベースのアクセス制御](../role-based-access-control-configure.md)
- [Azure リソース マネージャー コマンドレットに関するページ](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): AzureResourceManager モジュールのコマンドレットを使用する方法について説明します。
- [Using Resource groups to manage your Azure resources (リソース グループを使用した Azure リソースの管理)](../azure-preview-portal-using-resource-groups.md): Microsoft Azure 管理ポータルでリソース グループを作成して管理する方法について説明します。
- [Azure blog (Azure のブログ)](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
- [Windows PowerShell blog (Windows PowerShell ブログ)](http://blogs.msdn.com/powershell): Windows PowerShell の新機能について説明します。
- ["Hey, Scripting Guy!" ブログ](http://blogs.technet.com/b/heyscriptingguy/): 実践で使えるヒントとテクニックを Windows PowerShell コミュニティから得られます。
- [Azure CLI を使用したロール ベースの Access Control の構成](role-based-access-control-xplat-cli.md)
- [ロール ベースの Access Control のトラブルシューティング](role-based-access-control-troubleshooting.md)
 

<!---HONumber=August15_HO6-->