---
title: Azure Confidential Ledger に格納されているダイジェストにアクセスする
description: Azure SQL Database 台帳で、Azure Confidential Ledger に格納されているダイジェストにアクセスします。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: f011494412b6f9cf1ac186f171b62fe82d02aaaa
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061741"
---
# <a name="access-the-digests-stored-in-confidential-ledger"></a>Confidential Ledger に格納されているダイジェストにアクセスする

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

この記事では、[Azure Confidential Ledger](../../confidential-ledger/index.yml) に格納された [Azure SQL Database 台帳](ledger-overview.md)のダイジェストにアクセスし、エンドツーエンドのセキュリティと整合性を保証する方法について説明します。 この記事を通して、格納された情報にアクセスし、その整合性を確認する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Python 2.7、3.5.3、またはそれ以降。
- 台帳が有効になっている Azure SQL Database。 SQL Database でデータベースをまだ作成していない場合は、「[クイックスタート: Azure SQL Database で台帳が有効化されたデータベースを作成する](ledger-create-a-single-database-with-ledger-enabled.md)」を参照してください。
- [Python 用 Azure Confidential Ledger クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)。
- [Confidential Ledger](../../confidential-ledger/index.yml) の実行インスタンス。

## <a name="how-does-the-integration-work"></a>連携の仕組み

Azure SQL Server では、[台帳データベース](ledger-overview.md#ledger-database)のダイジェストを定期的に計算して、それを Confidential Ledger に保存します。 データの整合性は、いつでも検証できます。 Confidential Ledger からダイジェストをダウンロードし、それらを SQL Database 台帳に格納されているダイジェストと比較します。 次の手順では、このプロセスについて説明します。

## <a name="1-find-the-digest-location"></a>1. ダイジェストの保存場所を見つける

> [!NOTE]
> ダイジェストの格納に複数の Confidential Ledger インスタンスを使用した場合は、クエリに対して複数の行が返されます。 各行に対して手順 2 から 6 を繰り返し、Confidential Ledger のすべてのインスタンスからダイジェストをダウンロードします。

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、次のクエリを実行します。 出力に、ダイジェストが格納されている Confidential Ledger インスタンスのエンドポイントが示されます。

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%'
```

## <a name="2-determine-the-subledgerid"></a>2. subledgerid を特定する

クエリの出力のパスの列の値を確認します。 `host name` と `subledgerid` の 2 つの部分で構成されています。 例として、URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` では、`host name` は `https://contoso-ledger.confidential-ledger.azure.com`、`subledgerid` は `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` です。 手順 4 でこれを使用してダイジェストをダウンロードします。

## <a name="3-obtain-an-azure-ad-token"></a>3. Azure AD トークンを取得する

Confidential Ledger API では、Azure Active Directory (Azure AD) ベアラー トークンを呼び出し元の ID として受け入れます。 この ID は、プロビジョニング時に Azure Resource Manager を通じて Confidential Ledger にアクセスできる必要があります。 SQL Database の台帳を有効にしたユーザーには、Confidential Ledger への管理者アクセス権が自動的に与えられます。 トークンを取得するには、Azure portal で使用したものと同じアカウントで [Azure CLI](/cli/azure/install-azure-cli) を使用して認証を行う必要があります。 認証が完了したら、[AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) を使用してベアラー トークンを取得し、Azure Confidential Ledger API を呼び出すことができます。

Confidential Ledger へのアクセス権がある ID を使用して Azure AD にサインインします。

```azure-cli
az login
```

ベアラー トークンを取得します。

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-confidential-ledger"></a>4. Confidential Ledger からダイジェストをダウンロードする

次の Python スクリプトによって、Confidential Ledger からダイジェストがダウンロードされます。 このスクリプトでは [Python 用 Confidential Ledger クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)を使用します。

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

## <a name="5-download-the-digests-from-the-sql-server"></a>5. SQL サーバーからダイジェストをダウンロードする

> [!NOTE]
> この手順は、Azure SQL Database 台帳に格納されたハッシュが以前から変化していないことを確認する方法です。 SQL Database 台帳の整合性の完全な監査を行うには、「[台帳テーブルを検証して改ざんを検出する方法](ledger-verify-database.md)」を参照してください。

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、次のクエリを実行します。 クエリに対し、Genesis ブロックに連なるブロックのダイジェストが返されます。

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. 比較

Confidential Ledger から取得したダイジェストを、`block_id` をキーとして使用して SQL Database 内のデータベースから返されたダイジェストと比較します。 たとえば、`block_id` = `1` のダイジェストは、`block_id`= `2` 行の `previous_block_hash` 列の値です。 同様に、`block_id` = `3` でそれに相当するのは、`block_id` = `4` 行の `previous_block_id` 列の値です。 ハッシュ値の不一致が見つかった場合、データが改ざんされた可能性があります。

データの改ざんが疑われる場合は、「[台帳テーブルを検証して改ざんを検出する方法](ledger-verify-database.md)」を参照して、SQL Database 台帳の完全な監査を行います。

## <a name="next-steps"></a>次の手順

- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [データベース台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [台帳テーブルを検証して改ざんを検出する](ledger-verify-database.md)
