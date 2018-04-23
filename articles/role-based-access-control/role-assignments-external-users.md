---
title: ロールベースのアクセス制御のカスタム ロールを作成して Azure の内部および外部ユーザーに割り当てる | Microsoft Docs
description: PowerShell と CLI を使用して作成したカスタム RBAC ロールを内部および外部ユーザーに割り当てます
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: d2eb39aa0a3fda7b543b6989cda937559f4d09ea
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="intro-on-role-based-access-control"></a>ロールベースのアクセス制御の概要

ロールベースのアクセス制御は Azure Portal 限定の機能です。この機能を利用すると、サブスクリプションの所有者は他のユーザーに詳細なロールを割り当てることができ、そのユーザーは環境内の特定のリソース スコープを管理できるようになります。

RBAC では、環境内の特定のリソースへのアクセスが必要なものの、インフラストラクチャ全体や課金に関連するスコープへのアクセスが必ずしも必要ではない外部のコラボレーター、ベンダー、フリーランサーと連携している大企業や SMB 向けの、優れたセキュリティ管理を実現できます。 RBAC では、管理者アカウント (サブスクリプション レベルでのサービス管理者ロール) によって管理される 1 つの Azure サブスクリプションの所有者を柔軟に設定できるほか、管理者権限を付与することなく、複数のユーザーを同じサブスクリプションでの業務に招待することができます。 管理や課金という観点から、RBAC 機能は、さまざまなシナリオで Azure を利用するための、時間効率と管理効率に優れた方法であることが証明されています。

## <a name="prerequisites"></a>前提条件
Azure 環境で RBAC を使用するには、以下のことが必要です。

* スタンドアロンの Azure サブスクリプションが所有者 (サブスクリプション ロール) としてユーザーに割り当てられている
* Azure サブスクリプションの所有者ロールがある
* [Azure Portal](https://portal.azure.com) にアクセスできる
* ユーザーのサブスクリプションに次のリソース プロバイダーが登録されていることを確認する: **Microsoft.Authorization**。 リソース プロバイダーの登録方法の詳細については、[Resource Manager のプロバイダー、リージョン、API のバージョン、およびスキーマ](../azure-resource-manager/resource-manager-supported-services.md)に関するページを参照してください。

> [!NOTE]
> Office 365 管理センターからプロビジョニングされた Office 365 サブスクリプションまたは Azure Active Directory ライセンス (例: Azure Active Directory へのアクセス) に RBAC の使用資格はありません。

## <a name="how-can-rbac-be-used"></a>RBAC の使用方法
RBAC は、Azure の 3 種類のスコープで適用できます。 最上位から最下位までの各スコープは以下のとおりです。

* サブスクリプション (最上位)
* リソース グループ
* リソース スコープ (個々の Azure リソース スコープに対象が設定されたアクセス許可を提供する最下位アクセス レベル)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>RBAC ロールをサブスクリプション スコープで割り当てる
RBAC が使用されるケースとして一般的な 2 つの例を次に示します (ただし、これらに限定されません)。

* 特定のリソースまたはサブスクリプション全体を管理するために組織の外部のユーザー (管理者ユーザーの Azure Active Directory テナントに属していないユーザー) を招待する場合
* 組織の内部のユーザー (管理者ユーザーの Azure Active Directory テナントに属しているユーザー) であるが、サブスクリプション全体または環境内の特定のリソース グループやリソース スコープへの詳細なアクセスを必要とする別のチームまたはグループに属しているユーザーと連携する場合

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Azure Active Directory の外部のユーザーにサブスクリプション レベルでアクセス権を付与する
RBAC ロールは、サブスクリプションの**所有者**のみが付与できます。 そのため、管理者は、このロールが事前に割り当てられているユーザーまたは Azure サブスクリプションを作成したユーザーとしてログインする必要があります。

管理者としてサインインした後、Azure Portal で [サブスクリプション] を選択し、目的のサブスクリプションを選択します。
![Azure Portal のサブスクリプション ブレード](./media/role-assignments-external-users/0.png) 既定では、管理者ユーザーが Azure サブスクリプションを購入している場合、そのユーザーが **[アカウント管理者]** として表示され、これがサブスクリプション ロールとなります。 Azure サブスクリプション ロールの詳細については、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](../billing/billing-add-change-azure-subscription-administrator.md)」を参照してください。

この例では、ユーザー "alflanigan@outlook.com" が AAD テナント "Default tenant Azure" の "無料試用版" サブスクリプションの**所有者**です。 このユーザーは初期 Microsoft アカウントが "Outlook" (Microsoft アカウント = Outlook、Live など) である Azure サブスクリプションの作成者なので、このテナントに追加される他のすべてのユーザーの既定のドメイン名は **"@alflaniganuoutlook.onmicrosoft.com"** となります。 仕様により、新しいドメインの構文は、テナントを作成したユーザーのユーザー名とドメイン名を組み合わせ、拡張子 **".onmicrosoft.com"** を追加することで構成されます。
さらに、新しいテナント用にカスタム ドメイン名を追加して確認すると、ユーザーはそのカスタム ドメイン名でサインインできます。 Azure Active Directory テナントのカスタム ドメイン名を確認する方法の詳細については、[ディレクトリへのカスタム ドメイン名の追加](/active-directory/active-directory-add-domain)に関する記事を参照してください。

この例では、"Default tenant Azure" ディレクトリに "@alflanigan.onmicrosoft.com" というドメイン名のユーザーのみが含まれています。

サブスクリプションの選択後、管理者ユーザーは **[アクセス制御 (IAM)]**、**[新しいロールの追加]** の順にクリックする必要があります。





![Azure Portal の [アクセス制御 (IAM)] 機能](./media/role-assignments-external-users/1.png)





![Azure Portal の [アクセス制御 (IAM)] 機能で新しいユーザーを追加する](./media/role-assignments-external-users/2.png)

次の手順では、割り当てるロールと RBAC ロールの割り当て先のユーザーを選択します。 管理者ユーザーの場合、**[ロール]** ドロップダウン メニューには Azure で利用できる組み込み RBAC ロールのみが表示されます。 各ロールとその割り当て可能なスコープの詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](built-in-roles.md)」を参照してください。

次に、管理者ユーザーは外部ユーザーのメール アドレスを追加する必要があります。 想定される動作として、既存のテナントに外部ユーザーが表示されません。 外部ユーザーを招待すると、そのユーザーは、**[サブスクリプション] の [アクセス制御 (IAM)]** 内に、現在サブスクリプション スコープで RBAC ロールが割り当てられているすべてのユーザーと共に表示されます。





![新しい RBAC ロールにアクセス許可を追加する](./media/role-assignments-external-users/3.png)





![サブスクリプション レベルでの RBAC ロールの一覧](./media/role-assignments-external-users/4.png)

ユーザー "chessercarlton@gmail.com" は、"無料試用版" サブスクリプションの**所有者**になるよう招待されています。 招待を送信すると、外部ユーザーにはアクティブ化リンクが含まれた確認メールが届きます。
![RBAC ロールについての招待メール](./media/role-assignments-external-users/5.png)

組織の外部のユーザーである新しいユーザーは、"Default tenant Azure" ディレクトリ内に既存の属性がありません。 属性は、外部ユーザーがディレクトリ内に記録されることに対して同意した後に作成されます。このディレクトリとは、外部ユーザーがロールを割り当てられたサブスクリプションに関連付けられているディレクトリです。





![RBAC ロールについての招待メールのメッセージ](./media/role-assignments-external-users/6.png)

この時点から外部ユーザーは Azure Active Directory テナントに外部ユーザーとして表示され、Azure Portal で確認できるようになります。





![ユーザー ブレード Azure Active Directory (Azure Portal)](./media/role-assignments-external-users/7.png)



**[ユーザー]** ビューでは、Azure Portal の異なるアイコンの種類によって外部ユーザーを認識できます。

ただし、**所有者**または**共同作成者**のアクセス権を**サブスクリプション** スコープで外部ユーザーに付与しても、**全体管理者**が許可しない限り、管理者ユーザーのディレクトリに外部ユーザーがアクセスすることはできません。 ユーザーのプロパティでは、**[メンバー]** と **[ゲスト]** という 2 つの共通パラメーターがある **[ユーザー タイプ]** を確認できます。 メンバーはディレクトリに登録されているユーザーであるのに対し、ゲストは外部ソースからディレクトリに招待されているユーザーです。 詳細については、「[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](../active-directory/active-directory-b2b-admin-add-users.md)」を参照してください。

> [!NOTE]
> ポータルで資格情報を入力した後に、外部ユーザーが正しいディレクトリを選択してサインインしていることを確認してください。 同じユーザーは、複数のディレクトリにアクセスすることができるほか、Azure Portal の右上にあるユーザー名をクリックすることで、ドロップダウン リストから適切なディレクトリを 1 つ選択することができます。

外部ユーザーは、ディレクトリのゲストとなっている間、Azure サブスクリプションのすべてのリソースを管理できますが、ディレクトリにはアクセスできません。





![Azure Active Directory へのアクセス制限 (Azure portal)](./media/role-assignments-external-users/9.png)

Azure Active Directory と Azure サブスクリプションには、他の Azure リソース (例: 仮想マシン、仮想ネットワーク、Web アプリ、ストレージなど) と Azure サブスクリプションとの間にあるような子と親の関係はありません。 後者はすべて Azure サブスクリプションの下で作成、管理、課金されますが、Azure ディレクトリへのアクセスの管理には Azure サブスクリプションが使用されます。 詳細については、[Azure サブスクリプションを Azure AD に関連付ける方法](/active-directory/active-directory-how-subscriptions-associated-directory)に関するページを参照してください。

すべての組み込み RBAC ロールのうち、**所有者**と**共同作成者**は環境内の全リソースへの完全な管理アクセスが可能ですが、共同作成者は新しい RBAC ロールを作成および削除できないという違いがあります。 **仮想マシン共同作成者**のような他の組み込みロールは、リソースの作成先となっている**リソース グループ**に関係なく、名前によって示されるリソースのみへの完全な管理アクセスが可能です。

**仮想マシン共同作成者**の組み込み RBAC ロールをサブスクリプション レベルで割り当てると、そのロールが割り当てられたユーザーは次のようになります。

* デプロイ日や属しているリソース グループに関係なく、すべての仮想マシンを表示できる
* サブスクリプション内の仮想マシンへの完全な管理アクセスができる
* サブスクリプション内の他の種類のリソースを表示できない
* 課金の観点からはどのような変更も加えることができない

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>組み込み RBAC ロールを外部ユーザーに割り当てる
このテストの別のシナリオ向けに、外部ユーザー "alflanigan@gmail.com" が**仮想マシン共同作成者**として追加されています。




![[仮想マシン共同作成者] 組み込みロール](./media/role-assignments-external-users/11.png)

この組み込みロールが割り当てられた外部ユーザーは、通常、仮想マシンと、それに隣接するデプロイ時に必要な Resource Manager 限定のリソースのみを表示および管理できます。 仕様により、これらの制限されたロールは、Azure Portal で作成された対応するリソースのみへのアクセスを提供します。



![Azure Portal の仮想マシン共同作成者ロールの概要](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>同じディレクトリのユーザーにサブスクリプション レベルでアクセス権を付与する
このプロセスのフローは、RBAC ロールを付与する管理者の観点でも、ロールへのアクセス権を付与されるユーザーの観点でも、外部ユーザーを追加するプロセスと同じです。 ここでの違いは、サインイン後にサブスクリプション内のすべてのリソース スコープがダッシュボードで利用できるようになるため、招待されるユーザーに招待メールが届かないことです。

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>リソース グループ スコープで RBAC ロールを割り当てる
RBAC ロールを**リソース グループ** スコープで割り当てるプロセスは、外部ユーザーと内部ユーザー (同じディレクトリに属しているユーザー)、どちらの種類のユーザーの場合でも、サブスクリプション レベルでロールを割り当てるプロセスと同じです。 RBAC ロールを割り当てられているユーザーの環境では、そのユーザーにアクセス権が割り当てられているリソース グループのみを、Azure Portal の **[リソース グループ]** アイコンから表示できます。

## <a name="assign-rbac-roles-at-the-resource-scope"></a>リソース スコープで RBAC ロールを割り当てる
Azure のリソース スコープで RBAC ロールを割り当てるプロセスは、サブスクリプション レベルやリソース グループ レベルでロールを割り当てるプロセスと同じであり、両方のシナリオと同じワークフローに従います。 ここでも、RBAC ロールが割り当てられているユーザーが表示できるのは、**[すべてのリソース]** タブに表示する場合も、ダッシュボードに直接表示する場合も、そのユーザーにアクセス権が割り当てられている項目のみです。

リソース グループ スコープまたはリソース スコープでの RBAC に共通する重要な点は、ユーザーが正しいディレクトリにサインインすることです。





![Azure Portal でのディレクトリ ログイン](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>RBAC ロールを Azure Active Directory グループに割り当てる
Azure の 3 種類のスコープで RBAC を使用するすべてのシナリオでは、割り当て済みユーザーとして各種リソースを管理およびデプロイする特権が与えられ、個人のサブスクリプションを管理する必要がありません。 割り当て済みユーザーによってそれ以降に作成されたすべてのリソースは、RBAC ロールが割り当てられているサブスクリプション、リソース グループ、またはリソース スコープに関係なく、ユーザーがアクセスできる 1 つの Azure サブスクリプションの下で課金されます。 この方法により、リソースの管理者がだれであっても、その Azure サブスクリプション全体について課金管理者のアクセス許可を付与されているユーザーが、使用量の概要を全体的に把握できます。

より大きな組織の場合は、RBAC ロールを同じ方法で Azure Active Directory グループに適用できます。これは、管理者ユーザーの観点で、各ユーザーに対して個別にではなく、チームまたは部門全体に対して詳細なアクセス権を付与する必要があると考えると、極めて時間効率および管理効率に優れた方法です。 この例を示すために、**共同作成者**ロールがテナント内の 1 つのグループにサブスクリプション レベルで追加されています。





![AAD グループ用の RBAC ロールを追加する](./media/role-assignments-external-users/14.png)

これらのグループは、Azure Active Directory 内でのみプロビジョニングされ、管理されるセキュリティ グループです。

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>サポート要求を開くためのカスタム RBAC ロールを PowerShell を使用して作成する
Azure で利用できる組み込みのロールでは、環境内の使用可能なリソースに基づき一定のアクセス許可レベルが保証されます。 ただし、組み込みのロールがニーズに適合しない場合は、カスタムのロールを作成できます。

カスタム ロールを作成するには、組み込みのロールから始めて、そのロールを編集して新しいロールを作成します。 この例では、組み込みの**閲覧者**ロールをカスタマイズして、ユーザーがサポート要求を開くことができるようにしています。

PowerShell では、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用して、**閲覧者**ロールを JSON 形式でエクスポートします。

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

閲覧者ロールの JSON 出力を次に示します。

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

次に、JSON 出力を編集してカスタム ロールを作成します。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

標準的なロールは、**Actions**、**NotActions**、**AssignableScopes** という 3 つのメイン セクションから構成されています。

**Actions** セクションには、このロールに許可されているすべての操作が記述されます。 この場合、サポート チケットを作成できるようにするには、**Microsoft.サポート/&ast;** 操作を追加する必要があります。 各操作は、リソース プロバイダーが使用を許可している点を理解しておくことが重要です。 リソース プロバイダーの操作一覧を取得するには、[Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) コマンドを使用するか、「[Azure Resource Manager Resource Provider operations](resource-provider-operations.md)」(Azure Resource Manager リソース プロバイダーの操作) を参照してください。

特定のロールのすべての操作を制限するには、リソース プロバイダーが **NotActions** セクションに記述されます。
ロールには、ロールが使用されるサブスクリプションの明示的なサブスクリプション ID が含まれていなければなりません。 サブスクリプション ID は **AssignableScopes** に記述されています。記述されていない場合は、ロールをサブスクリプションにインポートすることができません。

カスタム ロールを作成するには、[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) コマンドを使用して、更新された JSON ロール定義ファイルを指定します。

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

この例では、このカスタム ロールの名前は "Reader support tickets access level" です。 このロールが割り当てられたユーザーは、サブスクリプション内のすべてを閲覧し、サポート要求を開くこともできます。

> [!NOTE]
> ユーザーがサポート要求を開くことを許可できる組み込みロールは、**所有者**と**共同作成者**の 2 つのみです。 ユーザーがサポート要求を開けるようにするには、そのユーザーにサブスクリプション スコープを指定してロールを割り当てる必要があります。これは、すべてのサポート要求が Azure サブスクリプションに基づき作成されるためです。

新しいカスタム ロールは Azure Portal で使用できるようになり、ユーザーに割り当てることができます。

![Azure Portal にインポートされたカスタム ロールのスクリーンショット](./media/role-assignments-external-users/18.png)

![同じディレクトリのユーザーに対するインポート済みカスタム ロールの割り当てのスクリーンショット](./media/role-assignments-external-users/19.png)

![インポート済みカスタム ロールのアクセス許可のスクリーンショット](./media/role-assignments-external-users/20.png)

このカスタム ロールを持つユーザーは、新しいサポート要求を作成できるようになりました。

![サポート要求を作成するカスタム ロールのスクリーンショット](./media/role-assignments-external-users/21.png)

このカスタム ロールを持つユーザーは、VM の作成やリソース グループの作成など、他のアクションを実行できません。

![VM を作成できないカスタム ロールのスクリーンショット](./media/role-assignments-external-users/22.png)

![新しい RG を作成できないカスタム ロールのスクリーンショット](./media/role-assignments-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>サポート要求を開くためのカスタム RBAC ロールを Azure CLI を使用して作成する

Azure CLI を使用してカスタム ロールを作成する手順は、JSON 出力が異なる点を除き、PowerShell を使用する場合と似ています。

この例では、組み込みの**閲覧者**ロールから始めることができます。 閲覧者ロールのアクションを一覧表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) コマンドを使用します。

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

次の形式の JSON ファイルを作成します。 **Microsoft.Support/&ast;** 操作が **[アクション]** セクションに追加され、このユーザーは引き続き閲覧者でありながら、サポート要求を開くこともできます。 このロールが使用されるサブスクリプションの ID を **AssignableScopes** セクションに追加する必要があります。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

カスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az_role_definition_create) コマンドを使用します。

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

新しいカスタム ロールが Azure Portal で使用できるようになりました。このロールを使用するプロセスは、前述の PowerShell セクションと同じです。

![Azure Portal のスクリーンショット: CLI 1.0 を使用して作成されたカスタム ロール](./media/role-assignments-external-users/26.png)
