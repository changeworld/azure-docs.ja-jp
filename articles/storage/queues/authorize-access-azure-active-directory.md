---
title: Active Directory を使用してキューへのアクセスを承認する
titleSuffix: Azure Storage
description: Azure Active Directory (Azure AD) を使用して Azure キューへのアクセスを承認します。 アクセス権に対して Azure ロールを割り当てる。 Azure AD アカウントでのアクセス データ。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bd9224537be7559a325b30a71fb72eab07a71bd0
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855367"
---
# <a name="authorize-access-to-queues-using-azure-active-directory"></a>Azure Active Directory を使用してキューへのアクセスを承認する

Azure Storage では、Azure Active Directory (Azure AD) を使用してキュー データへの要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 その後、そのトークンを、Queue サービスに対する要求を承認するために使用できます。

Azure AD を使用して Azure Storage に対する要求を承認すると、共有キー承認よりも優れたセキュリティと使いやすさが実現されます。 Microsoft では、必要最小限の特権でアクセスできるようにするために、可能な場合はキュー アプリケーションで Azure AD での承認を使用することをお勧めします。

Azure AD での承認は、すべてのパブリック リージョンおよび国内クラウド内のすべての汎用ストレージ アカウントに対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

## <a name="overview-of-azure-ad-for-queues"></a>キューに対する Azure AD の概要

セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) がキュー リソースへのアクセスを試みた場合、要求が承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 次に、そのトークンが Queue サービスへの要求の一部として渡され、指定されたリソースへのアクセスを承認するためにサービスによって使用されます。

認証の手順では、アプリケーションが実行時に OAuth 2.0 アクセス トークンを要求する必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure Functions アプリなどの Azure エンティティ内から実行されている場合、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用してキューにアクセスできます。 マネージド ID によって行われた要求を認可する方法については、[Azure リソースのマネージド ID を使用してキューのデータへのアクセスを認可する方法](authorize-managed-identity.md)に関する記事を参照してください。

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 キュー データへの一般的なアクセス許可セットを含む Azure ロールは、Azure Storage によって提供されます。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 キュー データにアクセスするための Azure ロールの割り当ての詳細については、「[キュー データにアクセスするための Azure ロールを割り当てる](../queues/assign-azure-role-data-access.md)」を参照してください。

Azure Queue サービスに対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用してアクセスを承認することもできます。 アクセス トークンを要求し、それを使用して要求を承認する方法については、[Azure Storage アプリケーションからの Azure AD による Azure Storage へのアクセスの承認](../common/storage-auth-aad-app.md)に関する記事をご覧ください。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Storage では、キュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。 キュー データにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

### <a name="resource-scope"></a>リソースのスコープ

セキュリティ プリンシパルに Azure RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。 より広い範囲で定義されている Azure RBAC ロールは、その下のリソースによって継承されます。

Azure キュー リソースへのアクセスのスコープは、次のレベルで指定できます (最も狭いスコープから順に示します)。

- **個々のキュー。** このスコープでは、ロールの割り当ては、キュー内のメッセージと、キューのプロパティおよびメタデータに適用されます。
- **ストレージ アカウント。** このスコープでは、ロールの割り当てはすべてのキューとそのメッセージに適用されます。
- **リソース グループ。** このスコープでは、ロールの割り当ては、リソース グループ内のすべてのストレージ アカウントのすべてのキューに適用されます。
- **サブスクリプション。** このスコープでは、ロールの割り当ては、サブスクリプション内のすべてのリソース グループ内のすべてのストレージ アカウントのすべてのキューに適用されます。
- **管理グループ。** このスコープでは、ロールの割り当ては、管理グループ内のすべてのサブスクリプション内のすべてのリソース グループ内のすべてのストレージ アカウントのすべてのキューに適用されます。

Azure RBAC ロールの割り当てのスコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」を参照してください。

### <a name="azure-built-in-roles-for-queues"></a>キュー用の Azure 組み込みロール

Azure RBAC には、Azure AD と OAuth を使用してキュー データへのアクセスを承認するために、多数の組み込みロールが用意されています。 Azure Storage のデータ リソースへのアクセス許可を付与するロールの例を次に示します。

- [ストレージ キュー データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):Azure キューの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データのメッセージ プロセッサ](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):Azure Storage キューのピーク、メッセージの取得と削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ メッセージ送信者](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):Azure Storage キューへのメッセージの追加アクセス許可を付与するために使用します。

Azure 組み込みロールをセキュリティ プリンシパルに割り当てる方法については、「[キュー データにアクセスするための Azure ロールを割り当てる](../queues/assign-azure-role-data-access.md)」を参照してください。 Azure RBAC ロールとそのアクセス許可を一覧表示する方法については、「[Azure ロールの定義を一覧表示する](../../role-based-access-control/role-definitions-list.md)」を参照してください。

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../../role-based-access-control/role-definitions.md#control-and-data-actions)」を参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによるキュー データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者** などの組み込みロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、Azure AD を介したそのアカウント内のキュー データへのアクセスは提供されません。 ただし、ロールに **Microsoft.Storage/storageAccounts/listKeys/action** が含まれている場合、そのロールが割り当てられているユーザーは、アカウント アクセス キーを使った共有キーによる承認を介してストレージ アカウントのデータにアクセスできます。 詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../../storage/queues/authorize-data-operations-portal.md)」を参照してください。

データ サービスと管理サービスの両方に対する Azure Storage 用の Azure 組み込みロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。

### <a name="access-permissions-for-data-operations"></a>データ操作用のアクセス許可

特定の Queue サービスの操作を呼び出すために必要なアクセス許可の詳細については、「[データ操作呼び出しのアクセス許可](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)」を参照してください。

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD アカウントでのアクセス データ

Azure portal、PowerShell、または Azure CLI 経由でのキュー データへのアクセスは、ユーザーの Azure AD アカウントを使用するか、アカウント アクセス キー (共有キーによる承認) を使用することによって承認できます。

### <a name="data-access-from-the-azure-portal"></a>Azure portal からのデータ アクセス

Azure portal では、Azure AD アカウントまたはアカウント アクセス キーのいずれかを使用して、Azure ストレージ アカウントのキュー データにアクセスできます。 Azure portal で使用する認証スキームは、お客様に割り当てられている Azure ロールに応じて異なります。

キュー データにアクセスしようとすると、最初に Azure portal によって、お客様に **Microsoft.Storage/storageAccounts/listkeys/action** で Azure ロールが割り当てられているかどうかが確認されます。 このアクションを持つロールが割り当てられている場合、Azure portal は共有キー承認によってキュー データにアクセスするためにアカウント キーを使用します。 このアクションを持つロールが割り当てられていない場合、Azure portal は Azure AD アカウントを使用してデータへのアクセスを試みます。

Azure AD アカウントを使用して Azure portal からキュー データにアクセスするには、そのキュー データにアクセスするためのアクセス許可が必要です。また、Azure portal でストレージ アカウント リソース内を移動するためのアクセス許可も必要です。 Azure Storage によって提供されている組み込みロールではキュー リソースへのアクセス権が付与されますが、ストレージ アカウント リソースへのアクセス許可は付与されません。 このため、ポータルへのアクセスには、スコープがストレージ アカウント以上のレベルに設定された、[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールなどの Azure Resource Manager ロールの割り当ても必要です。 **リーダー** 役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。 Azure AD アカウントを使用した Azure portal 内のデータ アクセスのためにユーザーにアクセス許可を割り当てる方法の詳細については、「[キュー データにアクセスするための Azure ロールを割り当てる](../queues/assign-azure-role-data-access.md)」または「[キュー データにアクセスするための Azure ロールを割り当てる](../queues/assign-azure-role-data-access.md)」を参照してください。

Azure portal では、キューに移動すると、どの承認スキームが使用されているかが示されます。 ポータル内でのデータ アクセスの詳細については、「[Azure portal でキュー データへのアクセスの承認方法を選択する](../queues/authorize-data-operations-portal.md)」と「[Azure portal でキュー データへのアクセスの承認方法を選択する](../queues/authorize-data-operations-portal.md)」を参照してください。

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell または Azure CLI からのデータ アクセス

Azure CLI と PowerShell では、Azure AD の資格情報を使ったサインインをサポートします。 サインインした後、セッションはその資格情報で実行されます。 詳細については、次のいずれかの記事を参照してください。

- [Azure CLI でキュー データへのアクセスの承認方法を選択する](authorize-data-operations-cli.md)
- [Azure AD 資格情報を使用して PowerShell コマンドを実行し、キューのデータにアクセスする](authorize-data-operations-powershell.md)

## <a name="next-steps"></a>次のステップ

- [Azure Storage 内のデータへのアクセスを承認する](../common/authorize-data-access.md)
- [キュー データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)