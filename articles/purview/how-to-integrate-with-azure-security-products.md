---
title: Azure のセキュリティ製品との統合
description: この記事では、Azure セキュリティ サービスと Azure Purview を接続して、強化されたセキュリティ エクスペリエンスを実現する方法について説明します。
author: aashishr
ms.author: aashishr
ms.service: purview
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 7369f64f16724eb5c660b54a3d14a30046a41685
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894891"
---
# <a name="integrate-azure-purview-with-azure-security-products"></a>Azure Purview と Azure のセキュリティ製品を統合する

このドキュメントでは、Azure Purview アカウントとさまざまな Azure セキュリティ製品を接続し、データ分類と秘密度ラベルを使用してセキュリティ エクスペリエンスを強化するために必要な手順について説明します。

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud
Azure Purview では、データの機密性に関する豊富な洞察が提供されます。 Microsoft Defender for Cloud を使用して組織のセキュリティ体制を管理し、ワークロードに対する脅威から保護するセキュリティ チームにとって、これが重要になります。 データ リソースは、悪意のあるアクターにとって依然として一般的なターゲットであり、セキュリティ チームがクラウド環境全体で機密データ リソースを識別し、優先順位付けして、セキュリティ保護することが重要です。 Azure Purview との統合により、データ層の可視性が拡張され、セキュリティ チームは機密データを含むリソースに優先順位を付けることができるようになります。

[Microsoft Defender for Cloud でこのエンリッチメント](../security-center/information-protection.md)を利用するために、Azure Purview での追加の手順は必要ありません。 Microsoft Defender for Cloud の[インベントリ ページ](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/25)を使用して、セキュリティ エンリッチメントの調査を開始します。ここでは、分類と秘密度ラベルを含むデータ ソースの一覧を確認できます。

### <a name="supported-data-sources"></a>サポートされるデータ ソース
統合により、Azure と AWS のデータ ソースがサポートされます。これらのリソースで検出された機密データは、Microsoft Defender for Cloud と共有されます。
- [Azure Blob Storage](./register-scan-azure-blob-storage-source.md)
- [Azure Cosmos DB](./register-scan-azure-cosmos-database.md)
- [Azure Data Explorer](./register-scan-azure-data-explorer.md)
- [Azure Data Lake Storage Gen1](./register-scan-adls-gen1.md)
- [Azure Data Lake Storage Gen2](./register-scan-adls-gen2.md)
- [Azure Files](./register-scan-azure-files-storage-source.md)
- [Azure Database for MySQL](./register-scan-azure-mysql-database.md)
- [Azure Database for PostgreSQL](./register-scan-azure-postgresql.md)
- [Azure SQL Database マネージド インスタンス](./register-scan-azure-sql-database-managed-instance.md)
- [Azure 専用 SQL プール (以前の SQL DW)](./register-scan-azure-synapse-analytics.md)
- [Azure SQL Database](./register-scan-azure-sql-database.md)
- [Azure Synapse Analytics (ワークスペース)](./register-scan-synapse-workspace.md)
- [Amazon S3](./register-scan-amazon-s3.md)

### <a name="known-issues"></a>既知の問題
1. 現在、データの機密情報は、SAP、Erwin、Teradata などの仮想マシン内でホストされているソースでは共有されていません。
2. データの機密情報は、現在 Amazon RDS では共有されていません。
3. 現在、接続文字列を使用して登録された Azure PaaS データ ソースでは、データの機密情報は共有されていません。 
5. Azure Purview のデータ ソースの登録を解除しても、Microsoft Defender for Cloud のデータの機密性エンリッチメントは削除されません。
6. Azure Purview アカウントを削除すると、Microsoft Defender for Cloud ではデータの機密性エンリッチメントが 30 日間保持されます。
7. Microsoft 365 コンプライアンス センターまたは Azure Purview で定義されたカスタム分類は、Microsoft Defender for Cloud と共有されません。

## <a name="faq"></a>よく寄せられる質問
### <a name="why-dont-i-see-the-aws-data-source-i-have-scanned-with-azure-purview-in-microsoft-defender-for-cloud"></a>**Microsoft Defender for Cloud で Azure Purview でスキャンした AWS データ ソースが表示されないのはなぜですか?**

データ ソースは、Microsoft Defender for Cloud にもオンボードする必要があります。 [AWS アカウントを接続](../security-center/quickstart-onboard-aws.md)し、Microsoft Defender for Cloud で AWS データ ソースを確認する方法に関する記事を参照してください。

### <a name="why-dont-i-see-sensitivity-labels-in-microsoft-defender-for-cloud"></a>**Microsoft Defender for Cloud に秘密度ラベルが表示されないのはなぜですか?**

Microsoft Defender for Cloud でラベルを表示するには、まず Azure Purview でアセットにラベルを付ける必要があります。 [秘密度ラベルの前提条件](./how-to-automatically-label-your-content.md)を満たしているかどうかを確認します。 データをスキャンすると、Azure Purview にラベルが表示され、Microsoft Defender for Cloud に自動的に表示されます。

## <a name="next-steps"></a>次のステップ
- [Azure Purview の秘密度を使用して強化される Microsoft Defender for Cloud のエクスペリエンス](../security-center/information-protection.md)
