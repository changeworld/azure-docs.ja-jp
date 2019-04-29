---
title: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する | Microsoft Docs
description: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 12598f3866cd1041cdf3cb89dac985b8d2caafce
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148808"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する

Azure Storage は、Azure Active Directory (AD) を使用して BLOB サービスと Queue サービスの認証と承認をサポートします。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、またはアプリケーションのサービス プリンシパルにアクセスを許可します。 

Azure AD の資格情報を使用したユーザーまたはアプリケーションの認証は、セキュリティと使いやすさで他の承認手段よりも優れています。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることもできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。 Azure Storage アプリケーションでは、できる限り Azure AD 認証を使用することをお勧めします。

Azure AD の資格情報での認証と認可は、すべてのパブリック リージョンのすべての汎用および BLOB ストレージ アカウントに対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>BLOB とキューに対する Azure AD の概要

セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) が BLOB またはキューのリソースにアクセスしようとすると、BLOB が匿名アクセスに利用できる場合を除き、要求は承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 次に、そのトークンが BLOB サービスまたは Queue サービスへの要求の一部として渡され、指定されたリソースへのアクセスを承認するためにサービスによって使用されます。

認証手順には、セキュリティ プリンシパルに割り当てられた 1 つまたは複数の RBAC ロールが必要です。 BLOB およびキューのデータへの一般的なアクセス許可セットを含む RBAC ロールは、Azure Storage によって提供されます。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセスが決定されます。 Azure Storage 用の RBAC ロールの割り当てについては、[RBAC で Azure Storage データへのアクセス許可を管理する](storage-auth-aad-rbac.md)方法に関するページを参照してください。

承認の手順では、実行時にアプリケーションで OAuth 2.0 アクセス トークンを要求する必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure Functions アプリなどの Azure エンティティ内から実行されている場合、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して BLOB またはキューにアクセスできます。 Azure BLOB サービスまたは Queue サービスに対するマネージド ID によって作成された要求を承認する方法については、「[Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)」を参照してください。

Azure BLOB サービスまたは Queue サービスに対する要求を作成するネイティブ アプリケーションと Web アプリケーションは、Azure AD で認証することもできます。 アクセス トークンを要求し、それを使用して BLOB またはキューのデータへの要求を認可する方法については、[Azure Storage アプリケーションからの Azure AD による認証](storage-auth-aad-app.md)に関する記事をご覧ください。

## <a name="assigning-rbac-roles-for-access-rights"></a>アクセス権に対して RBAC ロールを割り当てる

Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB およびキューのデータへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 BLOB およびキューのデータにアクセスするためのカスタム ロールを定義することもできます。

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>BLOB とキューの組み込み RBAC ロール

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

組み込み RBAC ロールをセキュリティ プリンシパルに割り当てる方法を学習するには、次のいずれかの記事を参照してください。

- [Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)
- [RBAC と Azure CLI を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-cli.md)
- [RBAC と PowerShell を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-powershell.md)

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview)」を参照してください。 カスタム RBAC ロールの作成の詳細については、[Azure のロールベースのアクセス制御のためにカスタム ロールを作成する方法](../../role-based-access-control/custom-roles.md)に関するページを参照してください。

### <a name="access-permissions-for-data-operations"></a>データ操作用のアクセス許可

特定の BLOB サービスまたは Queue サービスの操作を呼び出すために必要なアクセス許可の詳細については、「[Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」 (BLOB とキューのデータ操作を呼び出すためのアクセス許可) を参照してください。

## <a name="resource-scope"></a>リソースのスコープ

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD アカウントでのアクセス データ

Azure portal、PowerShell、または Azure CLI 経由での BLOB またはキューのデータへのアクセスは、ユーザーの Azure AD アカウントを使用するか、アカウント アクセス キー (共有キー認証) を使用することによって承認できます。

### <a name="data-access-from-the-azure-portal"></a>Azure portal からのデータ アクセス

Azure portal では、Azure AD アカウントまたはアカウント アクセス キーのいずれかを使用して、Azure ストレージ アカウントの BLOB とキューのデータにアクセスできます。 Azure portal で使用する認証スキームは、お客様に割り当てられている RBAC ロールに応じて異なります。

BLOB またはキューのデータにアクセスしようとすると、Azure portal でポータルはまずお客様に **Microsoft.Storage/storageAccounts/listkeys/action** で RBAC ロールが割り当てられているかどうかを確認します。 このアクションを持つロールが割り当てられている場合、Azure portal は共有キー承認によって BLOB およびキューのデータにアクセスするためにアカウント キーを使用します。 このアクションを持つロールが割り当てられていない場合、Azure portal は Azure AD アカウントを使用してデータへのアクセスを試みます。

Azure AD アカウントを使用して、Azure portal から BLOB またはキューのデータにアクセスするには、次のステートメントの両方が真である必要があります。

- Azure Resource Manager の[リーダー](../../role-based-access-control/built-in-roles.md#reader)ロールが、少なくとも、ストレージ アカウント以上のレベルを範囲として割り当てられている。 **リーダー**役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。
- BLOB またはキューのデータへのアクセスを提供する組み込みまたはカスタムの RBAC ロールのいずれかが割り当てられています。

Azure portal では、コンテナーまたはキューに移動すると、使用中のスキーマが示されます。 ポータル内でのデータ アクセスの詳細については、「[Azure portal を使用した BLOB またはキュー データへのアクセス](storage-access-blobs-queues-portal.md)」を参照してください。

Azure AD アカウントを使用した Azure portal 内のデータ アクセスのためにユーザーにアクセス許可を割り当てる方法の詳細については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)」を参照してください。

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell または Azure CLI からのデータ アクセス

Azure CLI と PowerShell では、Azure AD の資格情報を使ったサインインをサポートします。 サインインした後、セッションはその資格情報で実行されます。 詳細については、「[Azure AD ID を使用し、CLI または PowerShell で BLOB とキューのデータにアクセスする](storage-auth-aad-script.md)」を参照してください。

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Azure Files での SMB を使用した Azure AD 認証

Azure Files はドメイン参加 VM に関してのみ SMB 経由での Azure AD 認証をサポートしています (プレビュー)。 Azure Files に対して SMB 経由で Azure AD を使用する詳細については、「[SMB を使用した Azure Files の Azure Active Directory 認証の概要 (プレビュー)](../files/storage-files-active-directory-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)
- [BLOB やキューにアクセスするためにアプリケーションから Azure Active Directory で認証を行う](storage-auth-aad-app.md)
- [Azure Active Directory ベースのアクセス制御の Azure Storage によるサポートの一般提供](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
