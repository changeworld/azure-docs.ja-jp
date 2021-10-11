---
title: Azure Key Vault のマネージド ストレージ アカウント キーについて - Azure Key Vault
description: Azure Key Vault のマネージド ストレージ アカウント キーの概要。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390655"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>Azure Key Vault のマネージド ストレージ アカウント キーについて

> [!IMPORTANT]
> Azure Storage と Azure Active Directory (Azure AD) の統合 (Microsoft のクラウドベースの ID およびアクセス管理サービス) を使用することをお勧めします。 Azure AD 統合は [Azure BLOB およびキュー](../../storage/blobs/authorize-access-azure-active-directory.md)で利用できます。また、Azure Key Vault と同様に、Azure Storage へのトークンベースの OAuth2 アクセスが提供されます。 Azure AD では、ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証することができます。 Azure で実行するときは、[Azure AD マネージド ID](../../active-directory/managed-identities-azure-resources/index.yml) を使用できます。 マネージド ID を使用すると、クライアント認証やアプリケーションでの資格情報の保存が不要になります。 Azure AD 認証が不可能な場合にのみ、以下のソリューションを使用してください。

Azure ストレージ アカウントでは、アカウント名とキーで構成された資格情報が使用されます。 キーは自動生成され、暗号キーではなくパスワードとして機能します。 Key Vault では、ストレージ アカウントでストレージ アカウント キーを定期的に再生成することでそれらのキーの管理が行われることに加え、ストレージ アカウント内のリソースへの委任アクセス用の Shared Access Signature トークンが提供されます。

Key Vault マネージド ストレージ アカウント キー機能を使用して、Azure ストレージ アカウントのキーを一覧表示し (同期)、定期的にキーを再生成 (ローテーション) できます。 ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理できます。

## <a name="azure-storage-account-key-management"></a>Azure ストレージ アカウント キーの管理

Key Vault では、[Azure ストレージ アカウント](../../storage/common/storage-account-overview.md) キーを管理できます。

- Key Vault の内部では、Azure ストレージ アカウントを使用してキーの一覧表示 (同期) ができます。 
- Key Vault は定期的にキーを再生成 (ローテーション) します。
- キーの値は、呼び出し元に応答で返されることはありません。
- Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーが管理されます。

詳細については、次を参照してください。
- [ストレージ アカウントのアクセス キー](../../storage/common/storage-account-keys-manage.md)
- [Azure Key Vault でのストレージ アカウント キーの管理](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>ストレージ アカウントのアクセス制御

ユーザーまたはアプリケーション プリンシパルがマネージド ストレージ アカウントに対する操作を実行するのを承認するときは、次のアクセス許可を使用できます。  

- マネージド ストレージ アカウントと SaS 定義の操作に対するアクセス許可
  - *get*:ストレージ アカウントに関する情報を取得します 
  - *list*:キー コンテナーによって管理されているストレージ アカウントを一覧表示します
  - *update*:ストレージ アカウントを更新します
  - *delete*:ストレージ アカウントを削除する  
  - *recover*:削除されたストレージ アカウントを復旧します
  - *backup*:ストレージ アカウントをバックアップします
  - *restore*:バックアップしたストレージ アカウントをキー コンテナーに復元します
  - *set*:ストレージ アカウントを作成または更新します
  - *regeneratekey*:ストレージ アカウントの指定されたキー値を再生成します
  - *getsas*:ストレージ アカウントの SAS 定義に関する情報を取得します
  - *listsas*:ストレージ アカウントのストレージ SAS 定義を一覧表示します
  - *deletesas*:ストレージ アカウントから SAS 定義を削除します
  - *setsas*:ストレージ アカウントの新しい SAS 定義/属性を作成または更新します

- 特権操作に対するアクセス許可
  - *purge*:マネージド ストレージ アカウントをパージ (完全に削除) します

詳しくは、[Key Vault REST API リファレンス内のストレージ アカウントの操作](/rest/api/keyvault)に関するページをご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する](overview-storage-keys.md)
- [Key Vault について](../general/overview.md)
- [キー、シークレット、証明書について](../general/about-keys-secrets-certificates.md)
- [Key Vault でのシークレットの管理に関するベスト プラクティス](secrets-best-practices.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
