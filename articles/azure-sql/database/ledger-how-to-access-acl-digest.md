---
title: Azure Confidential Ledger (ACL) に保存されたダイジェストにアクセスする方法
description: Azure SQL Database 台帳で、Azure Confidential Ledger (ACL) に保存されたダイジェストにアクセスする方法
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 3f8b5ae7c80c712c441648808f0303528bad8018
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966717"
---
# <a name="how-to-access-the-digests-stored-in-acl"></a>ACL に保存されたダイジェストにアクセスする方法

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 台帳は現在 **パブリック プレビュー** です。

この記事では、[Azure Confidential Ledger (ACL)](../../confidential-ledger/index.yml) に保存された [Azure SQL Database 台帳](ledger-overview.md)のダイジェストにアクセスし、エンドツーエンドのセキュリティと整合性を保証する方法を説明します。 この記事で、保存された情報にアクセスし、その整合性を確認する方法を解説します。

## <a name="prerequisites"></a>前提条件

- Python 2.7、3.5.3 以降
- 台帳を有効にした Azure SQL Database を用意します。 Azure SQL Database をまだ作成していない場合は、[クイックスタート: 台帳を有効にした Azure SQL Database の作成](ledger-create-a-single-database-with-ledger-enabled.md)に関する記事をご覧ください。
- [Azure Confidential Ledger client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)
- [Azure Confidential Ledger](../../confidential-ledger/index.yml) の実行インスタンス。

## <a name="how-does-the-integration-work"></a>連携の仕組み

Azure SQL サーバーでは、[台帳データベース](ledger-overview.md#ledger-database)のダイジェストを定期的に計算して、それを Azure Confidential Ledger に保存します。 Azure Confidential Ledger からダイジェストをダウンロードして Azure SQL Database 台帳に保存されているダイジェストと比較することで、ユーザーはいつでもデータの整合性を確認できます。 次の手順でそれを実行できます。

## <a name="1-find-the-digest-location"></a>1. ダイジェストの保存場所を見つける

> [!NOTE]
> ダイジェストの保存に複数の Azure Confidential Ledger インスタンスを使用した場合は、クエリに対して複数の行が返されます。 各行に対してステップ 2 - 6 を繰り返し、Azure Confidential Ledger のすべてのインスタンスからダイジェストをダウンロードします。

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) で次のクエリを実行します。 出力に、ダイジェストが保存されている Azure Confidential Ledger インスタンスのエンドポイントが表示されます。

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%
```

## <a name="2-determine-the-subledgerid"></a>2. Subledgerid を特定する

クエリの出力のパスの列の値を確認します。 `host name` と `subledgerid` の 2 つの部分で構成されています。 例として、Url `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` では、`host name` は `https://contoso-ledger.confidential-ledger.azure.com`、`subledgerid` は `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` です。 ステップ 4 では、これを使用してダイジェストをダウンロードします。

## <a name="3-obtain-an-azure-ad-token"></a>3. Azure AD トークンを取得する

Azure Confidential Ledger API では、Azure Active Directory (Azure AD) ベアラー トークンを呼び出し元の ID として受け入れます。 この ID は、プロビジョニング時に Azure Resource Manager を通じて ACL にアクセスできる必要があります。 SQL Database で台帳を有効にしたユーザーには、Azure Confidential Ledger への管理者アクセス権が自動的に付与されます。 トークンを取得するには、Azure portal で使用しているアカウントで [Azure CLI](/cli/azure/install-azure-cli) を使用して認証を行う必要があります。 認証が済んだら、[AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) を使用してベアラー トークンを取得し、Azure Confidential Ledger API を呼び出すことができます。

ACL へのアクセス権がある ID で Azure AD にログインします。

```azure-cli
az login
```

ベアラー トークンを取得します。

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-azure-confidential-ledger"></a>4. Azure Confidential Ledger からダイジェストをダウンロードする

次の Python スクリプトで、Azure Confidential Ledger からダイジェストをダウンロードできます。 このスクリプトでは [Azure Confidential Ledger client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger) を使用します。

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. - SQL Server からダイジェストをダウンロードする

> [!NOTE]
> この方法で、Azure SQL Database 台帳に保存されたハッシュが以前から変化していないことを確認します。 Azure SQL Database 台帳の整合性の完全な監査を行うには、[台帳テーブルの有効性を確認して改ざんを検出する方法](ledger-verify-database.md)に関する記事をご覧ください。

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) で次のクエリを実行します。 クエリに対し、Genesis ブロックに連なるブロックのダイジェストが返されます。

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. 比較

Azure Confidential Ledger から取得したダイジェストを、`block_id` をキーに使用して SQL データベースから返されたダイジェストと比較します。 たとえば、`block_id` = `1` のダイジェストは、`block_id`= `2` 行の `previous_block_hash` 列の値です。 同様に、`block_id` = `3` でそれに相当するのは、`block_id` = `4` 行の `previous_block_id` 列の値です。 ハッシュ値の不一致が見つかった場合、データが改ざんされた可能性があります。

データの改ざんが疑われる場合は、[台帳テーブルの有効性を確認して改ざんを検出する方法](ledger-verify-database.md)に関する記事を読み、Azure SQL Database 台帳の完全な監査を行います。

## <a name="next-steps"></a>次の手順

- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [データベース台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [台帳テーブルの有効性を確認して改ざんを検出する方法](ledger-verify-database.md)
