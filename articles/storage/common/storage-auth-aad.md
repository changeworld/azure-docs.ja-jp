---
title: Azure Active Directory を使用して Azure Storage へのアクセスを認証する (プレビュー) | Microsoft Docs
description: Azure Active Directory を使用して Azure Storage へのアクセスを認証します (プレビュー)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 90868961475c2e9d0ac7d28c5d9a50c8eb281675
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525207"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Azure Active Directory を使用して Azure Storage へのアクセスを認証する (プレビュー)

Azure Storage は、Azure Active Directory (AD) を使用して BLOB サービスと Queue サービスの認証と承認をサポートします。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、またはアプリケーションのサービス プリンシパルにアクセスを許可します。 

Azure AD を使用して Azure Storage にアクセスするアプリケーションを承認すると、他の承認オプションより優れた使いやすいセキュリティが提供されます。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 同様に、Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることはできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。

## <a name="about-the-preview"></a>プレビューについて

プレビューについて以下の点に注意してください。

- Azure AD の統合は、プレビューでのみ BLOB サービスと Queue サービス に対して使用できます。
- Azure AD の統合は、すべてのパブリック リージョンの GPv1、GPv2、および BLOB ストレージ アカウントで使用できます。 
- Resource Manager デプロイ モデルで作成されたストレージ アカウントのみがサポートされます。 
- Azure Storage Analytics のログでの呼び出し元 ID 情報のサポートは近日対応予定です。
- Standard ストレージ アカウントのリソースへのアクセスの Azure AD による承認は、現在サポートされています。 Premium ストレージ アカウントのページ BLOB に対するアクセスの承認は、近日中にサポートされるようになります。
- Azure Storage では、組み込みとカスタム両方の RBAC ロールがサポートされています。 サブスクリプション、リソース グループ、ストレージ アカウント、または個々のコンテナーやキューを対象としたロールを割り当てることができます。
- 現在 Azure AD の統合をサポートしている Azure Storage クライアント ライブラリは次のとおりです。
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (7.1.x-Preview を使用)
    - Python
        - [BLOB](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [キュー](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

> [!IMPORTANT]
> このプレビューは、非運用環境でのみの使用を意図しています。 運用サービス レベル アグリーメント (SLA) は、Azure Storage 用の Azure AD 統合の一般公開が宣言されるまで利用できません。 ご利用のシナリオで Azure AD 統合がサポートされていない場合、お使いのアプリケーションでは、共有キー承認か SAS トークンを引き続き使用します。
>
> プレビュー中、RBAC ロールの割り当ては反映に最大 5 分かかることがあります。
>
> Azure AD と Azure Storage の統合では、Azure Storage 操作に HTTPS を使用する必要があります。

## <a name="get-started-with-azure-ad-for-storage"></a>ストレージに対する Azure AD の使用の開始

Azure Storage で Azure AD の統合を使用する最初のステップは、ストレージ データに対する RBAC のロールを、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパル) またはマネージド サービス ID (MSI) に割り当てることです。 RBAC のロールには、コンテナーとキューに対する共通のアクセス許可セットが含まれます。 Azure Storage に関する RBAC のロールについて詳しくは、「[Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md)」(RBAC でストレージ データへのアクセス権を管理する (プレビュー)) をご覧ください。

Azure AD を使ってアプリケーションのストレージ リソースへのアクセスを承認するには、コードから OAuth 2.0 アクセス トークンを要求する必要があります。 アクセス トークンを要求し、それを使って Azure Storage への要求を承認する方法については、「[Authenticate with Azure AD from an Azure Storage application (Preview)](storage-auth-aad-app.md)」(Azure Storage アプリケーションから Azure AD を使用して認証する (プレビュー)) をご覧ください。 Azure マネージド サービス ID (MSI) を使っている場合は、「[Authenticate with Azure AD from an Azure VM Managed Service Identity (Preview)](storage-auth-aad-msi.md)」(Azure VM マネージド サービス ID から Azure AD を使用して認証する (プレビュー)) をご覧ください。

現在、Azure CLI と PowerShell は、Azure AD ID でのログインをサポートしています。 Azure AD ID を使ってログインすると、セッションはその ID で実行されるようになります。 詳しくは、「[Azure AD ID を使用し、CLI または PowerShell で Azure Storage にアクセスする (プレビュー)](storage-auth-aad-script.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure の BLOB とキューの Azure AD 統合に関する詳細については、Azure Storage チームのブログ投稿「[Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)」(Azure Storage の Azure AD Authentication のプレビューの発表) を参照してください。
