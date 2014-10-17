<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Windows PowerShell を使用した役割ベースのアクセス制御の管理

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ja-jp/documentation/articles/xplat-cli-rbac.md" title="クロスプラットフォーム CLI">クロスプラットフォーム CLI</a></div>

Azure プレビュー ポータルと Azure リソース マネージャー API の役割ベースのアクセス制御 (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

このチュートリアルでは、Windows PowerShell を使って RBAC を管理する方法について学習します。役割の割り当てを作成して確認するプロセスについて説明します。

**所要時間:** 15 分

## 前提条件

Windows PowerShell を使って RBAC を管理するには、その前に以下が必要です。

-   Windows PowerShell バージョン 3.0 または 4.0。Windows PowerShell のバージョンを調べるには、`$PSVersionTable` と入力して、`PSVersion` の値が 3.0 か 4.0 かを確認します。互換バージョンをインストールするには、「[Windows Management Framework 3.0][Windows Management Framework 3.0]」または「[Windows Management Framework 4.0][Windows Management Framework 4.0]」を参照してください。

-   Azure PowerShell バージョン 0.8.8 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Windows Azure PowerShell (Windows Azure PowerShell のインストールおよび構成方法)][How to install and configure Windows Azure PowerShell (Windows Azure PowerShell のインストールおよび構成方法)]」を参照してください。

このチュートリアルは、Windows PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。Windows PowerShell の詳細については、「[Getting Started with Windows PowerShell (Windows PowerShell の概要)][Getting Started with Windows PowerShell (Windows PowerShell の概要)]」を参照してください。

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

    Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

    Get-Help Add-AzureAccount -Detailed

また、次のチュートリアルをお読みになり、Windows PowerShell での Azure リソース マネージャーのセット アップと使用について詳しく理解してください。

-   [Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]
-   [リソース マネージャーでの Windows PowerShell の使用][リソース マネージャーでの Windows PowerShell の使用]

## このチュートリアルの内容

-   [サブスクリプションへの接続][サブスクリプションへの接続]
-   [既存の役割の割り当ての確認][既存の役割の割り当ての確認]
-   [役割の割り当ての作成][役割の割り当ての作成]
-   [アクセス許可の確認][アクセス許可の確認]
-   [次のステップ][次のステップ]

## <span id="connect"></span></a>サブスクリプションへの接続

RBAC は Azure リソース マネージャーのみと協働するので、最初に以下のように入力して Azure リソース マネージャー モードに切り替えます。

    PS C:\> Switch-AzureMode -Name AzureResourceManager

詳細については、「[リソース マネージャーでの Windows PowerShell の使用][リソース マネージャーでの Windows PowerShell の使用]」を参照してください。

ご使用の Azure サブスクリプションに接続するには、以下のように入力します。

    PS C:\> Add-AzureAccount

ポップアップ ブラウザー コントロールで、Azure アカウントのユーザー名とパスワードを入力します。PowerShell は、このアカウントのサブスクリプションをすべて入手し、最初のサブスクリプションを既定として使うと想定します。RBAC の使用時には、共同管理者であるか役割が割り当てられていることによってアクセス許可があるサブスクリプションのみを入手できることに注意してください。

サブスクリプションが複数ある場合に、別のサブスクリプションに切り替えるには、以下のように入力します。

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

詳細については、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

## <span id="check"></span></a>既存の役割の割り当ての確認

ここで、既にサブスクリプションに存在している役割の割り当てを確認しましょう。次のコマンドを入力します。

    PS C:\> Get-AzureRoleAssignment

サブスクリプション内の役割の割り当てがすべて返されます。注意点が 2 つあります。

1.  サブスクリプション レベルの読み取りアクセス権が必要です。
2.  サブスクリプションに役割の割り当てが多数ある場合は、すべて取得するまでに時間がかかることがあります。

特定の役割の定義向けに、特定のスコープで、特定のユーザーに対して行われた既存の役割の割り当てを確認することもできます。次のコマンドを入力します。

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

AD テナント内の特定のユーザー (リソース グループ「group1」に関する「所有者」役割の割り当てを持つユーザー) に関する役割の割り当てがすべて返されます。役割は、次の 2 つの場所から割り当てられます。

1.  ユーザーに対する、リソース グループに関する「所有者」の役割の割り当て。
2.  ユーザーに対する、このリソース グループの親 (この例ではサブスクリプション) に関する「所有者」役割の割り当て。特定のレベルのアクセス許可がある場合、そのすべての子に対する同じアクセス許可も付与されているからです。

このコマンドレットのパラメーターはすべて省略可能です。パラメーターを組み合わせて、さまざまなフィルターで役割の割り当てを確認することができます。

## <span id="create"></span></a>役割の割り当ての作成

役割の割り当てを作成するには、以下の点について考慮する必要があります。

-   役割の割り当て先:以下の Azure Active Directory コマンドレットを使って、AD テナント内に存在するユーザー、グループ、サービス プリンシパルを表示できます。

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

-   割り当てる役割:以下のコマンドレットを使って、サポートされている役割の定義を表示できます。

    `PS C:\> Get-AzureRoleDefinition`

-   割り当てるスコープ:スコープには 3 つのレベルがあります。

    -   現在のサブスクリプション
    -   リソース グループ。リソース グループの一覧を入手するには、`PS C:\> Get-AzureResourceGroup` と入力します。
    -   リソース。リソースの一覧を入手するには、`PS C:\> Get-AzureResource` と入力します。

次に、`New-AzureRoleAssignment` を使って役割の割り当てを作成します。次に例を示します。

-   次を使用すると、ユーザーに閲覧者として現在のサブスクリプション レベルでの役割の割り当てが作成されます。

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

-   次を使用すると、リソース グループ レベルでの役割の割り当てが作成されます。

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

-   次を使用すると、リソース レベルでの役割の割り当てが作成されます。

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <span id="verify"></span></a>アクセス許可の確認

ご使用のアカウントに役割の割り当てがあることを確認してから、次を実行することで、実際に役割の割り当てが付与したアクセス許可を確認することができます。

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

これらの 2 つのコマンドレットでは、読み取りのアクセス許可があるリソース グループまたはリソースのみが返されます。また、持っているアクセス許可も表示します。

続いて、`New-AzureResourceGroup` などの他のコマンドレットを実行しようとすると、アクセス許可がない場合にはアクセス拒否エラーを受け取ることがあります。

## <span id="next"></span></a>次のステップ

Windows PowerShell を使用した役割ベースのアクセス制御の管理の詳細と、関連トピックについては、以下を参照してください。

-   [Windows Azure での役割ベースのアクセス制御][Windows Azure での役割ベースのアクセス制御]
-   [Azure リソース マネージャー コマンドレット][Azure リソース マネージャー コマンドレット]:AzureResourceManager モジュール内のコマンドレットの使用法について説明します。
-   [リソース グループを使用した Azure リソースの管理][リソース グループを使用した Azure リソースの管理]:Azure 管理ポータルでリソース グループを作成して管理する方法について説明します。
-   [Azure ブログ][Azure ブログ]:Azure の新機能について説明しています。
-   [Windows PowerShell ブログ][Windows PowerShell ブログ]:Windows PowerShell の新機能について説明します。
-   ["Hey, Scripting Guy!"ブログ]["Hey, Scripting Guy!"ブログ]:実践で使えるヒントとテクニックを Windows PowerShell コミュニティから得られます。
-   [XPLAT CLI を使用した役割ベースのアクセス制御の構成][XPLAT CLI を使用した役割ベースのアクセス制御の構成]
-   [役割ベースのアクセス制御のトラブルシューティング][役割ベースのアクセス制御のトラブルシューティング]

  [Windows PowerShell]: /ja-jp/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [クロスプラットフォーム CLI]: /ja-jp/documentation/articles/xplat-cli-rbac.md "クロスプラットフォーム CLI"
  [Windows Management Framework 3.0]: http://www.microsoft.com/en-us/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/en-us/download/details.aspx?id=40855
  [How to install and configure Windows Azure PowerShell (Windows Azure PowerShell のインストールおよび構成方法)]: http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell/
  [Getting Started with Windows PowerShell (Windows PowerShell の概要)]: http://technet.microsoft.com/en-us/library/hh857337.aspx
  [Azure PowerShell のインストールおよび構成方法]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
  [リソース マネージャーでの Windows PowerShell の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/powershell-azure-resource-manager/
  [サブスクリプションへの接続]: #connect
  [既存の役割の割り当ての確認]: #check
  [役割の割り当ての作成]: #create
  [アクセス許可の確認]: #verify
  [次のステップ]: #next
  [Windows Azure での役割ベースのアクセス制御]: http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-configure/
  [Azure リソース マネージャー コマンドレット]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [リソース グループを使用した Azure リソースの管理]: http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure ブログ]: http://blogs.msdn.com/windowsazure
  [Windows PowerShell ブログ]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!"ブログ]: http://blogs.technet.com/b/heyscriptingguy/
  [XPLAT CLI を使用した役割ベースのアクセス制御の構成]: http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-xplat-cli/
  [役割ベースのアクセス制御のトラブルシューティング]: http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-troubleshooting/
