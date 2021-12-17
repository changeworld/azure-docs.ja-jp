---
title: Azure ロールベースのアクセス制御 (Azure RBAC) と Privileged Identity Management
titleSuffix: Azure Australia
description: オーストラリアのリージョン内で、オーストラリア政府のポリシー、規制、法令に固有の要件を満たす Azure ロールベースのアクセス制御 (Azure RBAC) と Privileged Identity Management を導入するためのガイダンス。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 819d8398395b9cccd4066b216cccd39a8a6fbfcc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2021
ms.locfileid: "117029044"
---
# <a name="azure-role-based-access-control-azure-rbac-and-privileged-identity-management-pim"></a>Azure ロールベースのアクセス制御 (Azure RBAC) と Privileged Identity Management (PIM)

管理特権を管理することは、IT 環境内でセキュリティを確保するうえでの重要なステップです。 最小限の特権によるセキュリティを使用して管理特権を制限することは、[ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) の要件となっており、[ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) リストのセキュリティ推奨事項にも組み込まれています。

Microsoft は、Microsoft Azure 内で、Just-In-Time および Just-Enough-Access を導入するための一連の制御機能を提供しています。 効果的なセキュリティ体制をクラウドに確保するためには、それらの制御機能を理解することが不可欠です。 このガイドでは、制御機能そのものの概要のほか、それらを導入する際に重要となる設計上の考慮事項について取り上げます。

## <a name="azure-rbac"></a>Azure RBAC

Azure ロールベースのアクセス制御 (Azure RBAC) は、Microsoft Azure 内のすべてのリソースに対するアクセスの管理と、Azure Active Directory (Azure AD) の管理の要となるものです。 Azure RBAC は、Azure で利用できる多数の補完的機能と共に導入できます。 この記事では、Azure 管理グループ、Azure Active Directory グループ、Azure Privileged Identity Management (PIM) を使用した効果的な RBAC の導入について、重点的に取り上げます。

Azure RBAC を導入するには、大きく分けて 3 つの構成要素が必要になります。

![図は、RBAC を導入するために必要な 3 つの構成要素、つまり、セキュリティ プリンシパル、ロール定義、スコープを表しています。これらはすべてロール割り当てにフィードされます。](media/rbac-overview.png)

* **セキュリティ プリンシパル**: セキュリティ プリンシパルとして、ユーザー、グループ、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) のいずれかを使用できます。 セキュリティ プリンシパルには、Azure Active Directory グループを使用して権限を割り当てる必要があります。

* **ロール定義**: ロール定義は、アクセス許可のコレクションであり、ロールとも呼ばれます。 それらのアクセス許可によって、ロールの定義に割り当てられたセキュリティ プリンシパルが実行できる操作が定義されます。 この機能は、Azure リソース ロールと Azure Active Directory 管理者ロールによって提供されます。 Azure には一連の組み込みロール (リンク) が備わっており、カスタム ロールでそれらのロールを拡張することができます。

* **スコープ**: スコープは、ロール定義の適用先となる Azure リソースのセットです。 Azure リソースには、Azure 管理グループを使用して Azure のロールを割り当てることができます。

これら 3 つの構成要素が組み合わさって、Azure 管理グループのスコープに該当する全リソースに対しての、ロールの定義で規定されたアクセス権がセキュリティ プリンシパルに与えられます。これを "ロールの割り当て" といいます。 1 つのセキュリティ プリンシパルにはロールの定義を複数割り当てることができ、1 つのスコープにはセキュリティ プリンシパルを複数割り当てることができます。

### <a name="azure-active-directory-groups"></a>Azure Active Directory グループ

個人やチームに権限を割り当てるときは、その割り当てを目的のユーザーに直接割り当てるのではなく、可能な限り Azure Active Directory グループにリンクさせることをお勧めします。 これは、Active Directory のオンプレミス導入から引き継がれた、同じ推奨プラクティスです。 Azure Active Directory グループは、作成した Azure 管理グループの論理構造を補完するものとなるよう、できるだけチームごとに作成するようにしてください。

ハイブリッド クラウドのシナリオでは、オンプレミスの Windows Server Active Directory セキュリティ グループを Azure Active Directory インスタンスと同期させることができます。 既にオンプレミスでそのような Windows Server Active Directory セキュリティ グループを使用して Azure RBAC を導入している場合、それらのグループを同期後に使用して Azure リソース用の Azure RBAC を導入できます。 それ以外の場合、ご利用のクラウド環境はクリーンな状態と考えられ、ご自分の Azure Active Directory 環境を中心に構築される信頼性の高い権限管理計画を立案し、導入することができます。

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure リソース ロールと Azure Active Directory 管理者ロールの比較

Microsoft Azure には、[Azure リソース](../role-based-access-control/built-in-roles.md)と [Azure Active Directory 管理](../active-directory/roles/permissions-reference.md)に使用されるさまざまな組み込みロールが用意されています。 どちらのタイプのロールも、Azure リソースに対して、または Azure AD 管理者のための粒度の細かい固有のアクセスを提供します。 Azure リソース ロールを使用して Azure AD への管理アクセス権を提供することはできないこと、そして Azure AD ロールは Azure リソースへの個別のアクセス権を提供するものではないことに注意してください。

組み込みロールを使用して Azure リソースに割り当てることができるアクセス権の種類について、いくつかの例を示します。

* あるユーザーにサブスクリプション内の仮想マシンの管理を許可し、別のユーザーに仮想ネットワークの管理を許可します
* DBA グループにサブスクリプション内の SQL データベースの管理を許可します
* あるユーザーに、仮想マシン、Web サイト、サブネットなど、リソース グループ内のすべてのリソースの管理を許可します
* あるアプリケーションに、リソース グループ内のすべてのリソースへのアクセスを許可します

Azure AD 管理用に割り当てることができるアクセス権の種類の例を次に示します。

* ユーザー パスワードのリセットをヘルプデスクのスタッフに許可する
* B2B コラボレーションに参加する外部ユーザーを Azure AD インスタンスに招待することをスタッフに許可する
* サインイン レポートと監査レポートへの読み取りアクセスを管理スタッフに許可する
* すべてのユーザーとグループの管理 (パスワードのリセットを含む) をスタッフに許可する。

必要以上のアクセス権を与えることがないよう、組み込みロールによって許可される一連の操作をすべて、時間をかけて理解することが大切です。 一連の組み込みロールとそれによって提供されるアクセス権は絶えず進化しています。ロールとその定義の全一覧を確認するには、前述のリンク先のドキュメントを参照するか、次の Azure PowerShell コマンドレットを使用してください。

```PowerShell
Get-AzRoleDefinition
```

```output
Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

または次の Azure CLI コマンドを使用します。

```azurecli-interactive
az role definition list
```

```output
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

必要に応じてカスタム Azure リソース ロールを作成することもできます。 それらのカスタム ロールは、Azure portal、PowerShell、Azure CLI のいずれかで作成できます。 カスタム ロールを作成する際は、ロールの目的が重複しないこと、また、その機能が既存の Azure リソース ロールによってまだ提供されていないことの確認が不可欠です。 そうすることで日常の管理の複雑さが軽減され、セキュリティ プリンシパルに不要な権限が割り当てられるリスクも軽減されます。 たとえば、組み込みの Azure リソース ロールである "仮想マシン共同作成者" と "仮想マシンの管理者ログイン" の間に位置するカスタム Azure リソース ロールを作成するケースが考えられます。

このカスタム ロールは、既存の共同作成者ロールをベースにすることができます。共同作成者ロールでは、次のアクセス権が与えられます。

| Azure リソース | アクセス レベル |
| --- | --- |
| Virtual Machines | 管理はできるものの、アクセスは不可 |
| VM にアタッチされた仮想ネットワーク | アクセス不可 |
| VM にアタッチされたストレージ | アクセス不可 |
|

このカスタム ロールでは、この基本的なアクセス権を引き継ぎつつ、特定のユーザーに対して、仮想マシンのネットワーク構成を変更するための基本的な権限を追加で許可するケースが考えられます。

Azure リソース ロールには、Azure 管理グループを介してリソースに割り当てることができるという利点もあります。

### <a name="azure-management-groups"></a>Azure 管理グループ

Azure 管理グループは、Azure テナント内のすべてのサブスクリプションとそのリソースへのロールの割り当てを組織が管理する目的で使用できます。 社内の組織構造を Azure 内で階層的にマップする機能など、管理階層を作成できるように Azure 管理グループは設計されています。 組織の事業単位を独立した論理エンティティとして作成することにより、組織内で各チームの特定の要件に基づいてアクセス許可を適用できるようになります。 Azure 管理グループを使用して、深さが最大 6 レベルの管理階層を定義することができます。

![管理グループ](media/management-groups.png)

Azure 管理グループは、Azure テナント内の Azure サブスクリプションにマップされます。 これにより、組織は、特定の事業単位に属する Azure リソースを分離し、コスト管理と権限割り当ての両方をきめ細かく制御することができます。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft では、Azure Privileged Identity Management を通じて、Just-In-Time (JIT) および Just-Enough-Access (JEA) を導入しています。 管理スタッフはこのサービスを利用して、Azure リソースへの特権アクセスの制御、管理、監視を行うことができます。 PIM では、管理スタッフがセキュリティ プリンシパルをロールの "対象 (有資格者)" にすることができ、ユーザーは、Azure portal または PowerShell コマンドレットを使用してロールのアクティブ化を要求することができます。 既定では、ロールの割り当てを一定期間 (1 時間から 72 時間) アクティブ化できます。 ロールの割り当ては、必要に応じてユーザーが延長を要求できるほか、ロールの割り当てを永続化するオプションも存在します。 資格のあるロールのアクティブ化をユーザーから要求されたときに、必要に応じて、Multi-Factor Authentication の要件を適用することもできます。 ロールのアクティブ化の割り当て期間が経過した後は、ロールによってセキュリティ プリンシパルに与えられていた特権アクセスが失効します。

Just-In-Time アクセスが使用されていない環境や特権の割り当てが日常的に監査されていない環境で起こり得る特権の割り当てに伴う問題は、PIM を使用することで回避できます。 よくある問題の 1 つは、昇格された特権の割り当てが忘れられ、昇格された特権を必要とするタスクが完了した後も、長期にわたりそのままの状態で放置されることです。 また、同様のセキュリティ プリンシパルを別途構成する際に、セキュリティ プリンシパルに割り当てられていたアクセス権が複製されることで、昇格された特権が環境内に増えていってしまう問題もあります。

## <a name="key-design-considerations"></a>設計上の主な考慮事項

最小限の特権によるセキュリティ運用を目指して Azure RBAC 戦略を立てる場合、次のセキュリティ要件を考慮する必要があります。

* 特権アクセスの要求を検証する
* 特定の職務を遂行するうえで必要な最小限のアクセス権に管理特権を制限する
* 特定の職務を遂行するうえで必要な最低限の期間に管理特権を制限する
* 許可された管理特権の定期レビューを実施する

Azure RBAC 戦略を立てるプロセスでは、業務の詳細なレビューを通じて、個別のビジネス ロール間のアクセス権の違いを把握すると共に、昇格された特権を必要とする作業の種類と頻度を理解する必要があります。 バックアップ オペレーター、セキュリティ管理者、監査担当者の各業務の違いにより、さまざまなタイミングで、さまざまなレベルのアクセス権が必要となり、継続的に行うレビューのレベルも多岐にわたります。

## <a name="validate-requests-for-access"></a>アクセス権の要求を検証する

昇格された特権は、明示的に承認する必要があります。 そのためには、承認プロセスを策定し、新たな権限に対する要求がすべて正当なものであることの検証を担当する適切なスタッフを配置しなければなりません。 Privileged Identity Management には、ロールの割り当てに関して複数の承認方法が用意されています。 ロールのアクティブ化要求は、自己承認を許可するように構成できるほか、関門を設けて、任命された承認者がロールのアクティブ化要求すべてを手動でレビューして承認しなければならないように構成することもできます。 アクティブ化要求に追加の関連情報 (チケット番号など) を含めることを必須とするようにアクティブ化要求を構成することもできます。

### <a name="restrict-privilege-based-on-duties"></a>職務に基づいて特権を制限する

過剰な特権の割り当てが IT セキュリティの一般的な攻撃ベクトルであるため、セキュリティ プリンシパルに付与される特権のレベルを制限することが重要です。 日常業務に最低限必要なレベルの特権を割り当てることができるように、管理の対象となるリソースの種類と担当チームを評価する必要があります。 日常業務に必要なレベルを超えた特権の追加は、特定のタスクを遂行するために必要な期間に限定して許可する必要があります。 たとえば、顧客の管理者に "共同作成者" のアクセス権を与えたうえで、一時的に高レベルなアクセスを必要とする特定のタスクについては、Azure リソースの "所有者" アクセス許可を要求できるようにする措置が考えられます。

そうすれば、個々の管理者に与えられる昇格されたアクセス権は、確実に最短期間に限定されます。 こうしたプラクティスを順守することで、組織の IT インフラストラクチャに対する攻撃面全体を小さくすることができます。

### <a name="regular-evaluation-of-administrative-privilege"></a>管理特権の定期評価

現時点で適切なレベルの特権が割り当てられていることを確認するために、環境内のセキュリティプリンシパルを定期的に監査することがきわめて重要です。 Microsoft Azure には、Azure のセキュリティ プリンシパルに割り当てられた特権を監査したり評価したするための手段が多数用意されています。 Privileged Identity Management を通じて管理スタッフが、セキュリティ プリンシパルに付与されたロールの "アクセス レビュー" を定期的に実施できます。 アクセス レビューは、Azure リソース ロールの割り当てと Azure Active Directory 管理者ロールの割り当ての両方を監査する目的で実施できます。 アクセス レビューは、次のプロパティで構成できます。

* **レビューの名前およびレビューの開始日と終了日**: レビューには、指名されたユーザーがレビューを完了できるだけの期間を設ける必要があります。

* **レビュー対象のロール**: 各アクセス レビューでは、個別の Azure ロールに取り組みます。

* **指名されたレビュー担当者**: レビューを実行するための 3 つのオプションがあります。 他のユーザーにレビューを割り当てて完了してもらうか、自分でレビューするか、または各ユーザーが自身のアクセスを確認できます。

* **アクセスの理由を提供するようユーザーに要求**: アクセス レビューを実行するとき、特権レベルを維持する理由を入力するようユーザーに要求できます。

承認待ちになっているアクセス レビューの進行状況は、Azure portal のダッシュボードから随時監視できます。 レビュー対象ロールへのアクセスは、アクセス レビューが完了するまで変更されません。 特定の期間に PIM で行われたすべてのユーザー割り当てとアクティブ化を[監査](../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md)することもできます。

## <a name="next-steps"></a>次のステップ

[Azure Australia におけるシステム監視](system-monitor.md)に関する記事を参照してください。
