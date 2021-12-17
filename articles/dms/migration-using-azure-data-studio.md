---
title: Azure Data Studio を使用して移行する
description: Azure Data Studio の Azure SQL Migration 拡張機能を使用して、Azure Database Migration Service でデータベースを移行する方法について説明します。
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d2840b5e8386801411a759d1b00f026bfbacad68
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027700"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>Azure Data Studio 用 Azure SQL Migration 拡張機能を使用してデータベースを移行する (プレビュー)

[Azure Data Studio 用 Azure SQL Migration 拡張機能](/sql/azure-data-studio/extensions/azure-sql-migration-extension)により、Azure Data Studio で新しい SQL Server 評価および移行機能を使用できます。

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>Azure Data Studio 用 Azure SQL Migration 拡張機能のアーキテクチャ

Azure Database Migration Service (DMS) は、アーキテクチャ全体のコア コンポーネントの 1 つです。 DMS は、Azure SQL へのデータベースの移行を可能にする信頼性の高い移行オーケストレーターを提供します。 DMS を作成したり、既存の DMS を再利用したりする際は、Azure Data Studio (ADS) の Azure SQL Migration 拡張機能を使用します。
DMS では、Azure Data Factory のセルフホステッド統合ランタイムを使用して、オンプレミスのネットワーク共有の有効なバックアップ ファイルにアクセスし、Azure ストレージ アカウントにアップロードします。

移行プロセスのワークフローを次の図に示します。

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="Azure Data Studio と DMS を使用したデータベース移行のアーキテクチャの図":::

1. **ソース SQL Server**: オンプレミス、プライベート クラウド、または任意のパブリック クラウドの仮想マシン上の SQL Server インスタンス。 SQL Server 2008 以降のすべてのエディションがサポートされています。
1. **ターゲット Azure SQL**: サポートされている Azure SQL ターゲットは、Azure SQL Managed Instance と Azure 仮想マシン上の SQL Server ([フル管理モード](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes)で SQL IaaS Agent 拡張機能に登録済み) です。
1. **ネットワーク ファイル共有**: 移行するデータベースのバックアップ ファイルが保存されているサーバー メッセージ ブロック (SMB) ネットワーク ファイル共有。 Azure Storage BLOB コンテナーと Azure Storage ファイル共有もサポートされています。
1. **Azure Data Studio**: [Azure Data Studio の Azure SQL Migration 拡張機能](/sql/azure-data-studio/extensions/azure-sql-migration-extension)をダウンロードしてインストールします。
1. **Azure DMS**: オンプレミスから Azure へのデータ移動アクティビティを実行するために移行パイプラインを調整する Azure サービスです。 DMS は、Azure Data Factory (ADF) のセルフホステッド統合ランタイム (IR) に関連付けられており、セルフホステッド IR を登録および監視する機能を提供します。
1. **セルフホステッド統合ランタイム (IR)** : セルフホステッド IR は、ソース SQL Server とバックアップ ファイルの場所に接続できるマシンにインストールする必要があります。 DMS によって認証キーが提供され、セルフホステッド IR が登録されます。
1. **Azure Storage へのバックアップ ファイルのアップロード**: DMS はセルフホステッド IR を使用して、オンプレミスのバックアップの場所から、プロビジョニング済みの Azure ストレージ アカウントに有効なバックアップ ファイルをアップロードします。 データ移動アクティビティとパイプラインは、バックアップ ファイルをアップロードするために移行ワークフローで自動的に作成されます。
1. **ターゲット Azure SQL でのバックアップの復元**: DMS は、Azure ストレージ アカウントからサポートされているターゲット Azure SQL にバックアップ ファイルを復元します。 
    > [!IMPORTANT]
    > オンライン移行モードでは、DMS がソース バックアップ ファイルを Azure Storage に継続的にアップロードし、ターゲットに復元していきます。これが終わったら、最後にターゲットへのカットオーバーを実行する必要があります。
    >
    > オフライン移行モードでは、DMS がソース バックアップ ファイルを Azure Storage にアップロードし、ターゲットに復元します。カットオーバーを実行する必要はありません。

## <a name="prerequisites"></a>前提条件

サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。

* [Azure Data Studio をダウンロードし、インストールする](/sql/azure-data-studio/download-azure-data-studio)
* Azure Data Studio マーケットプレースから [Azure SQL Migration 拡張機能をインストール](/sql/azure-data-studio/extensions/azure-sql-migration-extension)します
* 次に示す組み込みロールのいずれかに割り当てられている Azure アカウントを用意します。
    - ターゲット Azure SQL Managed Instance の共同作成者 (および SMB ネットワーク共有からデータベース バックアップ ファイルをアップロードするためのストレージ アカウント)。
    - ターゲット Azure SQL Managed Instance または Azure ストレージ アカウントを含む Azure リソース グループの所有者または共同作成者ロール。
    - Azure サブスクリプションの所有者または共同作成者ロール。
* ターゲットの [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) または [Azure 仮想マシン上の SQL Server](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md) を作成します。
* ソース SQL Server の接続に使用するログイン情報が、*sysadmin* サーバー ロールのメンバーであるか、`CONTROL SERVER` アクセス許可が付与されていることを確認します。 
* データベースの完全バックアップ ファイルとトランザクション ログ バックアップ ファイルに、次のいずれかのストレージ オプションを使用します。 
    - SMB ネットワーク共有 
    - Azure ストレージ アカウントのファイル共有または BLOB コンテナー 

    > [!IMPORTANT]
    > - データベース バックアップ ファイルが SMB ネットワーク共有で提供されている場合は、DMS サービスがデータベース バックアップ ファイルをアップロードできる [Azure ストレージ アカウントを作成](../storage/common/storage-account-create.md)します。  Azure Storage アカウントは、Azure Database Migration Service インスタンスの作成先と同じリージョンに作成してください。
    > - Azure Database Migration Service によってバックアップが開始されることはなく、移行には既存のバックアップが使用されます。これは、ディザスター リカバリー計画の一部として既に作成されている場合があります。
    > - [`WITH CHECKSUM` オプションを使用してバックアップ](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)を作成する必要があります。 
    > - 各バックアップは、独立したバックアップ ファイルまたは複数のバックアップ ファイルに書き込まれます。 ただし、1 つのバックアップ メディアに複数のバックアップ (完全バックアップとトランザクション ログなど) を追加することはサポートされていません。 
    > - 大きなバックアップの移行に関連する潜在的な問題が発生する可能性を低減するには、圧縮されたバックアップを使用します。
* ソース SQL Server インスタンスを実行しているサービス アカウントに、データベース バックアップ ファイルが格納されている SMB ネットワーク共有に対する読み取りおよび書き込みアクセス許可があることを確認します。
* Transparent Data Encryption (TDE) で保護されているデータベースのソース SQL Server インスタンス証明書は、データを移行する前に、ターゲットの Azure SQL Managed Instance または Azure 仮想マシン上の SQL Server に移行する必要があります。 詳細については、「[TDE で保護されたデータベースの証明書を Azure SQL Managed Instance に移行する](../azure-sql/managed-instance/tde-certificate-migrate.md)」および「[別の SQL Server への TDE で保護されたデータベースの移動](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)」を参照してください。
    > [!TIP]
    > データベースに [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio) で保護されている機密データが含まれている場合、Azure Data Studio と DMS を使用した移行プロセスでは、ターゲットの Azure SQL Managed Instance または Azure 仮想マシン上の SQL Server に Always Encrypted キーが自動的に移行されます。

* データベース バックアップがネットワーク ファイル共有にある場合は、データベース バックアップにアクセスして移行するための[セルフホステッド統合ランタイム](../data-factory/create-self-hosted-integration-runtime.md)をインストールするマシンを用意します。 移行ウィザードでは、セルフホステッド統合ランタイムをダウンロードしてインストールするためのダウンロード リンクと認証キーが提供されます。 移行の準備として、セルフホステッド統合ランタイムをインストールする予定のマシンで、次のアウトバウンド ファイアウォール規則とドメイン名が有効になっていることを確認します。

    | ドメイン名                                          | 送信ポート | 説明                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | パブリック クラウド: `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 中国: `{datafactory}.{region}.datafactory.azure.cn` | 443            | セルフホステッド統合ランタイムがデータ移行サービスに接続するために必要です。 <br>パブリック クラウドに新しく作成されたデータ ファクトリの場合、セルフホステッド統合ランタイム キーから `{datafactory}.{region}.datafactory.azure.net` 形式の FQDN を見つけます。 古いデータ ファクトリで、セルフホステッド統合キーに FQDN が見つからない場合は、代わりに *.frontend.clouddatahub.net を使用してください。 |
    | `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
    | `*.core.windows.net`          | 443            | ネットワーク共有からデータベース バックアップをアップロードするために Azure ストレージ アカウントに接続するセルフホステッド統合ランタイムによって使用されます |

    > [!TIP]
    > データベース バックアップ ファイルが Azure ストレージ アカウントで既に提供されている場合、移行プロセス中にセルフホステッド統合ランタイムは不要です。

* セルフホステッド統合ランタイムを使用する場合は、ランタイムがインストールされているマシンが、ソース SQL Server インスタンスと、バックアップ ファイルがあるネットワーク ファイル共有に接続できることを確認します。 アウトバウンド ポート 445 を有効にして、ネットワーク ファイル共有へのアクセスを許可する必要があります。
* Azure Database Migration Service を初めて使用する場合は、Microsoft.DataMigration リソース プロバイダーがサブスクリプションに登録されていることを確認します。 [リソース プロバイダーを登録する](./quickstart-create-data-migration-service-portal.md#register-the-resource-provider)手順に従ってください

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>データベースの移行にセルフホステッド統合ランタイムを使用する際の推奨事項
- 複数のソース SQL Server データベースに対して、1 つのセルフホステッド統合ランタイムを使用します。
- 1 台のマシンにはセルフホステッド統合ランタイムのインスタンスを 1 つだけインストールします。
- 1 つの DMS にはセルフホステッド統合ランタイムを 1 つだけ関連付けます。
- セルフホステッド統合ランタイムは、インストールされているマシン上のリソース (メモリや CPU) を使用します。 セルフホステッド統合ランタイムは、ソース SQL Server とは異なるマシンにインストールします。 しかし、セルフホステッド統合ランタイムをデータ ソースの近くに配置することにより、セルフホステッド統合ランタイムからデータ ソースへの接続時間が短縮されます。 
- セルフホステッド統合ランタイムは、データベース バックアップがオンプレミスの SMB ネットワーク共有にある場合にのみ使用します。 ソース データベース バックアップが既に Azure Storage BLOB コンテナーにある場合、データベースの移行にセルフホステッド統合ランタイムは不要です。
- データベースの同時移行は、1 台のマシンのセルフホステッド統合ランタイムごとに最大 10 個にすることをお勧めします。 データベースの同時移行の数を増やすには、セルフホステッド ランタイムを最大 4 ノードにスケールアウトするか、マシンごとにセルフホステッド統合ランタイムを個別に作成します。
- 自動更新するようにセルフホステッド統合ランタイムを構成して、リリースされた新機能、バグ修正、拡張機能を自動的に適用します。 詳細については、[セルフホステッド統合ランタイムの自動更新](../data-factory/self-hosted-integration-runtime-auto-update.md)に関する記事を参照してください。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項
- ターゲットの Azure SQL Managed Instance または Azure 仮想マシン上の SQL Server で DMS を使用して既存のデータベースを上書きする操作はサポートされていません。
- ソース トポロジに合わせてターゲットで高可用性とディザスター リカバリーを構成することは、DMS ではサポートされていません。
- 次のサーバー オブジェクトはサポートされていません。
    - Login
    - SQL Server エージェント ジョブ
    - 資格情報
    - SSIS パッケージ
    - サーバーの役割
    - サーバー監査
- PowerShell または CLI を使用して Azure Data Studio を使った移行を自動化することはサポートされていません。
- Azure SQL Database への移行はサポートされていません。
- 特定のファイアウォール規則で保護されているか、プライベート エンドポイントが構成されている Azure ストレージ アカウントは、移行がサポートされていません。
- DMS を使用したデータベースの移行には、Azure Data Factory から作成された既存のセルフホステッド統合ランタイムを使用することはできません。 最初は、Azure Data Studio の Azure SQL Migration 拡張機能を使用してセルフホステッド統合ランタイムを作成する必要があります。これを今後のデータベース移行に再利用できます。
> [!IMPORTANT]
> **Azure VM 上の SQL Server に複数のデータベースを移行するときの既知の問題:** Azure VM 上の同じ SQL Server に複数のデータベースを同時に移行すると、ほとんどのデータベースで移行が失敗します。 Azure VM 上の SQL Server には、データベースを 1 つずつ移行してください。  

## <a name="pricing"></a>価格
- Azure Database Migration Service は、Azure Data Studio の Azure SQL Migration 拡張機能で無料で使用できます。 Azure Database Migration Service または Azure SQL Migration 拡張機能を使用すると、無料でこのサービスを使用して複数の SQL Server データベースを移行できます。
- オンプレミスから Azure にデータベースを移行する場合、データ移動やデータ イングレスのコストはかかりません。 ソース データベースを別のリージョンまたは Azure VM から移動する場合、帯域幅プロバイダーとルーティングのシナリオに基づいて[帯域幅の料金](https://azure.microsoft.com/pricing/details/bandwidth/)が発生することがあります。
- Azure Data Studio をインストールする独自のマシンまたはオンプレミス サーバーを用意します。
- オンプレミスのネットワーク共有のデータベース バックアップにアクセスするには、セルフホステッド統合ランタイムが必要です。

## <a name="next-steps"></a>次のステップ

- Azure SQL Migration 拡張機能の概要とインストールについては、[Azure Data Studio 用 Azure SQL Migration 拡張機能](/sql/azure-data-studio/extensions/azure-sql-migration-extension)に関する記事を参照してください。
