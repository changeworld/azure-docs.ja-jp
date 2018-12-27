---
title: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する (プレビュー) | Microsoft Docs
description: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証します (プレビュー)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: a918a44042df13c8b799d823656c4cd878f8f618
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426653"
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
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB、キュー、ファイル](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="get-started-with-azure-ad-for-storage"></a>ストレージに対する Azure AD の使用の開始

Azure Storage で Azure AD の統合を使用する最初のステップは、ストレージ データに対する RBAC のロールを、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパル) または Azure リソースに対するマネージド ID に割り当てることです。 RBAC のロールには、コンテナーとキューに対する共通のアクセス許可セットが含まれます。 Azure Storage に関する RBAC のロールについて詳しくは、「[Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md)」(RBAC でストレージ データへのアクセス権を管理する (プレビュー)) をご覧ください。

Azure AD を使ってアプリケーションのストレージ リソースへのアクセスを承認するには、コードから OAuth 2.0 アクセス トークンを要求する必要があります。 アクセス トークンを要求し、それを使って Azure Storage への要求を承認する方法については、「[Authenticate with Azure AD from an Azure Storage application (Preview)](storage-auth-aad-app.md)」(Azure Storage アプリケーションから Azure AD を使用して認証する (プレビュー)) をご覧ください。 マネージド ID を使用している場合は、「[Azure リソース (プレビュー) の Azure 管理 ID を使用した blob およびキューへのアクセス認証](storage-auth-aad-msi.md)」をご覧ください。

現在、Azure CLI と PowerShell は、Azure AD ID でのログインをサポートしています。 Azure AD ID を使ってログインすると、セッションはその ID で実行されるようになります。 詳しくは、「[Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)](storage-auth-aad-script.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
