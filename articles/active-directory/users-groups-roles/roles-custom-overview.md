---
title: アクセス許可をカスタマイズできる Azure 管理者ロールをプレビューする - Azure Active Directory | Microsoft Docs
description: ID 管理を委任するためのカスタム Azure AD ロールをプレビューします。 Azure portal、PowerShell、または Graph API で Azure ロールを管理します。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779390"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory でのカスタム管理者ロール (プレビュー)

この記事では、Azure Active Directory (Azure AD) での新しいカスタム RBAC (ロールベースのアクセス制御) とリソース スコープを理解する方法について説明します。 カスタム RBAC ロールでは、[組み込みロール](directory-assign-admin-roles.md)の基になっているアクセス許可が公開されるので、独自のカスタム ロールを作成して整理することができます。 リソース スコープを使用すると、カスタム ロールを割り当てることにより、すべてのリソース (すべてのアプリケーションなど) へのアクセスを許可することなく、一部のリソース (1 つのアプリケーションなど) を管理することができます。

カスタム RBAC ロールを使用したアクセス許可の付与は、2 つのステップからなるプロセスです。 最初に、カスタム ロールの定義を作成し、あらかじめ設定されているアクセス許可の一覧からアクセス許可をその定義に追加します。 これらは組み込みロールで使用されるものと同じアクセス許可です。 ロールを作成したら、ロールの割り当てを作成することによって、ロールをユーザーに割り当てます。 この 2 ステップのプロセスにより、1 つのロールを作成し、異なるスコープで何度もそれを割り当てることができます。 カスタム ロールは、ディレクトリ スコープで割り当てることも、オブジェクト スコープで割り当てることもできます。 オブジェクト スコープの例は単一のアプリケーションです。 これにより、同じロールを、Sally に対してはディレクトリ内のすべてのアプリケーションについて割り当て、Naveen に対しては Contoso Expense Reports アプリについてだけ割り当てる、といったことができます。

カスタム RBAC ロールの最初のリリースには、アプリ登録管理用のアクセス許可を割り当てるためにロールを作成する機能が含まれています。 今後、エンタープライズ アプリケーション、ユーザー、デバイスなど、組織のリソースに対するアクセス許可がさらに追加されます。

プレビュー機能:

- カスタム ロールを作成および管理し、組織全体のスコープでユーザーに割り当てるためのポータル UI の更新
- 以下のための新しいコマンドレットを含むプレビュー PowerShell モジュール:
  - カスタム ロールを作成して管理する
  - 組織全体またはアプリごとの登録スコープでカスタム ロールを割り当てる
  - 組織全体のスコープで組み込みロールを割り当てる (GA コマンドレットと同等)
  - Azure AD Graph API のサポート

Azure AD のロールベースのアクセス制御は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランで利用できます。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD のロールベースのアクセス制御を理解する

Azure AD のロールベースのアクセス制御を使用すると、1 種類の Azure AD リソースに対してのみ許可されたアクションを実行できる、カスタマイズされたロールを割り当てることができます。 Azure AD のロールベースのアクセスは、Azure のロールベースのアクセス制御 (Azure リソース アクセス用の [Azure RBAC](../../role-based-access-control/overview.md)) と同様の概念で動作しますが、Azure AD のロールベースのアクセス制御は Microsoft Graph に基づき、Azure RBAC は Azure Resource Manager に基づいています。 ただし、どちらのシステムの機能も基になっているのはロールの割り当てです。

### <a name="role-assignments"></a>ロールの割り当て

Azure AD のロールベースのアクセス制御を使用してアクセスを制御するには、**ロールの割り当て**を作成し、それを使ってアクセス許可を適用します。 ロールの割り当ては、次の 3 つの要素で構成されます。

- セキュリティ プリンシパル
- ロール定義
- リソースのスコープ

アクセスは、ロールの割り当てを作成することによって許可され、ロールの割り当てを削除することによって取り消されます。 [ロールの割り当ての作成](roles-create-custom.md)は、Azure portal、Azure AD PowerShell、および Graph API を使って行うことができます。 個別に[カスタム ロールの割り当てを表示する](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)ことができます。

次の図では、ロールの割り当ての例を示します。 この例では、Chris Green には SalesForce アプリケーションのスコープで[アプリケーション管理者](directory-assign-admin-roles.md#application-administrator)ロールが割り当てられています。 Chris は、別のロールを割り当てられていない限り、他のアプリケーションを管理するためのアクセス権を持っていません。

![ロールの割り当てはアクセス許可を適用する方法であり、3 つの部分で構成される](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>セキュリティ プリンシパル

セキュリティ プリンシパルは、Azure AD リソースへのアクセスを割り当てられるユーザーまたはサービス プリンシパルを表します。 "*ユーザー*" は、Azure Active Directory 内にプロファイルを持つ個人です。 "*サービス プリンシパル*" は、特定の Azure AD リソースにアクセスするためにアプリケーションまたはサービスによって使用されるセキュリティ ID です。

セキュリティ プリンシパルは、ユーザー名とパスワードまたは証明書を表す点でユーザー ID に似ていますが、ユーザーではなくアプリケーションまたはサービスに対するものです。

### <a name="role"></a>Role

ロール定義またはロールは、アクセス許可のコレクションです。 ロール定義には、Azure AD リソースに対して実行できる操作 (作成、読み取り、更新、削除など) が列記されています。 Azure AD には 2 種類のロールがあります。

- Microsoft によって作成されて変更できない組み込みロール。 グローバル管理者の組み込みロールには、すべての Azure AD リソースに対するすべてのアクセス許可があります。
- 組織によって作成および管理されるカスタム ロール。

### <a name="scope"></a>Scope (スコープ)

スコープとは、特定の Azure AD リソースに対して許可されるアクションの制限です。 ロールを割り当てるときに、特定のリソースに対して管理者が実行できるアクションを制限するスコープを指定できます。 たとえば、開発者にカスタム ロールを付与し、特定のアプリケーション登録の管理だけを許可したい場合は、ロールの割り当てでスコープとして特定のアプリケーション登録を含めることができます。

  > [!Note]
  > カスタム ロールは、ディレクトリ スコープとリソース スコープで割り当てることができます。 管理単位のスコープで割り当てることはまだできません。
  > 組み込みロールは、ディレクトリ スコープと、場合によっては管理単位のスコープで、割り当てることができます。 オブジェクト スコープで割り当てることはまだできません。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次の手順

- [Azure portal、Azure AD PowerShell、および Graph API](roles-create-custom.md) を使用して、カスタム ロールの割り当てを作成する
- [カスタム ロールの割り当てを表示する](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
