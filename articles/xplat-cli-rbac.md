<properties
	pageTitle="Mac、Linux、WIndows 用の Azure CLI を使用したロールベースのアクセス制御の管理"
	description="Azure CLI を使用したロールベースのアクセス制御の管理"
	services=""
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tomfitz"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="tomfitz"/>

# Azure コマンドライン インターフェイス (Azure CLI) によるロールベースのアクセス制御の管理#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-rbac.md" title="Azure CLI">Azure CLI</a></div>

Azure ポータルと Azure Resource Manager AP のロールベースのアクセス制御 (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。この機能を使用すれば、特定のスコープで Active Directory のユーザー、グループ、またはサービス プリンシパルに役割を割り当てることで、アクセス権を付与できます。

このチュートリアルでは、Azure CLI を使用して RBAC を管理する方法について学習します。役割の割り当てを作成して確認するプロセスについて説明します。

**推定所要時間:** 15 分

## 前提条件 ##

Azure CLI を使用して RBAC を管理するには、事前に以下を用意しておく必要があります。

- Azure CLI バージョン 0.8.8 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[インストール](xplat-cli-install.md)」を参照してください。
- また、Azure CLI での Azure Resource Manager のセットアップと使用について詳しく理解するために、「[リソース マネージャーでの Azure CLI の使用 ](xplat-cli-azure-resource-manager.md)」を参照してください。

## このチュートリアルの内容 ##

* [サブスクリプションへの接続](#connect)
* [既存のロール割り当ての確認](#check)
* [ロールの割り当ての作成](#create)
* [アクセス許可の確認](#verify)
* [次のステップ](#next)

## <a id="connect"></a>サブスクリプションへの接続 ##

RBAC は Azure リソース マネージャーのみと協働するので、最初に以下のように入力して Azure リソース マネージャー モードに切り替えます。

    azure config mode arm

詳細については、「[リソース マネージャーでの Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。

ご使用の Azure サブスクリプションに接続するには、以下のように入力します。

    azure login -u <username>

コマンド ライン プロンプトで、ご使用の Azure アカウントのパスワード (社員 ID、学生 ID のみをサポート。**組織 ID **とも呼ばれる ) を入力します。Azure CLI は、このアカウントのご使用のサブスクリプションをすべて入手し、最初のサブスクリプションを既定として使用します。RBAC の使用時には、共同管理者であるか役割が割り当てられていることによってアクセス許可があるサブスクリプションのみを入手できることに注意してください。

サブスクリプションが複数ある場合に、別のサブスクリプションに切り替えるには、以下のように入力します。

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

詳細については、「[Azure CLI コマンド](azure-cli-arm-commands.md)」を参照してください。

## <a id="check"></a>既存のロールの割り当ての確認 ##

ここで、既にサブスクリプションに存在している役割の割り当てを確認しましょう。次のコマンドを入力します。

    azure role assignment list

サブスクリプション内の役割の割り当てがすべて返されます。注意点が 2 つあります。

1. サブスクリプション レベルの読み取りアクセス権が必要です。
2. サブスクリプションに役割の割り当てが多数ある場合は、すべて取得するまでに時間がかかることがあります。

特定の役割の定義向けに、特定のスコープで、特定のユーザーに対して行われた既存の役割の割り当てを確認することもできます。次のコマンドを入力します。

    azure role assignment list -g group1 --mail <user's email> -o Owner

AD テナント内の特定のユーザー (リソース グループ「group1」に関する「所有者」役割の割り当てを持つユーザー) に関する役割の割り当てがすべて返されます。役割は、次の 2 つの場所から割り当てられます。

1. ユーザーに対する、リソース グループに関する「所有者」の役割の割り当て。
2. ユーザーに対する、このリソース グループの親 (この例ではサブスクリプション) に関する「所有者」役割の割り当て。特定のレベルのアクセス許可がある場合、そのすべての子に対する同じアクセス許可も付与されているからです。

このコマンドレットのパラメーターはすべて省略可能です。パラメーターを組み合わせて、さまざまなフィルターで役割の割り当てを確認することができます。

## <a id="create"></a>ロールの割り当ての作成 ##

役割の割り当てを作成するには、以下の点について考慮する必要があります。

- 役割を割り当てるユーザー: 次の Azure Active Directory コマンドレットを使用して、AD テナントにいるユーザー、グループおよびサービス プリンシパルを確認できます。

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- 割り当てる役割: 次のコマンドレットを使用して、サポートされているロールの定義を表示できます。

    `azure role list`

- 割り当てるスコープ: スコープには 3 つのレベルがあります。

    - 現在のサブスクリプション
    - リソース グループ。リソース グループのリストを取得するには、「`azure group list`」と入力します。
    - リソース 。リソースのリストを取得するには、「`azure resource list`」と入力します。

次に、`azure role assignment create` ￼￼￼ を使ってロールの割り当てを作成します。次に例を示します。

 - 次を使用すると、ユーザーに閲覧者として現在のサブスクリプション レベルでの役割の割り当てが作成されます。

    `azure role assignment create --mail <user's email> -o Reader`

- 次を使用すると、リソース グループ レベルでの役割の割り当てが作成されます。

    `PS C:> azure role assignment create --mail <user's email> -o Contributor -g group1`

- 次を使用すると、リソース レベルでの役割の割り当てが作成されます。

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>アクセス許可の確認 ##

ご使用のアカウントに役割の割り当てがあることを確認してから、次を実行することで、実際に役割の割り当てが付与したアクセス許可を確認することができます。

    PS C:> azure group list
    PS C:> azure resource list

これらの 2 つのコマンドレットでは、読み取りのアクセス許可があるリソース グループまたはリソースのみが返されます。また、持っているアクセス許可も表示します。

他のコマンドレット (`azure group create` など) を実行する場合、アクセス許可がないとアクセス拒否エラーが表示されます。

## <a id="next"></a>次のステップ ##

Azure CLI を使用したロールベースのアクセス制御の管理の詳細と、関連トピックについては、以下を参照してください。

- [Azure CLI のインストールと構成](xplat-cli-install.md)
- [リソース マネージャーでの Azure CLI の使用](xplat-cli-azure-resource-manager.md)
- [Using Resource groups to manage your Azure resources (リソース グループを使用した Azure リソースの管理)](resource-groups-overview.md): Microsoft Azure 管理ポータルでリソース グループを作成して管理する方法について説明します。

<!---HONumber=July15_HO3-->