<properties pageTitle="Azure クロスプラットフォーム コマンド ライン インターフェイスによる役割ベースのアクセス制御の管理" metaKeywords="ResourceManager, Azure クロスプラットフォーム コマンド ライン インターフェイス, azure cli, azure コマンド ライン, azure cli, RBAC" description="クロスプラットフォームのコマンド ライン インターフェイスによる役割ベースのアクセス制御の管理" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# クロスプラットフォームのコマンド ライン インターフェイスによる役割ベースのアクセス制御の管理 #

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ja-jp/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">クロスプラットフォーム CLI</a></div>

Azure プレビュー ポータルと Azure リソース マネージャー API の役割ベースのアクセス制御 (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。この機能を使用すれば、特定のスコープで Active Directory のユーザー、グループ、またはサービス プリンシパルに役割を割り当てることで、アクセス権を付与できます。

このチュートリアルでは、Azure クロスプラットフォーム コマンド ライン インターフェイス (xplat-cli) を使用して RBAC を管理する方法を説明します。役割の割り当てを作成して確認するプロセスについて説明します。

**所要時間: ** 約 15 分

## 前提条件 ##

xplat-cli を使用して RBAC を管理する前に、以下を用意する必要があります。

- Azure クロスプラットフォーム コマンド ライン インターフェイス バージョン 0.8.8 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/)」を参照してください。
- また、次のチュートリアルを読み、Azure クロスプラットフォーム コマンド ライン インターフェイスでの Azure リソース マネージャーのセットアップと使用について詳しく理解してください。[リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli-azure-resource-manager/)

## このチュートリアルの内容 ##

* [サブスクリプションへの接続](#connect)
* [既存の役割の割り当ての確認](#check)
* [役割の割り当ての作成](#create)
* [アクセス許可の確認](#verify)
* [次のステップ](#next)

## <a id="connect"></a>サブスクリプションへの接続 ##

RBAC は Azure リソース マネージャーのみと協働するので、最初に以下のように入力して Azure リソース マネージャー モードに切り替えます。

    azure config mode arm

詳細については、「[リソース マネージャーでの Azure クロスプラットフォーム コマンド ライン インターフェイスの使用](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli-azure-resource-manager/)」を参照してください。

ご使用の Azure サブスクリプションに接続するには、以下のように入力します。

    azure login -u <ユーザー名>

コマンド ライン プロンプトで、ご使用の Azure アカウントのパスワード (組織 ID のみをサポート) を入力します。Xplat-cli は、このアカウントが所有するサブスクリプションをすべて取得し、最初のサブスクリプションを既定に使用します。RBAC の使用時には、共同管理者であるか役割が割り当てられていることによってアクセス許可があるサブスクリプションのみを入手できることに注意してください。 

サブスクリプションが複数ある場合に、別のサブスクリプションに切り替えるには、以下のように入力します。

    # これは、そのアカウントで管理するサブスクリプションを示します。
    azure account list
    # サブスクリプション名を使用して、目的のサブスクリプションを選択します。
    azure account set <サブスクリプション名>

詳細については、「[Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/)」を参照してください。

## <a id="check"></a>既存の役割の割り当ての確認 ##

ここで、既にサブスクリプションに存在している役割の割り当てを確認します。次のコマンドを入力します。

    azure role assignment list

サブスクリプション内の役割の割り当てがすべて返されます。注意点が 2 つあります。

1. サブスクリプション レベルの読み取りアクセス権が必要です。
2. サブスクリプションに役割の割り当てが多数ある場合は、すべて取得するまでに時間がかかることがあります。

特定の役割の定義向けに、特定のスコープで、特定のユーザーに対して行われた既存の役割の割り当てを確認することもできます。次のコマンドを入力します。

    azure role assignment list -g group1 --mail <ユーザーの電子メール> -o Owner

AD テナント内の特定のユーザー (リソース グループ "group1" に関する "所有者" 役割の割り当てを持つユーザー) に関する役割の割り当てがすべて返されます。役割は、次の 2 つの場所から割り当てられます。

1. ユーザーに対する、リソース グループに関する「所有者」の役割の割り当て。
2. ユーザーに対する、このリソース グループの親 (この例ではサブスクリプション) に関する「所有者」役割の割り当て。特定のレベルのアクセス許可がある場合、そのすべての子に対する同じアクセス許可も付与されているからです。

このコマンドレットのパラメーターはすべて省略可能です。パラメーターを組み合わせて、さまざまなフィルターで役割の割り当てを確認することができます。

## <a id="create"></a>役割の割り当ての作成 ##

役割の割り当てを作成するには、以下の点について考慮する必要があります。

- 役割の割り当て先: 以下の Azure Active Directory コマンドレットを使って、AD テナント内に存在するユーザー、グループ、サービス プリンシパルを表示できます。

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- 割り当てる役割: 以下のコマンドレットを使って、サポートされている役割の定義を表示できます。

    `azure role list`

- 割り当てるスコープ: スコープには 3 つのレベルがあります。

    - 現在のサブスクリプション
    - リソース グループ。リソース グループのリストを取得するには、「`azure group list`」と入力します。
    - リソースの一覧を取得するには、「`azure resource list`」と入力します。

`azure role assignment create` を使用して、役割の割り当てを作成します。次に例を示します。

 - 次を使用すると、ユーザーに閲覧者として現在のサブスクリプション レベルでの役割の割り当てが作成されます。

    `azure role assignment create --mail <user's email> -o Reader`

- 次を使用すると、リソース グループ レベルでの役割の割り当てが作成されます。

    `PS C:\> azure role assignment create --mail <ユーザーの電子メール> -o Contributor -g group1`

- 次を使用すると、リソース レベルでの役割の割り当てが作成されます。

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>アクセス許可の確認 ##

ご使用のアカウントに役割の割り当てがあることを確認してから、次を実行することで、実際に役割の割り当てが付与したアクセス許可を確認することができます。

    PS C:\> azure group list
    PS C:\> azure resource list

これらの 2 つのコマンドレットでは、読み取りのアクセス許可があるリソース グループまたはリソースのみが返されます。また、持っているアクセス許可も表示します。

続いて、`azure group create` などの他のコマンドレットを実行しようとすると、アクセス許可がない場合にはアクセス拒否エラーが発生します。

## <a id="next"></a>次のステップ ##

xplat-cli を使用した役割ベースのアクセス制御の管理の詳細と、関連トピックについては、以下を参照してください。

- [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/)
- [リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用](http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli-azure-resource-manager/)
- [リソース グループを使用した Azure リソースの管理](http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups): Azure の管理ポータルでリソース グループを作成および管理する方法について説明します。
- [Azure のブログ](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
