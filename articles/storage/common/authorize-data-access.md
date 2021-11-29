---
title: データ操作を承認する
titleSuffix: Azure Storage
description: Azure Storage でデータへのアクセスを承認するためのさまざまな方法について説明します。 Azure Storage では、Azure Active Directory、共有キー認証、または共有アクセス署名 (SAS) による承認がサポートされています。また、BLOB への匿名アクセスもサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 678dd934bfdece9a957595bbfb16a4e7c8e8d3de
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720399"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Azure Storage 内のデータへのアクセスを承認する

ストレージ アカウントのデータにアクセスするたびに、クライアント アプリケーションは HTTP/HTTPS で Azure Storage に要求を行います。 既定では、Azure Storage のすべてのリソースがセキュリティで保護されており、セキュリティ保護されたリソースへのすべての要求には承認される必要があります。 承認により、クライアント アプリケーションにはストレージ アカウント内のデータにアクセスするための適切なアクセス許可があることが保証されます。

次の表は、データへのアクセスを承認するために Azure Storage で用意されているオプションをまとめたものです。

| Azure の成果物 | 共有キー (ストレージ アカウント キー) | Shared Access Signature (SAS) | Azure Active Directory (Azure AD) | オンプレミス Active Directory Domain Services | 匿名のパブリック読み取りアクセス |
|--|--|--|--|--|--|
| Azure BLOB | [サポートされています](/rest/api/storageservices/authorize-with-shared-key/) | [サポートされています](storage-sas-overview.md) | [サポートされています](../blobs/authorize-access-azure-active-directory.md) | サポートされていません | [サポートされています](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [サポートされています](/rest/api/storageservices/authorize-with-shared-key/) | サポートされていません | [AAD ドメイン サービスでのみサポートされています](../files/storage-files-active-directory-overview.md) | [サポートされています。資格情報を Azure AD と同期する必要があります](../files/storage-files-active-directory-overview.md) | サポートされていません |
| Azure Files (REST) | [サポートされています](/rest/api/storageservices/authorize-with-shared-key/) | [サポートされています](storage-sas-overview.md) | サポートされていません | サポートされていません。 | サポートされていません。 |
| Azure キュー | [サポートされています](/rest/api/storageservices/authorize-with-shared-key/) | [サポートされています](storage-sas-overview.md) | [サポートされています](../queues/authorize-access-azure-active-directory.md) | サポートされていません | サポートされていません |
| Azure テーブル | [サポートされています](/rest/api/storageservices/authorize-with-shared-key/) | [サポートされています](storage-sas-overview.md) | [サポートされています](../tables/authorize-access-azure-active-directory.md) (プレビュー) | サポートされていません | サポートされていません |

ここでは、各認証オプションについて簡単に説明します。

- **BLOB、キュー、テーブル リソースへの要求を承認するための Azure Active Directory (Azure AD) 統合。** セキュリティと使いやすさを最適化するために、可能な場合は Azure AD 資格情報を使用して要求を承認することをお勧めします。 Azure AD 統合の詳細については、[BLOB](../blobs/authorize-access-azure-active-directory.md)、[キュー](../queues/authorize-access-azure-active-directory.md)、または[テーブル](../tables/authorize-access-azure-active-directory.md) リソースに関する記事を参照してください。

    Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ストレージ アカウント内の BLOB、キュー、テーブル リソースへのセキュリティ プリンシパルのアクセス許可を管理できます。 また、Azure の属性ベースのアクセス制御 (ABAC) を使用して、BLOB リソースの Azure ロールの割り当てに条件を追加することもできます。 RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。 ABAC の詳細については、「[Azure の属性ベースのアクセス制御 (Azure ABAC) とは (プレビュー)](../../role-based-access-control/conditions-overview.md)」を参照してください。

- Azure Files の **Azure Active Directory Domain Services (Azure AD DS) 認証**。 Azure Files では、Azure AD DS を介したサーバー メッセージ ブロック (SMB) の ID ベースの承認がサポートされています。 Azure RBAC を使用して、ストレージ アカウント内の Azure Files リソースへのクライアントのアクセスを細かく制御できます。 ドメイン サービスを利用した Azure Files 認証に関する詳細については、[概要](../files/storage-files-active-directory-overview.md)のページを参照してください。

- Azure Files 用の **オンプレミス Active Directory Domain Services (AD DS、またはオンプレミス AD DS) 認証**。 Azure Files では、AD DS を使用した SMB での ID ベースの承認がサポートされています。 AD DS 環境は、オンプレミス マシンまたは Azure VM でホストできます。 Files への SMB アクセスは、ドメインに参加しているマシン (オンプレミスまたは Azure) からの AD DS 資格情報を使用してサポートされます。 共有レベルのアクセス制御には Azure RBAC の組み合わせを、ディレクトリまたはファイル レベルのアクセス許可の適用には NTFS DACL を使用できます。 ドメイン サービスを利用した Azure Files 認証に関する詳細については、[概要](../files/storage-files-active-directory-overview.md)のページを参照してください。

- BLOB、ファイル、キュー、およびテーブル用の **共有キー認証**。 共有キーを使用するクライアントは、ストレージ アカウントのアクセス キーを使用して署名されたすべての要求にヘッダーを渡します。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key/)に関するページを参照してください。

    ストレージ アカウントの共有キー認証を許可しないようにすることができます。 共有キーの承認が許可されていない場合、クライアントは、そのストレージ アカウントのデータに対する要求を承認するために Azure AD を使用する必要があります。 詳細については、[Azure ストレージ アカウントの共有キーによる認可の禁止](shared-key-authorization-prevent.md)に関するページを参照してください。

- BLOB、ファイル、キュー、およびテーブル用の **共有アクセス署名**。 共有アクセス署名 (SAS) には、ストレージ アカウント内のリソースに対する制限付きの委任アクセス機能があります。 署名が有効な期間、または付与するアクセス許可に制約を追加すると、柔軟にアクセスを管理できます。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-sas-overview.md)」をご覧ください。

- コンテナーと BLOB 用の **匿名パブリック読み取りアクセス** 匿名アクセスが構成されている場合、クライアントは承認なしで BLOB データを読み取ることができます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/anonymous-read-access-configure.md)」を参照してください。

    ストレージ アカウントのパブリック読み取りアクセスを許可しないようにできます。 匿名パブリック読み取りアクセスが許可されていない場合、ユーザーは、匿名アクセスを有効にするようにコンテナーを構成することはできず、すべての要求が承認される必要があります。 詳細については、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](../blobs/anonymous-read-access-prevent.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Active Directory を使用して、[BLOB](../blobs/authorize-access-azure-active-directory.md)、[キュー](../queues/authorize-access-azure-active-directory.md)、または[テーブル](../tables/authorize-access-azure-active-directory.md) リソースへのアクセスを認可します。
- [共有キーを使用して承認する](/rest/api/storageservices/authorize-with-shared-key/)
- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
        