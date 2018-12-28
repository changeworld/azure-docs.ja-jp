---
title: RBAC を使用してコンテナーとキューへのアクセス権を管理する (プレビュー) - Azure Storage | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、アプリケーション サービス プリンシパル、またはマネージド サービス ID に、BLOB およびキュー データにアクセスするためのロールを割り当てます。 Azure Storage では、コンテナーとキューへのアクセス権に関して組み込みロールとカスタム ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: fce89cc754ac179054a60ce837949bb02b2102c6
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408699"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>RBAC を使用して Azure BLOB およびキューのデータへのアクセス権を管理する (プレビュー)

Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 コンテナーまたはキューへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 RBAC ロールが Azure AD ID に割り当てられると、指定されたスコープに応じて、その ID はそれらのリソースへのアクセスを許可されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure Storage リソースのアクセス権は、Azure portal、Azure コマンドライン ツール、Azure 管理 API を使用して割り当てることができます。 

Azure AD ID は、ユーザー、グループ、アプリケーション サービス プリンシパル、または Azure リソースのマネージド ID の場合があります。 セキュリティ プリンシパルは、ユーザー、グループ、またはアプリケーション サービス プリンシパルの場合があります。 [Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) は自動的に管理される ID であり、Azure Virtual Machines、関数アプリ、仮想マシン スケール セットなどで動作するアプリケーションから認証を行うために使用されます。 Azure AD の ID の概要については、「[Understand Azure identity solutions (Azure ID ソリューションについて)](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)」を参照してください。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>BLOB とキューの RBAC ロール

Azure Storage では、組み込みとカスタム両方の RBAC ロールがサポートされています。 これらの組み込み RBAC ロールは、Azure AD で使用するために Azure Storage によって提供されます。

- [ストレージ BLOB データ所有者 (プレビュー)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview):Azure Data Lake Storage Gen2 (プレビュー) で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者 (プレビュー)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者 (プレビュー)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データ共同作成者 (プレビュー)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者 (プレビュー)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview):Azure キューの読み取り専用アクセス許可を付与するために使用します。

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview)」を参照してください。

コンテナーとキューで使用するためのカスタム ロールを定義することもできます。 詳細については、[Azure のロールベースのアクセス制御のためのカスタム ロールを作成する方法](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)に関するページを参照してください。 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>セキュリティ プリンシパルへのロールの割り当て

RBAC ロールを Azure ID に割り当てて、ストレージ アカウントのコンテナーまたはキューへのアクセス許可を付与します。 ロールの割り当てのスコープは、ストレージ アカウントあるいは特定のコンテナーまたはキューに対して指定できます。 次の表では、組み込みロールによって付与されるアクセス権が、スコープに従ってまとめられています。

|Scope (スコープ)|BLOB データ所有者|BLOB データ共同作成者|BLOB データ閲覧者|キュー データ共同作成者|キュー データ閲覧者|
|---|---|---|---|---|---|
|サブスクリプション レベル|サブスクリプション内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|サブスクリプション内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス| サブスクリプション内のすべてのコンテナーと BLOB への読み取りアクセス|サブスクリプション内のすべてのキューへの読み取り/書き込みアクセス|サブスクリプション内のすべてのキューへの読み取りアクセス|
|リソース グループ レベル|リソース グループ内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|リソース グループ内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス|リソース グループ内のすべてのコンテナーと BLOB への読み取りアクセス|リソース グループ内のすべてのキューへの読み取り/書き込みアクセス|リソース グループ内のすべてのキューへの読み取りアクセス|
|ストレージ アカウント レベル|ストレージ アカウント内のすべてのコンテナーと BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理|ストレージ アカウント内のすべてのコンテナーと BLOB への読み取り/書き込みアクセス|ストレージ アカウント内のすべてのコンテナーと BLOB への読み取りアクセス|ストレージ アカウント内のすべてのキューへの読み取り/書き込みアクセス|ストレージ アカウント内のすべてのキューへの読み取りアクセス|
|コンテナー/キュー レベル|指定されたコンテナーとその BLOB の読み取り/書き込みアクセスと POSIX アクセス制御の管理。|指定されたコンテナーとその BLOB への読み取り/書き込みアクセス|指定されたコンテナーとその BLOB への読み取りアクセス|指定されたキューへの読み取り/書き込みアクセス|指定されたキューへの読み取りアクセス|

> [!NOTE]
> Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 Azure Storage の RBAC ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーまたはキューのレベルで割り当てることができます。

Azure Storage 操作を呼び出すために必要なアクセス許可の詳細については、「[Permissions for calling REST operations (REST 操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)」を参照してください。

次のセクションでは、ストレージ アカウントまたは個別のコンテナーに対してスコープが指定されたロールを割り当てる方法について説明します。

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>ストレージ アカウントに対してスコープが指定されたロールを Azure portal で割り当てる

ストレージ アカウント内のすべてのコンテナーまたはキューへのアクセスを許可する組み込みロールを Azure portal で割り当てるには:

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. ストレージ アカウントを選択し、**[アクセス制御 (IAM)]** を選択してアカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。

    ![ストレージのアクセス制御設定を示すスクリーン ショット](media/storage-auth-aad-rbac/portal-access-control.png)

1. **[ロールの割り当ての追加]** ボタンをクリックして新しいロールを追加します。
1. **[ロールの割り当ての追加]** ウィンドウで、Azure AD ID に割り当てるロールを選択します。 次に、そのロールの割り当て先となる ID を検索して見つけます。 たとえば次の図は、あるユーザーに割り当てられる**ストレージ BLOB データ閲覧者 (プレビュー)** ロールを示しています。

    ![RBAC ロールを割り当てる方法を示すスクリーン ショット](media/storage-auth-aad-rbac/add-rbac-role.png)

1. **[Save]** をクリックします。 ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば次の図は、今追加されたユーザーに、ストレージ アカウント内のすべての BLOB データに対する読み取りアクセス許可があることを示しています。

    ![ロールに割り当てられたユーザーの一覧を示すスクリーン ショット](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>コンテナーまたはキューに対してスコープが指定されたロールを Azure portal で割り当てる

> [!IMPORTANT]
> 階層型名前空間が有効なアカウントをまだ使用している場合、これは実行できません。

コンテナーまたはキューに対してスコープが指定された組み込みロールを割り当てる場合も、手順は同様です。 ここに示す手順では、コンテナーに対してスコープが指定されたロールを割り当てます。しかし、同じ手順に従って、キューに対してスコープが指定されたロールを割り当てることができます。 

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動し、そのアカウントの **[概要]** を表示します。
1. [サービス] で **[BLOB]** を選択します。 
1. 割り当てるロールの対象にするコンテナーを見つけて、コンテナーの設定を表示します。 
1. **[アクセス制御 (IAM)]** を選択して、コンテナーのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。

    ![コンテナーのアクセス制御設定を示すスクリーン ショット](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. **[ロールの割り当ての追加]** ボタンをクリックして新しいロールを追加します。
1. **[ロールの割り当ての追加]** ウィンドウで、Azure AD ID に割り当てるロールを選択します。 次に、そのロールの割り当て先となる ID を検索して見つけます。
1. **[Save]** をクリックします。 ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば次の図は、今追加されたユーザーに、*sample-container* という名前のコンテナーのデータに対する読み取りアクセス許可があることを示しています。

    ![ロールに割り当てられたユーザーの一覧を示すスクリーン ショット](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>次の手順

- RBAC の詳細については、「[ロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で RBAC ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-rest.md)
- ストレージ アプリケーション内からコンテナーおよびキューへのアクセスを承認する方法については、[Azure Storage アプリケーションでの Azure AD の使用](storage-auth-aad-app.md)に関するページを参照してください。
- Azure のコンテナーとキューの Azure AD 統合に関する詳細については、Azure Storage 用の Azure AD 認証のプレビューについて発表している [Azure Storage チームのブログ投稿](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)を参照してください。
