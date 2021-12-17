---
title: マネージド ID を使用してデータ ソースへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用してデータ ソースへのインデクサー接続を設定する方法を説明します
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 6c038b7bac8d0791aafcda0010adeae138489ceb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893787"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>マネージド ID を使用してデータ ソースへのインデクサー接続を設定する

> [!IMPORTANT] 
> 無料の Azure Cognitive Search レベルの場合、マネージド ID でデータ ソースへの接続を設定することはできません。

Azure Cognitive Search の[インデクサー](search-indexer-overview.md)は、データ ソースから Azure Cognitive Search にデータをプルするためのクローラーです。 インデクサーは、ユーザーが作成したデータ ソース オブジェクトからデータ ソース接続を取得します。 データ ソース オブジェクトには、通常、対象のデータ ソースの資格情報が含まれています。 たとえば、BLOB ストレージ コンテナーのデータに対してインデックスを作成する場合は、データ ソース オブジェクトに Azure Storage アカウント キーを含めることができます。

多くの場合、データ ソース オブジェクトで資格情報を直接指定することに問題はありませんが、いくつかの課題もあります。
* データ ソース オブジェクトを作成するコード内で、資格情報のセキュリティを確保するにはどうすればよいか。
* アカウント キーまたはパスワードが侵害され、パスワードを変更する必要がある場合には、インデクサーがデータ ソースに再び接続できるように、新しいアカウント キーやパスワードでデータ ソース オブジェクトを更新する必要がある。

これらの問題は、マネージド ID を使用して接続を設定することにより解決できます。

## <a name="using-managed-identities"></a>マネージド ID の使用

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) 内で自動管理される ID をアプリケーションで使用できるようにする機能です。 Azure Cognitive Search でこの機能を使用すると、資格情報が含まれていない接続文字列を使用してデータ ソース オブジェクトを作成できます。 その場合、検索サービスには、Azure ロールベースのアクセス制御 (Azure RBAC) によってデータ ソースへのアクセスが許可されます。

マネージド ID を使用してデータ ソースを設定することで、データ ソースの資格情報を変更しても、インデクサーでは引き続きデータソースに接続できるようになります。 また、アカウント キーを含めたり、Key Vault を使用してアカウント キーを取得したりしなくても、コード内にデータ ソース オブジェクトを作成することができます。

マネージド ID には 2 種類があります。 Azure Cognitive Search では、システム割り当てマネージド ID とユーザー割り当てマネージド ID がサポートされています。

### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) は、1 つの Azure サービスに割り当てることができます。 システム割り当てマネージド ID を 1 つの Azure Cognitive Search サービスに割り当てることができます。これは、その検索サービスのライフサイクルに関連付けられています。

### <a name="user-assigned-managed-identity-preview"></a>ユーザー割り当てマネージド ID (プレビュー)

> [!IMPORTANT]
>この機能はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 REST API バージョン 2021-04-30-Preview および[管理 REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) によってこの機能が提供されます。

[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) は、1 つ以上の Azure サービスに割り当てることができるスタンドアロン Azure リソースです。 1 つの Azure Cognitive Search サービスに、1 つ以上のユーザー割り当てマネージド ID を割り当てることができます。 1 つのユーザー割り当てマネージド ID を複数の検索サービスに割り当てることができます。

## <a name="limitations"></a>制限事項

マネージド ID を使用したインデクサー接続の設定は、次のデータ ソースでサポートされています。 

* [Azure Blob Storage、Azure Data Lake Storage Gen2 (プレビュー)、Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

次の機能では、現在のところ、マネージド ID した接続の設定はサポートされていません。
* ナレッジ ストア
* カスタム スキル
 
## <a name="next-steps"></a>次のステップ

マネージド ID を使用してインデクサー接続を設定する方法の詳細については、次を参照してください。

* [Azure Blob Storage、Azure Data Lake Storage Gen2 (プレビュー)、Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)