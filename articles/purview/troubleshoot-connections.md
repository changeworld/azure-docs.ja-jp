---
title: Azure Purview での接続のトラブルシューティング
description: この記事では、Azure Purview で接続のトラブルシューティングを行う手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 18f3a98874fcfb98978778cf40ebe2cd876826d0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076151"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure Purview での接続のトラブルシューティング

この記事では、Azure Purview でデータ ソースのスキャンを設定するときの、接続エラーをトラブルシューティングする方法について説明します。

## <a name="permission-the-credential-on-the-data-source"></a>データ ソースの資格情報のアクセス許可

スキャンの認証方法としてマネージド ID またはサービス プリンシパルを使用している場合は、これらの ID にデータ ソースへのアクセスを許可する必要があります。

ソースの種類ごとに固有の手順があります。

- [Azure の複数のソース](register-scan-azure-multiple-sources.md#authentication-for-registration)
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#authentication-for-registration)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#prerequisites-for-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#prerequisites-for-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)
- [SQL Server](register-scan-on-premises-sql-server.md#authentication-for-registration)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-s3-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>Azure Purview Studio で Azure リソースを列挙するための Azure ロールベースのアクセス制御の確認

### <a name="registering-single-azure-data-source"></a>単一の Azure データ ソースの登録
Azure Purview で、Azure BLOG ストレージや Azure SQL Database などの単一のデータ ソースを登録するには、リソースに対して少なくとも **閲覧者** ロールが付与されているか、リソース グループやサブスクリプションなどの上位のスコープから継承されている必要があります。 セキュリティ管理者などの一部の Azure RBAC の役割には、コントロール プレーンで Azure リソースを表示するための読み取りアクセス権がないことにご注意ください。  

次の手順に従って、これを確認します。

1. [Azure portal](https://portal.azure.com)から、Azure Purview に登録しようとしているリソースに移動します。 リソースを表示できれば、そのリソースに対して少なくとも閲覧者ロールが既に付与されている可能性があります。 
2. **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。
3. Azure Purview にデータ ソースを登録しようとしているユーザーの名前またはメール アドレスで検索します。
4. 閲覧者などのロールの割り当てが一覧に存在するかどうかを確認するか、必要に応じて新しいロールの割り当てを追加します。

### <a name="scanning-multiple-azure-data-sources"></a>複数の Azure データ ソースのスキャン
1. [Azure portal](https://portal.azure.com) から、サブスクリプションまたはリソース グループに移動します。  
2. 左側のメニューから  **[アクセス制御 (IAM)]**   を選択します。 
3. **[+追加]** を選択します。 
4. **[入力の選択]** ボックスで、 **[閲覧者]** ロールを選択し、Azure Purview のアカウント名 (その MSI 名を表すもの) を入力します。 
5. **[保存]** を選択して、ロールの割り当てを終了します。
6. 上記の手順を繰り返して、Azure Purview で複数のデータ ソースの新しいスキャンを作成しようとしているユーザーの ID を追加します。

## <a name="scanning-data-sources-using-private-link"></a>Private Link を使用したデータ ソースのスキャン 
パブリック エンドポイントがデータ ソースに対して制限されている場合、Private Link を使用して Azure データ ソースをスキャンするには、セルフホステッド統合ランタイムを設定し、資格情報を作成する必要があります。 

> [!IMPORTANT]
> _[Deny public network access]\(パブリック ネットワーク アクセスを拒否する\)_ を使用して、データベースを Azure SQL データベースとして含む複数のデータ ソースをスキャンすると、失敗します。 プライベート エンドポイントを使用してこれらのデータ ソースをスキャンするには、代わりに単一のデータ ソースを登録するオプションを使用します。

セルフホステッド統合ランタイムの設定については、[Ingestion のプライベート エンドポイントとスキャン ソース](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)を参照してください。

Azure Purview で新しい資格情報を作成する方法の詳細については、[Azure Purview でのソース認証のための資格情報](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)に関するページを参照してください

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>キー コンテナーへの資格情報の格納と、適切なシークレット名およびバージョンの使用

資格情報を Azure Key Vault インスタンスに格納し、適切なシークレット名およびバージョンを使用する必要があります。

次の手順に従って、これを確認します。

1. お使いのキー コンテナーに移動します。
1. **[Settings]\(設定\)** > **[Secrets]\(シークレット\)** を選択します。
1. スキャンするデータ ソースに対する認証に使用するシークレットを選択します。
1. 使用するバージョンを選択し、 **[シークレット値を表示する]** を選択して、パスワードまたはアカウント キーが正しいことを確認します。 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Azure Key Vault での、Purview が管理する ID のアクセス許可の確認

Azure Key Vault にアクセスするために、Purview が管理する ID に適切なアクセス許可が構成されていることを確認します。

これを確認するには、次の手順を実行します。

1. 使用するキー コンテナーに移動し、 **[アクセス ポリシー]** セクションに移動します。

1. Purview が管理する ID が *[現在のアクセス ポリシー]* セクションに表示され、少なくともシークレットに対する **Get** および **List** アクセス許可が付与されていることを確認します。

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Get と List の両方のアクセス許可についてドロップダウンの選択を示す画像":::

Purview が管理する ID が一覧に表示されない場合は、「[スキャン用の資格情報を作成して管理する](manage-credentials.md)」に記載されている手順に従って追加します。 

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
