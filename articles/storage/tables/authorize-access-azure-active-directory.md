---
title: Active Directory を使用してテーブルへのアクセスを承認する (プレビュー)
titleSuffix: Azure Storage
description: Azure Active Directory (Azure AD) を使用して Azure テーブルへのアクセスを承認します (プレビュー)。 アクセス権に対して Azure ロールを割り当てる。 Azure AD アカウントでのアクセス データ。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e0bf6ebac7826346089b82962963d3596d1d4a8f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857913"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>Azure Active Directory を使用して Azure テーブルへのアクセスを承認する

Azure Storage では、Azure Active Directory (Azure AD) を使用してテーブル データへの要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 その後、そのトークンを、Table サービスに対する要求を承認するために使用できます。

Azure AD を使用して Azure Storage に対する要求を承認すると、共有キー承認よりも優れたセキュリティと使いやすさが実現されます。 Microsoft では、必要最小限の特権でアクセスできるようにするために、可能な場合はテーブル アプリケーションで Azure AD での承認を使用することをお勧めします。

Azure AD での承認は、すべてのパブリック リージョンおよび国内クラウド内のすべての汎用に対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

> [!IMPORTANT]
> Azure AD を使用したテーブルの承認は、現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="overview-of-azure-ad-for-tables"></a>テーブルに対する Azure AD の概要

セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) がテーブル リソースへのアクセスを試みた場合、要求が承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 次に、そのトークンが Table サービスへの要求の一部として渡され、指定されたリソースへのアクセスを承認するためにサービスによって使用されます。

認証の手順では、アプリケーションが実行時に OAuth 2.0 アクセス トークンを要求する必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure Functions アプリなどの Azure エンティティ内から実行されている場合、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用してテーブルにアクセスできます。 マネージド ID によって行われた要求を認可する方法については、[Azure リソースのマネージド ID を使用してテーブルのデータへのアクセスを認可する方法](authorize-managed-identity.md)に関する記事を参照してください。

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 テーブル データへの一般的なアクセス許可セットを含む Azure ロールは、Azure Storage によって提供されます。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 テーブル データにアクセスするための Azure ロールの割り当ての詳細については、「[テーブル データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)」を参照してください。

Azure Table サービスに対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用してアクセスを承認することもできます。 アクセス トークンを要求し、それを使用して要求を承認する方法については、[Azure Storage アプリケーションからの Azure AD による Azure Storage へのアクセスの承認](../common/storage-auth-aad-app.md)に関する記事をご覧ください。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Storage では、テーブル データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。 テーブル データにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

### <a name="resource-scope"></a>リソースのスコープ

セキュリティ プリンシパルに Azure RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。 より広い範囲で定義されている Azure RBAC ロールは、その下のリソースによって継承されます。

Azure テーブル リソースへのアクセスのスコープは、次のレベルで指定できます (最も狭いスコープから順に示します)。

- **個々のテーブル。** このスコープでは、ロールの割り当ては、指定されたテーブルに適用されます。
- **ストレージ アカウント。** このスコープでは、ロールの割り当ては、アカウントのすべてのテーブルに適用されます。
- **リソース グループ。** このスコープでは、ロールの割り当ては、リソース グループ内のすべてのストレージ アカウントのすべてのテーブルに適用されます。
- **サブスクリプション。** このスコープでは、ロールの割り当ては、サブスクリプション内のすべてのリソース グループ内のすべてのストレージ アカウントのすべてのテーブルに適用されます。
- **管理グループ。** このスコープでは、ロールの割り当ては、管理グループ内のすべてのサブスクリプション内のすべてのリソース グループ内のすべてのストレージ アカウントのすべてのテーブルに適用されます。

Azure RBAC ロールの割り当てのスコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」を参照してください。

### <a name="azure-built-in-roles-for-tables"></a>テーブル用の Azure 組み込みロール

Azure RBAC には、Azure AD と OAuth を使用してテーブル データへのアクセスを承認するための組み込みロールが用意されています。 Azure Storage のテーブルへのアクセス許可を付与する組み込みロールには次のものがあります。

- [ストレージ テーブル データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor): テーブル ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ テーブル データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-table-data-reader): テーブル ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。

Azure 組み込みロールをセキュリティ プリンシパルに割り当てる方法については、「[テーブル データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)」を参照してください。 Azure RBAC ロールとそのアクセス許可を一覧表示する方法については、「[Azure ロールの定義を一覧表示する](../../role-based-access-control/role-definitions-list.md)」を参照してください。

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../../role-based-access-control/role-definitions.md#control-and-data-actions)」を参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによるテーブル データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者** などの組み込みロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、Azure AD を介したそのアカウント内のテーブル データへのアクセスは提供されません。 ただし、ロールに **Microsoft.Storage/storageAccounts/listKeys/action** が含まれている場合、そのロールが割り当てられているユーザーは、アカウント アクセス キーを使った共有キーによる承認を介してストレージ アカウントのデータにアクセスできます。

データ サービスと管理サービスの両方に対する Azure Storage 用の Azure 組み込みロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。

### <a name="access-permissions-for-data-operations"></a>データ操作用のアクセス許可

特定の Table サービスの操作を呼び出すために必要なアクセス許可の詳細については、「[データ操作呼び出しのアクセス許可](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Storage 内のデータへのアクセスを承認する](../common/authorize-data-access.md)
- [テーブル データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)