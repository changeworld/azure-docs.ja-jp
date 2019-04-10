---
title: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する (プレビュー) | Microsoft Docs
description: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証します (プレビュー)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012113"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する (プレビュー)

Azure Storage は、Azure Active Directory (AD) を使用して BLOB サービスと Queue サービスの認証と承認をサポートします。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、またはアプリケーションのサービス プリンシパルにアクセスを許可します。 

Azure AD の資格情報を使用したユーザーまたはアプリケーションの認証は、セキュリティと使いやすさで他の承認手段よりも優れています。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることもできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。 Azure Storage アプリケーションでは、できる限り Azure AD 認証を使用することをお勧めします。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>プレビューについて

プレビューについて以下の点に注意してください。

- Azure AD の統合は、プレビューでのみ BLOB サービスと Queue サービス に対して使用できます。
- Azure AD の統合は、すべてのパブリック リージョンの GPv1、GPv2、および BLOB ストレージ アカウントで使用できます。 
- Resource Manager デプロイ モデルで作成されたストレージ アカウントのみがサポートされます。 
- Azure Storage Analytics のログでの呼び出し元 ID 情報のサポートは近日対応予定です。
- Standard ストレージ アカウントのリソースへのアクセスの Azure AD による承認は、現在サポートされています。 Premium ストレージ アカウントのページ BLOB に対するアクセスの承認は、近日中にサポートされるようになります。
- Azure Storage では、組み込みとカスタム両方の RBAC ロールがサポートされています。 サブスクリプション、リソース グループ、ストレージ アカウント、または個々のコンテナーやキューを対象としたロールを割り当てることができます。
- 現在 Azure AD の統合をサポートしている Azure Storage クライアント ライブラリは次のとおりです。
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB、キュー、ファイル](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Azure AD for Storage の概要

Azure Storage で Azure AD の統合を使用する最初のステップは、ストレージ データに対する RBAC のロールを、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパル) または Azure リソースに対するマネージド ID に割り当てることです。 RBAC のロールには、コンテナーとキューに対する共通のアクセス許可セットが含まれます。 Azure Storage 用の RBAC ロールの割り当てについては、[RBAC で Azure Storage データへのアクセス許可を管理する (プレビュー)](storage-auth-aad-rbac.md) 方法に関するページを参照してください。

Azure AD を使ってアプリケーションのストレージ リソースへのアクセスを承認するには、コードから OAuth 2.0 アクセス トークンを要求する必要があります。 アクセス トークンを要求し、それを使って Azure Storage への要求を承認する方法については、「[Authenticate with Azure AD from an Azure Storage application (Preview)](storage-auth-aad-app.md)」(Azure Storage アプリケーションから Azure AD を使用して認証する (プレビュー)) をご覧ください。 マネージド ID を使用している場合は、「[Azure リソース (プレビュー) の Azure 管理 ID を使用した blob およびキューへのアクセス認証](storage-auth-aad-msi.md)」をご覧ください。

現在、Azure CLI と PowerShell は、Azure AD ID でのログインをサポートしています。 Azure AD ID を使ってログインすると、セッションはその ID で実行されるようになります。 詳しくは、「[Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)](storage-auth-aad-script.md)」をご覧ください。

## <a name="rbac-roles-for-blobs-and-queues"></a>BLOB とキューの RBAC ロール

Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 コンテナーまたはキューへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Azure portal で組み込みロールを割り当てる方法については、[Azure portal での RBAC を使用した Azure コンテナーとキューへのアクセス権の付与 (プレビュー)](storage-auth-aad-rbac.md) に関する記事を参照してください。

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC ロールによって付与されるアクセス許可 

次の表では、組み込みロールによって付与されるアクセス権が、スコープのレベルごとにまとめられています。

|Scope (スコープ)|BLOB データ所有者|BLOB データ共同作成者|BLOB データ閲覧者|キュー データ共同作成者|キュー データ閲覧者|
|---|---|---|---|---|---|
|サブスクリプション レベル|サブスクリプション内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|サブスクリプション内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス| サブスクリプション内のすべてのコンテナーと BLOB への読み取りアクセス|サブスクリプション内のすべてのキューへの読み取り/書き込みアクセス|サブスクリプション内のすべてのキューへの読み取りアクセス|
|リソース グループ レベル|リソース グループ内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|リソース グループ内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス|リソース グループ内のすべてのコンテナーと BLOB への読み取りアクセス|リソース グループ内のすべてのキューへの読み取り/書き込みアクセス|リソース グループ内のすべてのキューへの読み取りアクセス|
|ストレージ アカウント レベル|ストレージ アカウント内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|ストレージ アカウント内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス|ストレージ アカウント内のすべてのコンテナーと BLOB への読み取りアクセス|ストレージ アカウント内のすべてのキューへの読み取り/書き込みアクセス|ストレージ アカウント内のすべてのキューへの読み取りアクセス|
|コンテナー/キュー レベル|指定されたコンテナーとその BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理。|指定されたコンテナーとその BLOB への読み取り/書き込みアクセス|指定されたコンテナーとその BLOB への読み取りアクセス|指定されたキューへの読み取り/書き込みアクセス|指定されたキューへの読み取りアクセス|

Azure Storage 操作を呼び出すために必要なアクセス許可の詳細については、「[Permissions for calling REST operations (REST 操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
