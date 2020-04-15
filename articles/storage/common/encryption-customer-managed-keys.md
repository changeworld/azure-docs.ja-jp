---
title: Azure Key Vault でカスタマー マネージド キーを使用してアカウントの暗号化を管理する
titleSuffix: Azure Storage
description: 独自の暗号化キーを使用して、ストレージ アカウントのデータを保護できます。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、アクセス制御をより柔軟に管理できます。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6b5712094b9821dfa041cd5ba8617e86f7231bde
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478010"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Azure Key Vault でカスタマー マネージド キーを使用して Azure Storage の暗号化を管理する

独自の暗号化キーを使用して、ストレージ アカウントのデータを保護できます。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、アクセス制御をより柔軟に管理できます。

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/key-vault-overview.md)」をご覧ください。

## <a name="about-customer-managed-keys"></a>カスタマー マネージド キーの概要

次の図は、カスタマー マネージド キーを使用して要求を行うために、Azure Storage で Azure Active Directory と Azure Key Vault をどのように使用するかを示しています。

![Azure Storage でのカスタマー マネージド キーのしくみを示す図](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

次の一覧では、図の番号付きの手順について説明します。

1. Azure Key Vault 管理者が、ストレージ アカウントに関連付けられているマネージド ID に暗号化キーへのアクセス許可を付与します。
2. Azure Storage 管理者が、ストレージ アカウントのカスタマー マネージド キーを使用して暗号化を構成します。
3. Azure Storage は、ストレージ アカウントに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。
4. Azure Storage は、Azure Key Vault のカスタマー キーを使用してアカウント暗号化キーをラップします。
5. 読み取り/書き込み操作の場合、Azure Storage は Azure Key Vault に要求を送信して、暗号化と暗号化解除の操作を実行するためにアカウント暗号化キーをラップ解除します。

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>キューとテーブルのカスタマー マネージド キーがサポートされるアカウントを作成する

カスタマー マネージド キーがストレージ アカウントに対して有効になっている場合、キューおよびテーブル サービスに保管されているデータは、カスタマー マネージド キーによって自動的には保護されません。 この保護に含めるストレージ アカウントを作成するときに、必要に応じてこれらのサービスを構成できます。

キューとテーブルのカスタマー マネージド キーがサポートされるストレージ アカウントを作成する方法について詳しくは、「[テーブルとキューのカスタマー マネージド キーがサポートされるアカウントを作成する](account-encryption-key-create.md)」を参照してください。

BLOB とファイル サービスのデータは、カスタマー マネージド キーがストレージ アカウントに対して構成されている場合、カスタマー マネージド キーによって常に保護されます。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>ストレージ アカウントのカスタマー マネージド キーを有効にする

カスタマー マネージド キーは、既存のストレージ アカウントでのみ有効にすることができます。 キー コンテナーは、ストレージ アカウントに関連するマネージド ID にキーのアクセス許可を付与するアクセス ポリシーでプロビジョニングする必要があります。 マネージド ID は、ストレージ アカウントが作成された後でのみ使用できます。

カスタマー マネージド キーを構成すると、Azure Storage は、関連するキー コンテナー内のカスタマー マネージド キーを使用して、アカウントのルート データ暗号化キーをラップします。 カスタマー マネージド キーを有効にしても、パフォーマンスには影響せず、すぐに有効になります。

カスタマー マネージド キーの有効化または無効化、キーのバージョンの更新、または別のキーの指定によって、Azure Storage の暗号化に使用されるキーを変更すると、ルート キーの暗号化は変わりますが、Azure ストレージ アカウントのデータを再暗号化する必要はありません。

カスタマー マネージド キーを有効または無効にした場合、あるいはキーまたはキーのバージョンを変更した場合、ルート暗号化キーの保護は変更されますが、Azure Storage アカウントのデータを再暗号化する必要はありません。

Azure Storage 暗号化用に Azure Key Vault でカスタマー マネージド キーを使用する方法については、次のいずれかの記事を参照してください。

- [Azure portal から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure AD の 1 つの機能である、Azure リソース用マネージド ID に依存します。 マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID がストレージ アカウントに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはストレージ アカウントを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのストレージ アカウントに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

ストレージ アカウントでカスタマー マネージド キーを有効にするには、Azure Key Vault を使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。

Azure Storage の暗号化では、2048 ビットの RSA と RSA-HSM キーのみがサポートされます。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーをローテーションするときは、新しいキー バージョンの URI を使用するようにストレージ アカウントを更新する必要があります。 Azure portal で新しいバージョンのキーを使用するようにストレージ アカウントを更新する方法については、「[Azure ポータルを使用して、Azure Storage 用にカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)」の「**キーのバージョンを更新する**」というタイトルのセクションを参照してください。

キーをローテーションしても、ストレージ アカウントのデータの再暗号化はトリガーされません。 ユーザーがこれ以上操作を行う必要はありません。

## <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーに対するストレージ アカウントのアクセス権は、いつでも取り消すことができます。 カスタマー マネージド キーに対するアクセス権が取り消された後、あるいはキーが無効化または削除された後、クライアントは BLOB またはそのメタデータに対して読み取りまたは書き込みを行う操作を呼び出すことができません。 次のいずれかの操作を呼び出そうとすると、すべてのユーザーに対してエラー コード 403 (許可されていません) が表示され失敗します。

- [List Blobs](/rest/api/storageservices/list-blobs) (要求 URI で `include=metadata` パラメーターを指定して呼び出す場合)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob) (`x-ms-meta-name` 要求ヘッダーを使用して呼び出す場合)
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block From URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Append Block From URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page From URL](/rest/api/storageservices/put-page-from-url)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)

これらの操作を再び呼び出すには、カスタマー マネージド キーへのアクセス権を復元します。

このセクションに記載されていないすべてのデータ操作は、カスタマー マネージド キーが取り消された後、あるいはキーが無効化または削除された後でも、引き続き実行できます。

カスタマー マネージド キーへのアクセス権を取り消すには、[PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) または [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys) を使用します。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure マネージド ディスク用のカスタマー マネージド キー

カスタマー マネージド キーは、Azure マネージド ディスクの暗号化の管理にも使用できます。 マネージド ディスクに対するカスタマー マネージド キーの動作は、Azure Storage リソースに対する動作とは異なります。 詳細については、Windows の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/windows/disk-encryption.md)」を参照してください。また、Linux の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/linux/disk-encryption.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure portal から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化用に Key Vault でカスタマー マネージド キーを構成する](storage-encryption-keys-cli.md)
- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
