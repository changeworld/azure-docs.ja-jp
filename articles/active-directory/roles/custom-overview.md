---
title: Azure Active Directory のロールベースのアクセス制御 (RBAC) の概要
description: Azure Active Directory のロールの割り当てと制限スコープの部分について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 10/06/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 083b0da0152f82d75df2ca83a451b5672dc09002
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667898"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Azure Active Directory のロールベースのアクセス制御の概要

この記事では、Azure Active Directory (Azure AD) のロールベースのアクセス制御について説明します。 Azure AD ロールを使用すると、最小限の特権の原則に従って、管理者に詳細なアクセス許可を付与できます。 Azure AD の組み込みロールとカスタム ロールは、<bpt id="p1">[</bpt>Azure リソースのロールベースのアクセス制御システム<ept id="p1">](../../role-based-access-control/overview.md)</ept> (Azure ロール) と同様の概念で動作します。 <bpt id="p1">[</bpt>この 2 つのロールベースのアクセス制御システムの違い<ept id="p1">](../../role-based-access-control/rbac-and-directory-admin-roles.md)</ept>は次のとおりです。

- Azure AD ロールでは、Microsoft Graph API を使用して、ユーザー、グループ、アプリケーションなどの Azure AD リソースへのアクセスを制御します
- Azure ロールでは、Azure Resource Management を使用して、仮想マシンやストレージなどの Azure リソースへのアクセスを制御します

どちらのシステムにも、同様に使用されるロールの定義とロールの割り当ての概念が含まれています。 ただし、Azure AD ロールのアクセス許可を Azure カスタム ロールで使用することはできません。その逆も同様です。

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD のロールベースのアクセス制御を理解する
Azure AD では、2 種類のロールの定義がサポートされています。
* <bpt id="p1">[</bpt>組み込みのロール<ept id="p1">](./permissions-reference.md)</ept>
* <bpt id="p1">[</bpt>カスタム ロール<ept id="p1">](./custom-create.md)</ept>

組み込みロールは、一連のアクセス許可が固定された、すぐに使えるロールです。 これらのロールの定義は変更できません。 Azure AD では多数の<bpt id="p1">[</bpt>組み込みロール<ept id="p1">](./permissions-reference.md)</ept>がサポートされており、その数は増え続けています。 柔軟性を高め、高度な要件を満たすために、Azure AD では<bpt id="p1">[</bpt>カスタム ロール<ept id="p1">](./custom-create.md)</ept>もサポートされています。 カスタム Azure AD ロールを使用したアクセス許可の付与は、カスタム ロール定義の作成と、ロールの割り当てを使用した割り当てという 2 ステップのプロセスです。 カスタム ロールの定義は、プリセットの一覧から追加するアクセス許可のコレクションです。 これらのアクセス許可は、組み込みロールで使用されるものと同じアクセス許可です。  

カスタム ロールの定義を作成したら (または組み込みロールを使用する場合)、ロールの割り当てを作成することによって、ユーザーに割り当てることができます。 ロールの割り当てにより、指定したスコープでロール定義に含まれるアクセス許可がユーザーに付与されます。 この 2 ステップのプロセスにより、1 つのロール定義を作成し、異なるスコープで何度もそれを割り当てることができます。 スコープでは、ロール メンバーがアクセスできる Azure AD リソースのセットを定義します。 最も一般的なスコープは、組織全体 (org-wide) のスコープです。 カスタム ロールは、組織全体のスコープで割り当てることができます。つまり、ロール メンバーには組織内のすべてのリソースに対してロールのアクセス許可が付与されます。 カスタム ロールは、オブジェクトのスコープで割り当てることもできます。 オブジェクト スコープの例は単一のアプリケーションです。 同じロールを、あるユーザーに対しては組織内のすべてのアプリケーションについて割り当て、別のユーザーに対しては Contoso Expense Reports アプリだけのスコープで割り当てる、といったことができます。  

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>ユーザーがリソースへのアクセス権を持っているどうかを Azure AD が特定する方法

管理リソースへのアクセス権をユーザーが持っているかどうかを判断するために Azure AD が使用する手順の概要を次に示します。 この情報を使用して、アクセスに関する問題のトラブルシューティングを行います。

1. ユーザー (またはサービス プリンシパル) が、Microsoft Graph または Azure AD Graph エンドポイントへのトークンを取得します。
1. ユーザーは、発行されたトークンを使用して Microsoft Graph または Azure AD Graph 経由で Azure Active Directory (Azure AD) に対する API 呼び出しを行います。
1. 状況に応じて、Azure AD は次のいずれかのアクションを実行します。
   - ユーザーのアクセス トークン内の <bpt id="p1">[</bpt>wids 要求<ept id="p1">](../develop/access-tokens.md)</ept>に基づいて、ユーザーのロール メンバーシップを評価します。
   - アクションが実行されるリソースに対して直接またはグループ メンバーシップを介してユーザーに適用されるすべてのロールの割り当てを取得します。
1. Azure AD は、API 呼び出しでのアクションが、このリソースに対してユーザーが持っているロールに含まれるかどうかを判別します。
1. 要求されたスコープでのアクションを含むロールをユーザーが持っていない場合、アクセスは許可されません。 それ以外の場合、アクセスは許可されます。

## <a name="role-assignment"></a>ロール割り当て

ロールの割り当ては、Azure AD リソースへのアクセスを許可するために、特定の "*スコープ*" で "*セキュリティ プリンシパル*" に "*ロールの定義*" を関連付ける Azure AD リソースです。 アクセスは、ロールの割り当てを作成することによって許可され、ロールの割り当てを削除することによって取り消されます。 ロールの割り当ての核心は、次の 3 つの要素で構成されます。

- セキュリティ プリンシパル - アクセス許可を取得する ID。 ユーザー、グループ、またはサービス プリンシパルを指定できます。 
- ロールの定義 - アクセス許可のコレクション。 
- スコープ - アクセス許可を適用できる場所を制限する方法。

[ロールの割り当ての作成](manage-roles-portal.md)と[ロールの割り当ての一覧表示](view-assignments.md)は、Azure portal、Azure AD PowerShell、または Microsoft Graph API を使って行うことができます。 Azure CLI は Azure AD のロールの割り当てに対してはサポートされていません。

次の図では、ロールの割り当ての例を示します。 この例では、Contoso Widget Builder アプリ登録のスコープで、Chris にアプリ登録管理者のカスタム役割が割り当てられています。 割り当てにより、Chris には、この特定のアプリ登録に対してのみ、アプリ登録管理者の役割のアクセス許可が付与されます。

![ロールの割り当てはアクセス許可を適用する方法であり、3 つの部分で構成されます。](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>セキュリティ プリンシパル

セキュリティ プリンシパルは、Azure AD リソースへのアクセスを割り当てられるユーザー、グループ、またはサービス プリンシパルを表します。 "ユーザー" は、Azure Active Directory 内にプロファイルを持つ個人です。 グループは、isAssignableToRole プロパティが true に設定されている新しい Microsoft 365 またはセキュリティ グループです (現在はプレビュー段階)。 サービス プリンシパルは、アプリケーション、ホステッド サービス、自動ツールを使用して Azure AD リソースにアクセスするために作成された ID です。

### <a name="role-definition"></a>ロール定義

ロール定義またはロールは、アクセス許可のコレクションです。 ロール定義には、Azure AD リソースに対して実行できる操作 (作成、読み取り、更新、削除など) が列記されています。 Azure AD には 2 種類のロールがあります。

- Microsoft によって作成されて変更できない組み込みロール。
- 組織によって作成および管理されるカスタム ロール。

### <a name="scope"></a>Scope

スコープは、ロールの割り当ての一部として、許可されたアクションを特定のリソースのセットに制限する方法です。 たとえば、開発者にカスタム役割を割り当て、特定のアプリケーション登録の管理だけを許可する必要がある場合は、ロールの割り当てでスコープとして特定のアプリケーション登録を含めることができます。

ロールを割り当てるときには、次の種類のスコープのいずれかを指定します。

- Tenant
- <bpt id="p1">[</bpt>管理単位<ept id="p1">](administrative-units.md)</ept>
- Azure AD リソース

スコープとして Azure AD リソースを指定する場合は、次のいずれかを使用できます。

- Azure AD グループ
- エンタープライズ アプリケーション
- アプリケーションの登録

詳細については、「[Azure AD ロールを異なるスコープで割り当てる](assign-roles-different-scopes.md)」を参照してください。

## <a name="license-requirements"></a>ライセンスの要件

Azure AD の組み込みロールは無料で使用できますが、カスタム ロールには Azure AD Premium P1 ライセンスが必要です。 ご自分の要件に対して適切なライセンスを探すには、<bpt id="p1">[</bpt>一般公開されている Free および Premium エディションの機能比較<ept id="p1">](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)</ept>に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>Azure AD ロールを理解する<ept id="p1">](concept-understand-roles.md)</ept>
- <bpt id="p1">[</bpt>Azure AD ロールをユーザーに割り当てる<ept id="p1">](manage-roles-portal.md)</ept>
- <bpt id="p1">[</bpt>カスタム ロールを作成して割り当てる<ept id="p1">](custom-create.md)</ept>
