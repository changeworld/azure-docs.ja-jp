---
title: SQL Server から SQL Managed Instance:移行の概要
description: SQL Server データベースを Azure SQL Managed Instance に移行するために使用できるツールとオプションについて説明します。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078980"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>移行の概要: SQL Server から Azure SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server データベースを Azure SQL Managed Instance に移行するためのオプションと考慮事項について説明します。 

オンプレミスまたは次で動作している SQL Server データベースを移行できます。 

- Azure Virtual Machines 上の SQL Server。  
- アマゾン ウェブ サービス (AWS) Elastic Compute Cloud (EC2)。 
- AWS RDS (Relational Database Service)。 
- Google Cloud Platform (GCP) の Compute Engine。  
- GCP の Cloud SQL for SQL Server。 

その他の移行ガイドについては、[データベースの移行](https://docs.microsoft.com/data-migration)に関するページを参照してください。 

## <a name="overview"></a>概要

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) は、フル マネージド サービスを必要とする SQL Server ワークロードの推奨されるターゲット オプションであり、仮想マシンやそのオペレーティング システムを管理する必要ありません。 SQL Managed Instance を使用すると、アプリケーションやデータベースの変更を最小限にして、オンプレミスのアプリケーションを Azure に移動できます。 ネイティブの仮想ネットワーク サポートを使用してインスタンスの完全な分離が提供されます。 

## <a name="considerations"></a>考慮事項 

移行のオプションを評価する際に考慮する必要がある主な要素は、次のとおりです。 
- サーバーとデータベースの数
- データベースのサイズ
- 移行プロセス中の許容可能なビジネス ダウンタイム 

SQL Server データベースを SQL Managed Instance に移行する主な利点の 1 つは、インスタンス全体を移行するか、個々のデータベースのサブセットのみを移行するかを選択できる点です。 移行プロセスに次のものを含めるか慎重に計画します。 
- 同じインスタンスに併置する必要があるすべてのデータベース。 
- ログイン、資格情報、SQL Agent ジョブとオペレーター、サーバーレベルのトリガーなど、アプリケーションに必要なインスタンスレベルのオブジェクト。 

> [!NOTE]
> Azure SQL Managed Instance では、重大なシナリオでも 99.99% の可用性が保証されます。 SQL Managed Instance の一部の機能が原因で発生するオーバーヘッドは無効にできません。 詳細については、[SQL Managed Instance と SQL Server 間でパフォーマンスの違いが生じる主な原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)に関するブログ記事を参照してください。 


## <a name="choose-an-appropriate-target"></a>適切なターゲットを選択する

[パフォーマンス ベースライン](sql-server-to-managed-instance-performance-baseline.md)への適合のために、SQL Managed Instance の適切なサービス レベルと特性を選択する際に役立つ一般的なガイドラインを次に示します。 

- CPU 使用率ベースラインを使用して、SQL Server インスタンスで使用されるコア数に一致するマネージド インスタンスをプロビジョニングします。 [ハードウェアの世代の特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)に合わせてリソースのスケーリングが必要な場合があります。 
- メモリ使用量ベースラインを使用して、実際のメモリ割り当てに合った[仮想コア オプション](../../managed-instance/resource-limits.md#service-tier-characteristics)を選択します。 
- ファイル サブシステムのベースライン I/O 待機時間を使用して、General Purpose (5 ミリ秒を超える待機時間) と Business Critical (3 ミリ秒未満の待機時間) サービス レベルのいずれかを選択します。 
- ベースライン スループットを使用して、データとログ ファイルのサイズを事前に割り当てて、必要な I/O パフォーマンスを実現します。 

デプロイ時にコンピューティングとストレージのリソースを選択し、後で、アプリケーションのダウンタイムを発生させずに [Azure portal を使用してそれらを変更](../../database/scale-resources.md)できます。 

> [!IMPORTANT]
> [マネージド インスタンスの仮想ネットワーク要件](../../managed-instance/connectivity-architecture-overview.md#network-requirements)に不一致があると、新しいインスタンスの作成や既存インスタンスの使用ができない場合があります。  [新しいネットワークの作成](../../managed-instance/virtual-network-subnet-create-arm-template.md) および [既存のネットワークの構成](../../managed-instance/vnet-existing-add-subnet.md) についてご確認ください。 

Azure SQL Managed Instance でターゲットのサービス レベル (General Purpose または Business Critical) を選択する際のもう 1 つの重要な考慮事項は、Business Critical レベルでのみ利用できる特定の機能 (インメモリ OLTP など) を使用できるかどうかという点です。 

### <a name="sql-server-vm-alternative"></a>代替手段 としての SQL Server VM

お客様のビジネスの要件によっては、[Azure Virtual Machines 上の SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) の方が、Azure SQL Managed Instance よりも適切なターゲットである場合があります。 

次のいずれかの条件がビジネスに当てはまる場合は、代わりに SQL Server 仮想マシン (VM) に移行することを検討してください。 

- サードパーティ製やカスタムのエージェントを SQL Server と同じ仮想マシンにインストールするためなど、オペレーティング システムやファイル システムへの直接アクセスが必要である。 
- FileStream や FileTable、PolyBase、クロスインスタンス トランザクションなど、まだサポートされていない機能に対して厳密な依存関係がある。 
- 特定バージョンの SQL Server (たとえば 2012 など) を維持する必要がある。 
- コンピューティング要件が、マネージド インスタンスで提供されているものよりはるかに低く (1 つの仮想コアなど)、データベース統合が許容可能な選択肢ではない。 

## <a name="migration-tools"></a>移行ツール

次の移行ツールをお勧めします。 

|テクノロジ | 説明|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | この Azure サービスは、VMware 上で大規模に SQL データ資産を検出して評価するのに役立ちます。 Azure SQL デプロイに関する推奨事項、ターゲットのサイズ設定、月単位の見積もりが提供されます。 | 
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | この Azure サービスでは、移行プロセス時のダウンタイムを許容できるアプリケーションに対してオフライン モードでの移行がサポートされます。 オンライン モードでの継続的な移行とは異なり、オフライン モードの移行では、ソースからターゲットへのデータベースの完全バックアップの 1 回限りの復元が実行されます。 | 
|[ネイティブ バックアップと復元](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance では、ネイティブ SQL Server データベースのバックアップ (.bak ファイル) の復元がサポートされています。 これは、Azure Storage にデータベースの完全バックアップを提供できるお客様にとって、最も簡単な移行オプションです。 完全と差分バックアップもサポートされており、この記事の後半の[移行資産に関するセクション](#migration-assets)で説明しています。| 
|[ログ再生サービス](../../managed-instance/log-replay-service-migrate.md) | このクラウド サービスは、SQL Server のログ配布テクノロジに基づいて SQL Managed Instance で有効になっています。 これは、データベースの完全、差分、およびログ バックアップを Azure Storage に提供できるお客様のための移行オプションです。 ログ再生サービスは、バックアップ ファイルを Azure Blob Storage から SQL Managed Instance に復元するために使用されます。| 
| | |

次の表に、代替移行ツールを示します。 

|**テクノロジ** |**説明**  |
|---------|---------|
|[トランザクション レプリケーション](../../managed-instance/replication-transactional-overview.md) | トランザクションの一貫性を維持しながら、パブリッシャーとサブスクライバーの種類の移行オプションを提供することによって、ソース SQL Server データベース テーブルから SQL Managed Instance にデータをレプリケートします。 | 
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [一括コピー プログラム (bcp) ツール](/sql/tools/bcp-utility)では、SQL Server のインスタンスからデータ ファイルにデータをコピーします。 このツールを使用して、ソースからデータをエクスポートし、ターゲット SQL Managed Instance にデータ ファイルをインポートします。 </br></br> Azure SQL Managed Instance にデータを移動する高速一括コピー操作の場合、[スマート一括コピー ツール](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)を使用し、並列コピー タスクを利用して転送速度を最大化できます。 | 
|[インポートおよびエクスポート ウィザードまたは BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) は .bacpac 拡張子を持つ Windows ファイルで、データベースのスキーマとデータがカプセル化されています。 BACPAC を使用すると、SQL Server ソースからデータをエクスポートし、そのデータを Azure SQL Managed Instance にインポートできます。 |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  Azure Data Factory の[コピー アクティビティ](../../../data-factory/copy-activity-overview.md)では、組み込みコネクタと[統合ランタイム](../../../data-factory/concepts-integration-runtime.md)を使用して、ソースの SQL Server データベースから SQL Managed Instance にデータが移行されます。</br> </br> Data Factory では、SQL Server ソースから SQL Managed Instance にデータを移動するためのさまざまな[コネクタ](../../../data-factory/connector-overview.md)がサポートされています。 |

## <a name="compare-migration-options"></a>移行オプションを比較する

移行オプションを比較して、ビジネス ニーズに合ったパスを選択します。 

次の表では、推奨される移行オプションを比較しています。 

|移行オプション  |使用する場合  |考慮事項  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | - 単一のデータベースまたは複数のデータベースを大規模に移行する。 </br> - 移行プロセス中のダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスまたは Azure VM の SQL Server (2005 - 2019) </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM |  - 大規模な移行は、[PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) を使用して自動化できます。 </br> - 移行が完了するまでの時間は、データベースのサイズによって異なり、バックアップと復元の時間に左右されます。 </br> - 十分なダウンタイムが必要になる可能性があります。 |
|[ネイティブ バックアップと復元](../../managed-instance/restore-sample-database-quickstart.md) | - 個々の基幹業務アプリケーション データベースを移行する。  </br> - 個別の移行サービスまたはツールを使用せずに素早く簡単に移行する。  </br> </br> サポートされるソース: </br> - オンプレミスまたは Azure VM の SQL Server (2005 - 2019) </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM | - データベース バックアップでは、Azure Blob Storage へのデータ転送を最適化するために複数のスレッドが使用されますが、パートナー帯域幅とデータベース サイズが転送速度に影響する可能性があります。 </br> - ダウンタイムには、完全バックアップと復元を実行するために必要な時間 (データ操作のサイズ) を考慮する必要があります。| 
|[ログ再生サービス](../../managed-instance/log-replay-service-migrate.md) | - 個々の基幹業務アプリケーション データベースを移行する。  </br> - データベース移行には、さらに多くのコントロールが必要になります。  </br> </br> サポートされるソース: </br> - オンプレミスまたは Azure VM の SQL Server (2008 - 2019) </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM | - 移行すると、SQL Server で完全なデータベース バックアップが行われ、Azure Blob Storage にバックアップ ファイルがコピーされます。 ログ再生サービスは、バックアップ ファイルを Azure Blob Storage から SQL Managed Instance に復元するために使用されます。 </br> - 移行プロセス中に復元されるデータベースは復元中モードに入り、プロセスが完了するまで読み取りや書き込みに使用できません。| 
| | | |

次の表では、代替移行オプションを比較しています。 

|方法またはテクノロジ |使用する場合 |考慮事項  |
|---------|---------|---------|
|[トランザクション レプリケーション](../../managed-instance/replication-transactional-overview.md) | - ソース データベース テーブルからターゲット SQL Managed Instance データベース テーブルに変更を継続的にパブリッシュすることで移行する。 </br> - データベースを完全に、または選択したテーブル (データベースのサブセット) について部分的に移行する。  </br> </br> サポートされるソース: </br> - SQL Server (2012 - 2019) (一部制限あり) </br> - AWS EC2  </br> - GCP コンピューティングの SQL Server VM | </br> - セットアップは、他の移行オプションと比べて比較的複雑です。   </br> - (データベースをオフラインにせずに) データを移行するための継続的レプリケーション オプションが提供されます。</br> - トランザクション レプリケーションには、ソース SQL Server インスタンスにパブリッシャーを設定するときに考慮すべき制限があります。 詳細については、「[オブジェクトのパブリッシュに関する制限事項](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)」を参照してください。  </br> - [レプリケーション アクティビティを監視](/sql/relational-databases/replication/monitor/monitoring-replication)する機能を利用できます。    |
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 完全または部分的なデータ移行を実行する。 </br> - ダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスまたは Azure VM の SQL Server (2005 - 2019) </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM   | - データをソースからエクスポートするため、およびターゲットにインポートするためのダウンタイムが必要です。 </br> - エクスポートまたはインポートで使用されるファイル形式とデータ型は、テーブル スキーマと一致している必要があります。 |
|[インポートおよびエクスポート ウィザードまたは BACPAC](../../database/database-import.md)| - 個々の基幹業務アプリケーション データベースを移行する。 </br>- 比較的小規模なデータベースに適している。  </br>  個別の移行サービスやツールを必要としない。 </br> </br> サポートされるソース: </br> - オンプレミスまたは Azure VM の SQL Server (2005 - 2019) </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM  |   </br> - データをソースでエクスポートし、転送先でインポートする必要があるため、ダウンタイムが必要です。   </br> - 切り捨てやデータ型不一致のエラーを回避するために、エクスポートまたはインポートで使用されるファイル形式とデータ型は、テーブル スキーマと一致している必要があります。 </br> - 多数のオブジェクトを含むデータベースをエクスポートするために要する時間は、大幅に長くなる可能性があります。 |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| - ソース SQL Server データベースからのデータの移行や変換を行う。</br> - 複数のデータ ソースのデータを Azure SQL Managed Instance にマージする (通常、ビジネス インテリジェンス (BI) ワークロードが対象)。   </br> - ソースからターゲットにデータを移動するために、Data Factory でデータ移動パイプラインを作成する必要がある。   </br> - [コスト](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)が重要な考慮事項であり、パイプライン トリガー回数、アクティビティの実行数、データ移動の期間などの要因に応じて変わる。 |
| | | |

## <a name="feature-interoperability"></a>機能の相互運用性 

他の SQL Server 機能に依存するワークロードを移行する場合は、追加の考慮事項があります。 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

[Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md) を使用して、SSISDB の SQL Server Integration Services (SSIS) パッケージとプロジェクトを Azure SQL Managed Instance に移行します。 

移行でサポートされるのは、SQL Server 2012 以降の SSISDB の SSIS パッケージのみです。 移行前に以前の SSIS パッケージを変換します。 詳細については、[プロジェクト変換のチュートリアル](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)に関する記事を参照してください。 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) のレポートを、Power BI のページ分割されたレポートに移行できます。  [RDL 移行ツール](https://github.com/microsoft/RdlMigration)を使用すると、レポートの準備と移行に役立ちます。 Microsoft では、ユーザーがレポート定義言語 (RDL) レポートを SSRS サーバーから Power BI に移行できるようにするために、このツールを開発しました。 GitHub から入手でき、移行シナリオのエンドツーエンドのチュートリアルが付属しています。 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 以降の SQL Server Analysis Services テーブル モデルは、Azure の Analysis Services テーブル モデルの PaaS (サービスとしてのプラットフォーム) デプロイ モデルである Azure Analysis Services に移行できます。 Azure Analysis Services へのオンプレミス モデルの移行について詳しくは、[こちらのビデオ チュートリアル](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)をご覧ください。

また、[新しい XMLA の読み取りまたは書き込みエンドポイントを使用して Power BI Premium](/power-bi/admin/service-premium-connect-tools) にオンプレミスの Analysis Services テーブル モデルを移行することもできます。 

### <a name="high-availability"></a>高可用性

SQL Server の高可用性機能である Always On フェールオーバー クラスター インスタンスと Always On 可用性グループは、ターゲットの SQL マネージド インスタンスでは使用されなくなりました。 高可用性アーキテクチャは、SQL Managed Instance の [General Purpose (Standard 可用性モデル)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) と [Business Critical (Premium 可用性モデル)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) サービス レベルの両方に既に組み込まれています。 また、Premium 可用性モデルでは、読み取り専用の目的で、セカンダリ ノードのいずれかに接続できる読み取りスケールアウトも提供されます。     

SQL Managed Instance に含まれる高可用性アーキテクチャに加えて、[自動フェールオーバー グループ](../../database/auto-failover-group-overview.md)機能もあります。これを使用すると、マネージド インスタンス内のデータベースの別リージョンへのレプリケーションとフェールオーバーを管理できます。 

### <a name="sql-agent-jobs"></a>SQL Agent ジョブ

Azure Database Migration Service のオフライン オプションを使用して [SQL Agent ジョブ](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)を移行します。 これを行わない場合、SQL Server Management Studio を使用して Transact-SQL (T-SQL) でジョブをスクリプト化し、その後、ターゲット SQL Managed Instance でそれらを手動で再作成します。 

> [!IMPORTANT]
> 現在、Azure Database Migration Service では、T-SQL サブシステム ステップを使用するジョブのみがサポートされています。 SSIS パッケージのステップを使用するジョブは、手動で移行する必要があります。 

### <a name="logins-and-groups"></a>ログインとグループ

SQL ログインは、オフライン モードで Database Migration Service を使用して、SQL Server ソースから Azure SQL Managed Instance に移動します。  移行ウィザードの [[ログインの選択]](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) ペインを使用して、ログインをターゲット SQL Managed Instance に移行します。 

既定では Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。 ただし、次の方法で Windows ログインの移行を有効にできます。

- ターゲット SQL Managed Instance に Azure Active Directory (Azure AD) の読み取りアクセス権があることを確認します。 全体管理者の役割を持つユーザーは、Azure portal を使用してそのアクセス権を構成できます。
- Windows ユーザーまたはグループのログインの移行を有効にするように Azure Database Migration Service を構成します。 これは、Azure portal の **[構成]** ページで設定します。 この設定を有効にしたら、サービスを再起動して変更を有効にします。

サービスを再起動すると、Windows ユーザーまたはグループのログインが、移行可能なログインの一覧に表示されます。 移行する Windows ユーザーまたはグループのログインについて、関連付けられているドメイン名を指定するように求められます。 サービス ユーザー アカウント (ドメイン名 NT AUTHORITY を含むアカウント) と仮想ユーザー アカウント (ドメイン名 NT SERVICE を含むアカウント) はサポートされていません。 詳細については、[T-SQL を使用して SQL Server インスタンスの Windows ユーザーとグループを Azure SQL Managed Instance に移行する方法](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)に関する記事を参照してください。

または、Microsoft データ移行アーキテクトが特別に設計した [PowerShell ユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)を使用できます。 このユーティリティでは、ログインを再作成し、ソースからターゲットへのデータベース ユーザーを選択するために、PowerShell を使用して T-SQL スクリプトが作成されます。 

PowerShell ユーティリティでは、Windows Server Active Directory アカウントが Azure AD アカウントに自動的にマップされ、ソースの Active Directory インスタンスに対してログインごとに UPN 参照を実行できます。 このユーティリティでは、ロール メンバーシップおよびユーザー アクセス許可と共に、カスタム サーバーとデータベース ロールのスクリプトが作成されます。 包含データベースはまだサポートされていません。使用可能な SQL Server アクセス許可のサブセットのみがスクリプト化されます。 

### <a name="encryption"></a>暗号化

ネイティブ復元オプションを使用して、 [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)  によって保護されているデータベースをマネージド インスタンスに移行する場合は、データベースの復元 "*前*" に、ソース SQL Server インスタンスからターゲット SQL マネージド インスタンスに[対応する証明書を移行します](../../managed-instance/tde-certificate-migrate.md)。 

### <a name="system-databases"></a>システム データベース

システム データベースの復元はサポートされていません。 (マスターまたは msdb のデータベースに格納されている) インスタンスレベルのオブジェクトを移行するには、T-SQL を使用してそれらをスクリプト化してから、ターゲット マネージド インスタンス上にそれらを再作成します。 

### <a name="in-memory-oltp-memory-optimized-tables"></a>インメモリ OLTP (メモリ最適化テーブル)

SQL Server には、インメモリ OLTP 機能が用意されています。 これを使用すると、メモリ最適化テーブル、メモリ最適化テーブル型、ネイティブにコンパイルされた SQL モジュールを使用して、高いスループットと短い待機時間というトランザクション処理要件があるワークロードを実行できます。 

> [!IMPORTANT]
> インメモリ OLTP は、Azure SQL Managed Instance の Business Critical レベルでのみサポートされています。 General Purpose レベルではサポートされていません。

オンプレミスの SQL Server インスタンスにメモリ最適化テーブルまたはメモリ最適化テーブル型がある場合、Azure SQL Managed Instance に移行する際には、次のいずれかを行う必要があります。

- インメモリ OLTP がサポートされているターゲット SQL Managed Instance の Business Critical レベルを選択します。
- Azure SQL Managed Instance の General Purpose レベルに移行する場合は、データベースを移行する前に、メモリ最適化テーブル、メモリ最適化テーブル型、およびメモリ最適化オブジェクトと対話するネイティブにコンパイルされた SQL モジュールを削除します。 次の T-SQL クエリを使用すると、General Purpose レベルに移行する前に削除する必要があるすべてのオブジェクトを識別できます。

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

インメモリ テクノロジの詳細については、「[Azure SQL Database と Azure SQL Managed Instance でインメモリ テクノロジを使用してパフォーマンスを最適化する](../../in-memory-oltp-overview.md)」を参照してください。

## <a name="advanced-features"></a>高度な機能 

SQL Managed Instance に含まれているクラウドベースの高度な機能を活用してください。 たとえば、バックアップの管理はサービスによって行われるため、もうそれについて心配する必要はありません。 [保持期間内の任意の特定の時点](../../database/recovery-using-backups.md#point-in-time-restore)に復元できます。 さらに、 [高可用性は組み込まれている](../../database/high-availability-sla.md)ため、高可用性の設定について心配する必要もありません。 

セキュリティを強化するために、 [Azure AD 認証](../../database/authentication-aad-overview.md)、[監査](../../managed-instance/auditing-configure.md)、 [脅威の検出](../../database/azure-defender-for-sql.md)、 [行レベル セキュリティ](/sql/relational-databases/security/row-level-security)、 [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)の使用を検討してください。

高度な管理とセキュリティの機能に加えて、SQL Managed Instance では[ワークロードの監視とチューニング](../../database/monitor-tune-overview.md)に役立つ高度なツールが提供されています。 [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) を使用すると、多数のマネージド インスタンスを一元的に監視できます。マネージド インスタンスの [自動チューニング](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) では、SQL プラン実行のパフォーマンスが継続的に監視され、特定されたパフォーマンスの問題が自動的に修正されます。 

一部の機能は、[データベース互換レベル](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)を最新の互換性レベル (150) に変更すると使用できるようになります。 

## <a name="migration-assets"></a>移行資産 

さらに支援が必要な場合は、実際の移行プロジェクトのために開発された次のリソースを参照してください。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、ワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの対応性、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価を迅速に行うことができます。|
|[DBLoader ユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader を使用すると、区切りテキスト ファイルから SQL Server にデータを読み込むことができます。 この Windows コンソール ユーティリティでは、SQL Server ネイティブ クライアントの一括読み込みインターフェイスが使用されます。 このインターフェイスは、Azure SQL Managed Instance と共に、SQL Server のすべてのバージョンで動作します。|
|[オンプレミスの SQL Server ログインを Azure SQL Managed Instance に移動するユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|PowerShell スクリプトでは、ログインを再作成し、オンプレミスの SQL Server から Azure SQL Managed Instance にデータベース ユーザーを選択するための T-SQL コマンド スクリプトを作成できます。 このツールを使用すると、Windows Server Active Directory アカウントを Azure AD アカウントに自動的にマッピングできるだけでなく、必要に応じて SQL Server のネイティブ ログインを移行することもできます。|
|[Logman を使用した Perfmon データ収集の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Logman ツールを使用すると、Perfmon データを収集し (ベースライン パフォーマンスの把握に役立てるため)、移行ターゲットの推奨事項を取得できます。 このツールでは、logman.exe を使用して、リモート SQL Server インスタンスに設定されたパフォーマンス カウンターを作成、開始、停止、削除するコマンドを作成します。|
|[完全および差分バックアップを復元して Azure SQL Managed Instance にデータベースを移行する](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|このホワイトペーパーでは、完全および差分バックアップのみがある (ログ バックアップ機能はない) 場合に、SQL Server から Azure SQL Managed Instance への移行を加速させるのに役立つガイダンスと手順が提供されています。|

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。


## <a name="next-steps"></a>次のステップ

- SQL Server データベースの Azure SQL Managed Instance への移行を開始するには、[SQL Server から SQL Managed Instance への移行ガイド](sql-server-to-managed-instance-guide.md)のページを参照してください。

- データベースとデータの移行シナリオおよび特殊なタスクに役立つサービスとツールの一覧については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Managed Instance の詳細については、以下を参照してください。
   - [Azure SQL Managed Instance のサービス レベル](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server と Azure SQL Managed Instance の相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。

- データ アクセス レイヤーでの A/B テストの実行方法の詳細については、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) に関するページを参照してください。
