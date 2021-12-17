---
title: Active Directory を使用して BLOB へのアクセスを認可する
titleSuffix: Azure Storage
description: Azure Active Directory (Azure AD) を使用して Azure BLOB へのアクセスを認可します。 アクセス権に対して Azure ロールを割り当てる。 Azure AD アカウントでのアクセス データ。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9f52d0555e07a79956aed466e3f9a307946bbcf6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724142"
---
# <a name="authorize-access-to-blobs-using-azure-active-directory"></a>Azure Active Directory を使用して BLOB へのアクセスを認可する

Azure Storage では、Azure Active Directory (Azure AD) を使用して BLOB データへの要求を認可することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 その後、そのトークンを、Blob service に対する要求を認可するために使用できます。

Azure AD を使用して Azure Storage に対する要求を承認すると、共有キー承認よりも優れたセキュリティと使いやすさが実現されます。 Microsoft では、必要最小限の特権でアクセスできるようにするために、可能な場合は BLOB アプリケーションで Azure AD の認可を使用することをお勧めします。

Azure AD での承認は、すべてのパブリック リージョンおよび国内クラウド内のすべての汎用および BLOB ストレージ アカウントに対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

Blob Storage ではさらに、Azure AD 資格情報で署名された Shared Access Signature (SAS) を作成することもサポートされています。 詳細については、[Shared Access Signatures を使用したデータへの制限付きアクセスの許可](../common/storage-sas-overview.md)に関するページを参照してください。

## <a name="overview-of-azure-ad-for-blobs"></a>BLOB に対する Azure AD の概要

セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) が BLOB リソースにアクセスしようとする場合、BLOB を匿名アクセスに利用できる場合を除き、要求は認可される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 次に、そのトークンが Blob service への要求の一部として渡され、指定されたリソースへのアクセスを認可するためにサービスによって使用されます。

認証の手順では、アプリケーションが実行時に OAuth 2.0 アクセス トークンを要求する必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure Functions アプリなどの Azure エンティティ内から実行されている場合、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して BLOB データにアクセスできます。 Azure Blob service に対してマネージド ID によって行われた要求を承認する方法については、[Azure リソースのマネージド ID を使用した BLOB データへのアクセスの承認](authorize-managed-identity.md)に関するページを参照してください。

承認の手順では、要求を行うセキュリティ プリンシパルに 1 つまたは複数の Azure RBAC ロールを割り当てる必要があります。 詳細については、「[アクセス権の Azure ロールを割り当てる](#assign-azure-roles-for-access-rights)」を参照してください。

Azure Blob service に対する要求を作成するネイティブ アプリケーションと Web アプリケーションは、Azure AD でアクセスを認可することもできます。 アクセス トークンを要求し、それを使用して BLOB データへの要求を認可する方法については、[Azure Storage アプリケーションからの Azure AD による Azure Storage へのアクセスの認可](../common/storage-auth-aad-app.md)に関する記事をご覧ください。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、Azure RBAC を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB データへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 BLOB データにアクセスするためのカスタム ロールを定義することもできます。 BLOB アクセス用の Azure ロールの割り当ての詳細については、「[BLOB データにアクセスするための Azure ロールを割り当てる](../blobs/assign-azure-role-data-access.md)」を参照してください。

Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。 セキュリティ プリンシパルに割り当てられた RBAC ロールによって、そのプリンシパルが持つアクセス許可が決定されます。 BLOB アクセス用の Azure ロールの割り当ての詳細については、「[BLOB データにアクセスするための Azure ロールを割り当てる](../blobs/assign-azure-role-data-access.md)」を参照してください。

場合によっては、BLOB リソースへのきめ細かなアクセスを有効にしたり、ストレージ リソースに対するロールの割り当てが多数ある場合にアクセス許可を簡略化したりする必要があります。 Azure 属性ベースのアクセス制御 (Azure ABAC) を使用して、ロール割り当てに関する条件を構成できます。 [カスタム役割](../../role-based-access-control/custom-roles.md)で条件を使用したり、組み込みロールを選択したりすることができます。 ABAC を使用した Azure ストレージ リソースの条件の構成に関する詳細については、「[Azure ロールの割り当て条件を使用して BLOB へのアクセスを承認する (プレビュー)](../common/storage-auth-abac.md)」を参照してください。 BLOB データ操作でサポートされる条件の詳細については、「[Azure Storage 内での Azure のロールの割り当て条件のアクションと属性 (プレビュー)](../common/storage-auth-abac-attributes.md)」を参照してください。

### <a name="resource-scope"></a>リソースのスコープ

セキュリティ プリンシパルに Azure RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。 より広い範囲で定義されている Azure RBAC ロールは、その下のリソースによって継承されます。

Azure BLOB リソースへのアクセスのスコープは、次のレベルで指定できます (最も狭いスコープから順に示します)。

- **個々のコンテナー。** このスコープでは、ロールの割り当ては、コンテナー内のすべての BLOB と、コンテナーのプロパティおよびメタデータに適用されます。
- **ストレージ アカウント。** このスコープでは、ロールの割り当てはすべてのコンテナーとその BLOB に適用されます。
- **リソース グループ。** このスコープでは、ロールの割り当ては、リソース グループ内のすべてのストレージ アカウントのすべてのコンテナーに適用されます。
- **サブスクリプション。** このスコープでは、ロールの割り当ては、サブスクリプション内のすべてのリソース グループ内の、すべてのストレージ アカウントのすべてのコンテナーに適用されます。
- **管理グループ。** このスコープでは、ロールの割り当ては、管理グループ内のすべてのサブスクリプション内のすべてのリソース グループ内のすべてのストレージ アカウントのすべてのコンテナーに適用されます。

Azure RBAC ロールの割り当てのスコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」を参照してください。

### <a name="azure-built-in-roles-for-blobs"></a>BLOB 用の Azure 組み込みロール

Azure RBAC には、Azure AD と OAuth を使用して BLOB データへのアクセスを承認するために、多数の組み込みロールが用意されています。 Azure Storage のデータ リソースへのアクセス許可を付与するロールの例を次に示します。

- [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../../storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [Storage Blob デリゲータ](../../role-based-access-control/built-in-roles.md#storage-blob-delegator):Azure AD 資格情報で署名された、コンテナーまたは BLOB 用の共有アクセス署名を作成するために使用するユーザー委任キーを取得します。

Azure 組み込みロールをセキュリティ プリンシパルに割り当てる方法については、「[BLOB データにアクセスするための Azure ロールを割り当てる](../blobs/assign-azure-role-data-access.md)」を参照してください。 Azure RBAC ロールとそのアクセス許可を一覧表示する方法については、「[Azure ロールの定義を一覧表示する](../../role-based-access-control/role-definitions-list.md)」を参照してください。

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../../role-based-access-control/role-definitions.md#control-and-data-actions)」を参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによる BLOB データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者** などの組み込みロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、Azure AD を介したそのアカウント内の BLOB データへのアクセスは提供されません。 ただし、ロールに **Microsoft.Storage/storageAccounts/listKeys/action** が含まれている場合、そのロールが割り当てられているユーザーは、アカウント アクセス キーを使った共有キーによる承認を介してストレージ アカウントのデータにアクセスできます。 詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../../storage/blobs/authorize-data-operations-portal.md)」を参照してください。

データ サービスと管理サービスの両方に対する Azure Storage 用の Azure 組み込みロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。

### <a name="access-permissions-for-data-operations"></a>データ操作用のアクセス許可

特定の Blob service の操作を呼び出すために必要なアクセス許可の詳細については、「[データ操作呼び出しのアクセス許可](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)」を参照してください。

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD アカウントでのアクセス データ

Azure portal、PowerShell、または Azure CLI 経由での BLOB データへのアクセスは、ユーザーの Azure AD アカウントを使用するか、アカウント アクセス キー (共有キーによる認可) を使用することによって認可できます。

### <a name="data-access-from-the-azure-portal"></a>Azure portal からのデータ アクセス

Azure portal では、Azure AD アカウントまたはアカウント アクセス キーのいずれかを使用して、Azure ストレージ アカウントの BLOB データにアクセスできます。 Azure portal で使用する認証スキームは、お客様に割り当てられている Azure ロールに応じて異なります。

BLOB データにアクセスしようとすると、Azure portal によって、まず **Microsoft.Storage/storageAccounts/listkeys/action** を持つ Azure ロールが割り当てられているかどうかが確認されます。 このアクションを持つロールが割り当てられている場合、Azure portal では共有キー認可によって BLOB データにアクセスするためのアカウント キーが使用されます。 このアクションを持つロールが割り当てられていない場合、Azure portal は Azure AD アカウントを使用してデータへのアクセスを試みます。

Azure AD アカウントを使用して Azure portal から BLOB データにアクセスするには、その BLOB データにアクセスするためのアクセス許可が必要です。また、Azure portal でストレージ アカウント リソース内を移動するためのアクセス許可も必要です。 Azure Storage によって提供されている組み込みロールでは BLOB リソースへのアクセス権が付与されますが、ストレージ アカウント リソースへのアクセス許可は付与されません。 このため、ポータルへのアクセスには、スコープがストレージ アカウント以上のレベルに設定された、[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールなどの Azure Resource Manager ロールの割り当ても必要です。 **リーダー** 役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。 Azure AD アカウントを使用した Azure portal 内のデータ アクセスのためにユーザーにアクセス許可を割り当てる方法の詳細については、「[BLOB データへのアクセスのための Azure ロールを割り当てる](../blobs/assign-azure-role-data-access.md)」を参照してください。

Azure portal では、コンテナーに移動すると、どの認可スキームが使用されているかが示されます。 ポータルでのデータ アクセスの詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-data-operations-portal.md)」を参照してください。

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell または Azure CLI からのデータ アクセス

Azure CLI と PowerShell では、Azure AD の資格情報を使ったサインインをサポートします。 サインインした後、セッションはその資格情報で実行されます。 詳細については、次のいずれかの記事を参照してください。

- [Azure CLI で BLOB データへのアクセスの承認方法を選択する](authorize-data-operations-cli.md)
- [Azure AD 資格情報を使用して PowerShell コマンドを実行して BLOB データにアクセスする](authorize-data-operations-powershell.md)

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)| ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="next-steps"></a>次のステップ

- [Azure Storage 内のデータへのアクセスを認可する](../common/authorize-data-access.md)
- [BLOB データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)
