---
title: よく寄せられる質問 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL の SQL Managed Instance に関してよく寄せられる質問 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: b72952618b2d024bd2c4b445c3ea673ed523866b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247939"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL Managed Instance に関してよく寄せられる質問 (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) について特によく寄せられる質問を取り上げます。

## <a name="supported-features"></a>サポートされている機能

**SQL Managed Instance でサポートされている機能の一覧はどこで確認できますか?**

SQL Managed Instance でサポートされている機能の一覧については、[Azure SQL Managed Instance の機能](../database/features-comparison.md)に関するページを参照してください。

Azure SQL Managed Instance と SQL Server 間での構文と動作の違いについては、[T-SQL と SQL Server の相違点](transact-sql-tsql-differences-sql-server.md)に関するページを参照してください。


## <a name="tech-spec--resource-limits"></a>技術仕様およびリソースの制限
 
**SQL Managed Instance の技術的特性とリソース制限はどこで確認できますか?**

使用可能なハードウェアの世代の特性については、[ハードウェアの世代間の技術的相違点](resource-limits.md#hardware-generation-characteristics)に関するページを参照してください。
使用可能なサービス レベルとその特性については、[サービス レベル間の技術的相違点](resource-limits.md#service-tier-characteristics)に関するページを参照してください。

## <a name="known-issues--bugs"></a>既知の問題 & バグ

**既知の問題やバグはどこで確認できますか?**

バグや既知の問題については、「[既知の問題](../database/doc-changes-updates-release-notes.md#known-issues)」をご覧ください。

## <a name="new-features"></a>新機能

**最新の機能とパブリック プレビュー段階の機能はどこにありますか?**

新機能とプレビュー機能については、[リリース ノート](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)を参照してください。

## <a name="deployment-times"></a>デプロイ時間 

**インスタンスを作成または更新したり、データベースを復元したりするのにどれくらいの時間がかかりますか?**

SQL マネージド インスタンスの作成やサービス レベル (仮想コア、ストレージ) の変更にかかると予想される時間は、いくつかの要因に左右されます。 [こちら](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)の管理操作をご覧ください。 

## <a name="naming-convention"></a>命名規則

**SQL マネージド インスタンスにオンプレミスの SQL Server インスタンスと同じ名前を付けることはできますか?**

SQL Managed Instance の名前の変更は、サポートされていません。

SQL Managed Instance の既定の DNS ゾーン *.database.windows.net* は、変更できます。 

既定値の代わりに別の DNS ゾーン、たとえば *.contoso.com* などを使用するには、次のようにします。 
- CliConfig を使用して別名を定義する。 このツールは単なるレジストリ設定ラッパーなので、グループ ポリシーやスクリプトを使用して行うことも可能です。
- *TrustServerCertificate=true* オプションを指定した *CNAME* を使用する。

## <a name="move-db-from-mi"></a>MI から DB を移動する 

**データベースを SQL Managed Instance から元の SQL Server や Azure SQL Database に移動するには、どうすればよいですか?**

[データベースを BACPAC にエクスポート](../database/database-export.md)し、その [BACPAC ファイルをインポート]( ../database/database-import.md)できます。 データベースが 100 GB 未満の場合は、これが推奨される方法です。

データベース内のすべてのテーブルに主キーがある場合は、トランザクション レプリケーションを使用できます。

SQL Managed Instance から取得したネイティブの `COPY_ONLY` バックアップを SQL Server に復元することはできません。SQL Managed Instance には、SQL Server と比較してより新しいデータベース バージョンがあるためです。

## <a name="migrate-instance-db"></a>インスタンス DB の移行

**インスタンス データベースを単一の Azure SQL Database に移行するには、どうすればよいですか?**

1 つの方法として、[データベースを BACPAC にエクスポート](../database/database-export.md)し、その [BACPAC ファイルをインポート](../database/database-import.md)します。 

データベースが 100 GB 未満の場合は、これが推奨される方法です。 データベース内のすべてのテーブルに主キーがある場合は、トランザクション レプリケーションを使用できます。

## <a name="switch-hardware-generation"></a>ハードウェアの世代の切り替え 

**使用している SQL マネージド インスタンスのハードウェア世代を Gen 4 と Gen 5 間でオンラインで切り替えることはできますか?**

お使いの SQL マネージド インスタンスがプロビジョニングされているリージョンでハードウェアの世代が両方とも利用可能になっている場合は、ハードウェアの世代間のオンライン切り替えを自動で行うことができます。 この場合は、「[仮想コア モデルの概要](../database/service-tiers-vcore.md)」ページでハードウェアの世代間を切り替える方法を参照してください。

新しい SQL マネージド インスタンスがバックグラウンドでプロビジョニングされ、プロセスの最後に迅速なフェールオーバーによって古いインスタンスと新しいインスタンス間でデータベースが自動的に転送されるため、これは時間のかかる操作です。 

**ハードウェアの両方の世代が同じリージョンでサポートされていない場合はどうなりますか?**

ハードウェアの両方の世代が同じリージョンでサポートされていない場合、ハードウェアの世代を変更することはできますが、手動で行う必要があります。 その場合は、必要なハードウェアの世代が使用可能になっているリージョンに新しいインスタンスをプロビジョニングし、古いインスタンスと新しいインスタンスの間でデータのバックアップと復元を手動で行う必要があります。

**更新操作を実行できるだけの十分な IP アドレスが存在しない場合はどうなりますか?**

マネージド インスタンスのプロビジョニング先となるサブネットに十分な IP アドレスが存在しない場合は、新しいサブネットを作成したうえで、その中に新しいマネージド インスタンスを作成する必要があります。 また、新しいサブネットを作成するにあたっては、将来の更新操作で同様の問題が生じないよう、より多くの IP アドレスを確保することをお勧めします。適切なサブネット サイズについては、[VNet のサブネット サイズを決める方法](vnet-subnet-determine-size.md)に関するページを参照してください。 新しいインスタンスのプロビジョニング後、古いインスタンスと新しいインスタンスとの間で、データのバックアップと復元を手動で行うことができるほか、インスタンス間の[ポイントインタイム リストア](point-in-time-restore.md?tabs=azure-powershell)を実行することができます。 


## <a name="tune-performance"></a>パフォーマンスの調整

**使用している SQL マネージド インスタンスのパフォーマンスを調整するには、どうすればよいですか?**

General Purpose SQL Managed Instance では、データおよびログ ファイルのサイズがパフォーマンスにとって重要であるため、リモート ストレージが使用されます。 詳しくは、「[Impact of log file size on General Purpose SQL Managed Instance performance (General Purpose SQL Managed Instance のパフォーマンスに対するログ ファイルのサイズの影響)](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)」をご覧ください。

ワークロードが多数の小さなトランザクションで構成されている場合は、接続の種類をプロキシからリダイレクト モードに切り替えることを検討してください。

## <a name="maximum-storage-size"></a>最大ストレージ サイズ

**SQL Managed Instance の最大ストレージ サイズはどうなっていますか?**

SQL Managed Instance のストレージ サイズは選択したサービス レベル (General Purpose または Business Critical) に応じて異なります。 これらのサービス レベルのストレージ制限については、[サービス レベルの特性](../database/service-tiers-general-purpose-business-critical.md)に関するページを参照してください。

## <a name="back-up-storage-cost"></a>バックアップ ストレージのコスト 

**バックアップ ストレージは、使用している SQL Managed Instance ストレージから差し引かれますか?**

いいえ。バックアップ ストレージがお使いの SQL Managed Instance ストレージ領域から差し引かれることはありません。 バックアップ ストレージはインスタンス ストレージ領域から独立しており、サイズは制限されていません。 バックアップ ストレージは、インスタンス データベースのバックアップを保持する期間に制限があり、7 から 35 日の範囲内で構成可能です。 詳細については、「[自動バックアップ](../database/automated-backups-overview.md)」を参照してください。

## <a name="track-billing"></a>課金の追跡

**使用している SQL マネージド インスタンスの課金コストを追跡する方法はありますか?**

これを行うには、[Azure Cost Management ソリューション](/azure/cost-management/)を使用します。 [Azure portal](https://portal.azure.com) で **[サブスクリプション]** に移動し、 **[コスト分析]** を選択します。 

**[累積コスト]** オプションを使用し、その後 **[リソースの種類]** を `microsoft.sql/managedinstances` としてフィルター処理します。 
  
## <a name="inbound-nsg-rules"></a>インバウンド NSG ルール

**管理ポートでインバウンド NSG 規則を設定するには、どうすればよいですか?**

SQL の SQL Managed Instance コントロール プレーンでは、管理ポートを保護する NSG ルールが保持されます。

管理ポートの用途は次のとおりです。

ポート 9000 および 9003 は、Service Fabric インフラストラクチャによって使用されます。 Service Fabric の主な役割は、仮想クラスターを正常な状態に保ち、コンポーネントのレプリカ数に関して目標とする状態を維持することです。

ポート 1438、1440、および 1452 は、ノード エージェントによって使用されます。 Node Agent とは、クラスター内で実行され、コントロール プレーンによって管理コマンドを実行するために使用されるアプリケーションです。

NSG ルールに加えて、組み込みファイアウォールにより、ネットワーク レイヤー上のインスタンスが保護されます。 アプリケーション レイヤーの通信は証明書で保護されます。
  
詳細および組み込みファイアウォールの確認方法については、[Azure SQL Managed Instance の組み込みファイアウォール](management-endpoint-verify-built-in-firewall.md)に関するページを参照してください。


## <a name="mitigate-data-exfiltration-risks"></a>データ窃盗リスクを軽減する  

**データ窃盗リスクを軽減するにはどうすればいいですか?**

データ窃盗リスクを軽減するために、以下に示す一連のセキュリティ設定および制御を適用することをお勧めします。

- すべてのデータベースで [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) を有効にする。
- 共通言語ランタイム (CLR) を無効にする。 これは、オンプレミスでも推奨される設定です。
- Azure Active Directory (AAD) 認証のみを使用する。
- 低特権の DBA アカウントでインスタンスにアクセスする。
- sysadmin アカウント用に Jumpbox への JIT アクセスを構成する。
- [SQL 監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)を有効にしてアラート メカニズムと統合する。
- [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) スイートから[脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)を有効にする。


## <a name="cost-saving-use-cases"></a>コストを節約するユース ケース

**SQL Managed Instance のユース ケースやコスト削減の成果はどこで確認できますか?**

SQL Managed Instance のケース スタディ:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
SQL Managed Instance のデプロイに関連する利点、コスト、およびリスクについて理解を深めるには、次の Forrester 社による調査も参考になります: [MI の総合的な経済効果](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS の更新 

**DNS の更新はできますか?**

現在、SQL Managed Instance の DNS サーバー構成を更新する機能は提供されていません。

DNS 構成は、最終的に次の場合に更新されます。

- DHCP リースが期限切れになったとき。
- プラットフォームのアップグレード時。

対処方法として、SQL Managed Instance を 4 仮想コアにダウングレードしてから、後でアップグレードし直します。 これには、DNS 構成の更新という副作用があります。


## <a name="ip-address"></a>IP アドレス

**IP アドレスを使用して SQL Managed Instance に接続できますか?**

IP アドレスを使用した SQL Managed Instance への接続はサポートされていません。 SQL Managed Instance のホスト名は、SQL Managed Instance 仮想クラスターの前にあるロード バランサーにマップされます。 1 つの仮想クラスターでは複数の SQL マネージド インスタンスをホストすることが可能なため、名前を明示的に指定しないで適切な SQL Managed Instance に接続をルーティングすることはできません。

SQL Managed Instance 仮想クラスターのアーキテクチャの詳細については、「[仮想クラスターの接続アーキテクチャ](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)」を参照してください。

**SQL マネージド インスタンスに静的 IP アドレスを付与することはできますか?**

まれではあるものの必ず生じる状況として、SQL マネージド インスタンスを新しい仮想クラスターにオンラインで移行することが必要になる場合があります。 必要な場合、この移行を行うのは、サービスのセキュリティおよび信頼性の向上を目指してテクノロジ スタックに変更を加えるためです。 新しい仮想クラスターに移行すると、その SQL Managed Instance のホスト名にマップされている IP アドレスが変更されます。 SQL Managed Instance サービスでは、静的 IP アドレスのサポートの要求は行われておらず、定期的なメンテナンス サイクルの一環として、そのアドレスを予告なしに変更する権限が維持されています。

このような理由から、IP アドレスの不変性を当てにすると不要なダウンタイムの発生につながるので、変更を想定しておくことを強くお勧めします。

## <a name="change-time-zone"></a>タイムゾーンの変更

**既存の SQL マネージド インスタンスのタイム ゾーンを変更できますか?**

タイム ゾーン構成は、SQL マネージド インスタンスの初回のプロビジョニング時に設定できます。 既存の SQL マネージド インスタンスのタイム ゾーンの変更は、サポートされていません。 詳細については、[タイム ゾーンの制限](timezones-overview.md#limitations)に関する記事を参照してください。

対処方法としては、適切なタイム ゾーンで新しい SQL マネージド インスタンスを作成し、手動バックアップおよび復元を実行するか、[クロスインスタンスのポイントインタイム リストア](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) (推奨) を実行します。


## <a name="resolve-performance-issues"></a>パフォーマンスの問題の解決

**使用している SQL マネージド インスタンスのパフォーマンスの問題をどのように解決できますか?**

SQL Managed Instance と SQL Server のパフォーマンスを比較するために、まずは「[Azure SQL Managed Instance と SQL Server 間のパフォーマンス比較に関するベスト プラクティス](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)」の記事をご覧ください。

SQL Managed Instance でのデータ読み込みは、必須の完全復旧モデルと、トランザクション ログの書き込みスループットに対する[制限](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)が原因で、SQL Server よりも遅くなることがよくあります。 これは、一時的なデータをユーザー データベースではなく tempdb に読み込むか、またはクラスター化列ストアやメモリ最適化テーブルを使用することで対処できる場合があります。


## <a name="restore-encrypted-backup"></a>暗号化されたバックアップの復元

**暗号化されたデータベースを SQL Managed Instance に復元できますか?**

はい。SQL Managed Instance に復元するために、データベースを復号化する必要はありません。 暗号化されたバックアップ ファイルからデータを読み取れるように、ソース システム上で暗号化キーの保護機能として使用される証明書またはキーを SQL マネージド インスタンスに提供する必要があります。 その方法は次の 2 つです。

- *SQL マネージド インスタンスに証明書の保護機能をアップロードする。* これを実行できるのは、PowerShell を使用した場合のみです。 この[サンプル スクリプト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)にプロセス全体が記述されています。
- *非対称キーの保護機能を Azure Key Vault (AKV) にアップロードし、SQL Managed Instance がその保護機能を指すように指定する。* この方法は、暗号化キーの格納にも AKV 統合を使用する Bring Your Own Key (BYOK) TDE のユース ケースに似ています。 キーを暗号化キーの保護機能として使用せず、SQL Managed Instance によって暗号化されたデータベースが復元される際にキーを利用できるようにするだけの場合は、[BYOK TDE の設定](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)手順を実行し、 *[選択したキーを既定の TDE 保護機能にします]* チェック ボックスはオフにしてください。

暗号化保護機能を SQL Managed Instance で利用できるようにしたら、標準のデータベース復元手順を進めることができます。

## <a name="migrate-from-sql-database"></a>SQL Database からの移行 

**Azure SQL Database から SQL Managed Instance に移行するには、どうすればよいですか?**

SQL Managed Instance によって提供されるコンピューティングおよびストレージ サイズあたりのパフォーマンス レベルは、Azure SQL Database と同じです。 単一のインスタンスにデータを統合する場合、または単に SQL Managed Instance でのみサポートされている機能が必要な場合は、エクスポートおよびインポート (BACPAC) 機能を使用してデータを移行できます。

## <a name="password-policy"></a>パスワード ポリシー 

**SQL Managed Instance の SQL ログインには、どのようなパスワード ポリシーが適用されますか?**

SQL Managed Instance の SQL ログイン用のパスワード ポリシーは、マネージド インスタンスを保持する仮想クラスターを形成している VM に適用された Azure プラットフォーム ポリシーを継承します。 現時点では、これらの設定は Azure によって定義され、マネージド インスタンスによって継承されるため、変更することはできません。

 > [!IMPORTANT]
 > Azure プラットフォームでは、そのポリシーに依存するサービスに通知することなく、ポリシー要件を変更することが可能です。

**現在の Azure プラットフォーム ポリシーはどうなっていますか?**

各ログインでは、ログイン時にパスワードを設定し、有効期間の上限に達したらそのパスワードを変更する必要があります。

| **ポリシー** | **セキュリティ設定** |
| --- | --- |
| パスワードの有効期間 | 42 日 |
| パスワードの変更禁止期間 | 1 日 |
| パスワードの最小文字数 | 10 文字 |
| パスワードでは、複雑さの要件を満たす必要がある | Enabled |

**ログイン レベルで SQL Managed Instance のパスワードの複雑さと有効期限を無効にすることはできますか?**

はい。CHECK_POLICY および CHECK_EXPIRATION フィールドをログイン レベルで制御することが可能です。 現在の設定を確認するには、以下の T-SQL コマンドを実行します。

```sql
SELECT *
FROM sys.sql_logins
```

その後、以下を実行して、指定されているログイン設定を変更できます。

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(' test ' を目的のログイン名に置き換え、ポリシーと有効期限の値を調整してください)
