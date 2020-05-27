---
title: マネージド インスタンスの FAQ
description: SQL Database マネージド インスタンスに関してよく寄せられる質問 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 3ffa4bc905a08c1757865db7bab828193ff3c7ea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770144"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database マネージド インスタンスに関してよく寄せられる質問 (FAQ)

この記事には、[SQL Database マネージド インスタンス](sql-database-managed-instance.md)について特によく寄せられる質問が数多く記載されています。

## <a name="supported-features"></a>サポートされている機能

**マネージド インスタンスでサポートされている機能の一覧はどこで確認できますか?**

マネージド インスタンスでサポートされている機能の一覧については、[Azure SQL Database と SQL Server の比較](sql-database-features.md)に関するページを参照してください。

Azure SQL Database マネージド インスタンスとオンプレミス SQL Server の構文および動作の違いについては、[T-SQL と SQL Server の相違点](sql-database-managed-instance-transact-sql-information.md)に関するページを参照してください。


## <a name="tech-spec--resource-limits"></a>技術仕様およびリソースの制限
 
**マネージド インスタンスの技術的特性とリソース制限はどこで確認できますか?**

使用可能なハードウェアの世代の特性については、[ハードウェアの世代間の技術的相違点](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)に関するページを参照してください。
使用可能なサービス レベルとその特性については、[サービス レベル間の技術的相違点](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)に関するページを参照してください。

## <a name="known-issues--bugs"></a>既知の問題 & バグ

**既知の問題やバグはどこで確認できますか?**

バグや既知の問題については、「[既知の問題](sql-database-release-notes.md#known-issues)」をご覧ください。

## <a name="new-features"></a>新機能

**最新の機能とパブリック プレビュー段階の機能はどこにありますか?**

新機能とプレビュー機能については、[リリース ノート](sql-database-release-notes.md?tabs=managed-instance)を参照してください。

## <a name="deployment-times"></a>デプロイ時間 

**インスタンスを作成または更新したり、データベースを復元したりするのにどれくらいの時間がかかりますか?**

新しいマネージ インスタンスの作成やサービス レベル (仮想コアやストレージ) の変更に予想される時間は、いくつかの要因に左右されます。 [こちら](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)の管理操作をご覧ください。 

## <a name="naming-convention"></a>命名規則

**マネージド インスタンスにオンプレミスの SQL Server と同じ名前を付けることはできますか?**

マネージド インスタンス名の変更はサポートされていません。

マネージド インスタンスの既定の DNS ゾーン *.database.windows.net* は変更できました。 

既定値の代わりに別の DNS ゾーン、たとえば *.contoso.com* などを使用するには、次のようにします。 
- CliConfig を使用して別名を定義する。 このツールは単なるレジストリ設定ラッパーなので、グループ ポリシーやスクリプトを使用して行うことも可能です。
- *TrustServerCertificate=true* オプションを指定した *CNAME* を使用する。

## <a name="move-db-from-mi"></a>MI から DB を移動する 

**データベースをマネージド インスタンスから元の SQL Server や Azure SQL Database に移動するには、どうすればよいですか?**

[データベースを BACPAC にエクスポート](sql-database-export.md)し、その [BACPAC ファイルをインポート]( sql-database-import.md)できます。 データベースが 100 GB 未満の場合は、これが推奨される方法です。

データベース内のすべてのテーブルに主キーがある場合は、トランザクション レプリケーションを使用できます。

マネージド インスタンスから取得したネイティブの `COPY_ONLY` バックアップを SQL Server に復元することはできません。マネージド インスタンスには、SQL Server と比較して新しいバージョンのデータベースがあるためです。

## <a name="migrate-instance-db"></a>インスタンス DB の移行

**インスタンス データベースを単一の Azure SQL Database に移行するには、どうすればよいですか?**

1 つの方法として、[データベースを BACPAC にエクスポート](sql-database-export.md)し、その [BACPAC ファイルをインポート](sql-database-import.md)します。 

データベースが 100 GB 未満の場合は、これが推奨される方法です。 データベース内のすべてのテーブルに主キーがある場合は、トランザクション レプリケーションを使用できます。

## <a name="switch-hardware-generation"></a>ハードウェアの世代の切り替え 

**マネージド インスタンスのハードウェア世代の Gen 4 と Gen 5 をオンラインで切り替えることはできますか?**

マネージド インスタンスがプロビジョニングされているリージョンでハードウェアの世代が両方とも利用可能になっている場合は、ハードウェアの世代間のオンライン切り替えを自動で行うことができます。 この場合は、「[仮想コア モデルの概要](sql-database-service-tiers-vcore.md)」ページでハードウェアの世代間を切り替える方法を参照してください。

これは、新しいマネージド インスタンスがバックグラウンドでプロビジョニングされ、データベースがプロセスの最後の迅速なフェールオーバーによって古いインスタンスと新しいインスタンスの間で自動的に転送されるため、時間のかかる操作です。 

**ハードウェアの両方の世代が同じリージョンでサポートされていない場合はどうなりますか?**

ハードウェアの両方の世代が同じリージョンでサポートされていない場合、ハードウェアの世代を変更することはできますが、手動で行う必要があります。 その場合は、必要なハードウェアの世代が使用可能になっているリージョンに新しいインスタンスをプロビジョニングし、古いインスタンスと新しいインスタンスの間でデータのバックアップと復元を手動で行う必要があります。

**更新操作を実行できるだけの十分な IP アドレスが存在しない場合はどうなりますか?**

マネージド インスタンスのプロビジョニング先となるサブネットに十分な IP アドレスが存在しない場合は、新しいサブネットを作成したうえで、その中に新しいマネージド インスタンスを作成する必要があります。 また、新しいサブネットを作成するにあたっては、将来の更新操作で同様の問題が生じないよう、より多くの IP アドレスを確保することをお勧めします。適切なサブネット サイズについては、[VNet のサブネット サイズを決める方法](sql-database-managed-instance-determine-size-vnet-subnet.md)に関するページを参照してください。 新しいインスタンスのプロビジョニング後、古いインスタンスと新しいインスタンスとの間で、データのバックアップと復元を手動で行うことができるほか、インスタンス間の[ポイントインタイム リストア](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell)を実行することができます。 


## <a name="tune-performance"></a>パフォーマンスの調整

**マネージド インスタンスのパフォーマンスを調整するには、どうすればよいですか?**

General Purpose マネージド インスタンスでは、データおよびログ ファイルのサイズがパフォーマンスにとって重要であるため、リモート ストレージが使用されます。 詳しくは、「[General Purpose Managed Instance のパフォーマンスに対するログ ファイルのサイズの影響](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)」をご覧ください。

ワークロードが多数の小さなトランザクションで構成されている場合は、接続の種類をプロキシからリダイレクト モードに切り替えることを検討してください。

## <a name="maximum-storage-size"></a>最大ストレージ サイズ

**マネージド インスタンスの最大ストレージ サイズは?**

マネージド インスタンスのストレージ サイズは選択したサービス レベル (General Purpose または Business Critical) によって異なります。 これらのサービス レベルのストレージ制限については、[サービス レベルの特性](sql-database-service-tiers-general-purpose-business-critical.md)に関するページを参照してください。

## <a name="back-up-storage-cost"></a>バックアップ ストレージのコスト 

**バックアップ ストレージは、マネージド インスタンス ストレージから差し引かれますか?**

いいえ。バックアップ ストレージがマネージド インスタンス ストレージ領域から差し引かれることはありません。 バックアップ ストレージはインスタンス ストレージ領域から独立しており、サイズは制限されていません。 バックアップ ストレージは、インスタンス データベースのバックアップを保持する期間に制限があり、7 から 35 日の範囲内で構成可能です。 詳細については、「[自動バックアップ](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)」を参照してください。

## <a name="track-billing"></a>課金の追跡

**マネージド インスタンスの課金コストを追跡する方法はありますか?**

これを行うには、[Azure Cost Management ソリューション](/azure/cost-management/)を使用します。 [Azure portal](https://portal.azure.com) で **[サブスクリプション]** に移動し、 **[コスト分析]** を選択します。 

**[累積コスト]** オプションを使用し、その後 **[リソースの種類]** を `microsoft.sql/managedinstances` としてフィルター処理します。 
  
## <a name="inbound-nsg-rules"></a>インバウンド NSG ルール

**管理ポートでインバウンド NSG 規則を設定するには、どうすればよいですか?**

マネージド インスタンス コントロール プレーンは、管理ポートを保護する NSG ルールを保持します。

管理ポートの用途は次のとおりです。

ポート 9000 および 9003 は、Service Fabric インフラストラクチャによって使用されます。 Service Fabric の主な役割は、仮想クラスターを正常な状態に保ち、コンポーネントのレプリカ数に関して目標とする状態を維持することです。

ポート 1438、1440、および 1452 は、ノード エージェントによって使用されます。 Node Agent とは、クラスター内で実行され、コントロール プレーンによって管理コマンドを実行するために使用されるアプリケーションです。

NSG ルールに加えて、組み込みファイアウォールにより、ネットワーク レイヤー上のインスタンスが保護されます。 アプリケーション レイヤーの通信は証明書で保護されます。
  
詳細および組み込みファイアウォールの確認方法については、[Azure SQL Database マネージド インスタンスの組み込みファイアウォール](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)に関するページを参照してください。


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

**マネージド インスタンスのユース ケースやコスト削減の成果はどこで確認できますか?**

マネージド インスタンスのケース スタディ:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Azure SQL Database マネージド インスタンスのデプロイに関連する利点、コスト、リスクについて理解を深めるために、次の Forrester による調査結果もあります: [MI の総合的な経済効果](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS の更新 

**DNS の更新はできますか?**

現在、マネージド インスタンスの DNS サーバー構成を更新する機能は提供されていません。

DNS 構成は、最終的に次の場合に更新されます。

- DHCP リースが期限切れになったとき。
- プラットフォームのアップグレード時。

回避策として、マネージド インスタンスを 4 個の仮想コアにダウングレードしてからアップグレードし直します。 これには、DNS 構成の更新という副作用があります。


## <a name="ip-address"></a>IP アドレス

**IP アドレスを使用してマネージド インスタンスに接続できますか?**

IP アドレスを使用したマネージド インスタンスへの接続はサポートされていません。 マネージド インスタンスのホスト名は、マネージド インスタンスの仮想クラスターの前にあるロード バランサーにマップされます。 1 つの仮想クラスターが複数のマネージド インスタンスをホストすることが可能なため、名前を指定しないで適切なマネージド インスタンスに接続をルーティングすることはできません。

マネージド インスタンス仮想クラスターのアーキテクチャの詳細については、「[仮想クラスターの接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)」を参照してください。

**マネージド インスタンスに静的 IP アドレスを付与することはできますか?**

まれではあるものの不可欠な状況として、マネージド インスタンスを新しい仮想クラスターにオンラインで移行することが必要になる場合があります。 必要な場合、この移行を行うのは、サービスのセキュリティおよび信頼性の向上を目指してテクノロジ スタックに変更を加えるためです。 新しい仮想クラスターに移行すると、マネージド インスタンスのホスト名にマップされている IP アドレスが変更されます。 マネージド インスタンス サービスは、静的 IP アドレスのサポートを要求することはなく、定期的なメンテナンス サイクルの一環として、そのアドレスを予告なしに変更する権限を有しています。

このような理由から、IP アドレスの不変性を当てにすると不要なダウンタイムの発生につながるので、変更を想定しておくことを強くお勧めします。

## <a name="change-time-zone"></a>タイムゾーンの変更

**既存のマネージド インスタンスのタイム ゾーンを変更できますか?**

タイム ゾーン構成は、マネージド インスタンスの初回プロビジョニング時に設定できます。 既存のマネージド インスタンスのタイム ゾーンの変更はサポートされていません。 詳細については、[タイム ゾーンの制限](sql-database-managed-instance-timezone.md#limitations)に関する記事を参照してください。

対処方法には、適切なタイム ゾーンで新しいマネージド インスタンスを作成し、手動バックアップおよび復元を実行するか、[クロスインスタンスのポイントインタイム リストア](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) (推奨) を実行する方法があります。


## <a name="resolve-performance-issues"></a>パフォーマンスの問題の解決

**マネージド インスタンスのパフォーマンスの問題をどのように解決できますか?**

マネージド インスタンスと SQL Server のパフォーマンスを比較するために、まずは「[Azure SQL マネージド インスタンスと SQL Server のパフォーマンス比較に関するベスト プラクティス](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)」の記事をご覧ください。

マネージド インスタンスでのデータ読み込みは、必須の完全復旧モデルと、トランザクション ログの書き込みスループットに対する[制限](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)が原因で、SQL Server よりも遅くなることがよくあります。 これは、一時的なデータをユーザー データベースではなく tempdb に読み込むか、またはクラスター化列ストアやメモリ最適化テーブルを使用することで対処できる場合があります。


## <a name="restore-encrypted-backup"></a>暗号化されたバックアップの復元

**暗号化されたデータベースをマネージド インスタンスに復元できますか?**

はい。データベースを復号化しなくても、マネージド インスタンスに復元できます。 暗号化されたバックアップ ファイルからデータを読み取れるように、ソース システムで暗号化キーの保護機能として使用される証明書またはキーをマネージド インスタンスに提供する必要があります。 その方法は次の 2 つです。

- *マネージド インスタンスに証明書の保護機能をアップロードする。* これを実行できるのは、PowerShell を使用した場合のみです。 この[サンプル スクリプト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)にプロセス全体が記述されています。
- *非対称キーの保護機能を Azure Key Vault (AKV) にアップロードし、マネージド インスタンスがその保護機能を指すよう指定する。* この方法は、暗号化キーの格納にも AKV 統合を使用する Bring Your Own Key (BYOK) TDE のユース ケースに似ています。 キーを暗号化キーの保護機能として使用せず、マネージド インスタンスが暗号化されたデータベースを復元するためにキーを利用できるようにするだけの場合は、[BYOK TDE の設定](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)手順を実行し、 *[選択したキーを既定の TDE 保護機能にします]* チェック ボックスはオフにしてください。

暗号化保護機能をマネージド インスタンスで利用できるようにしたら、標準データベース復元手順を進めることができます。

## <a name="migrate-from-single-db"></a>単一の DB からの移行 

**Azure SQL Database の単体またはエラスティック プールからマネージド インスタンスに移行するには、どうすればよいですか?**

マネージド インスタンスで提供されるコンピューティングおよびストレージ サイズあたりのパフォーマンス レベルは、Azure SQL Database の他のデプロイ オプションと同じです。 単一インスタンスでデータを統合する場合または単にマネージド インスタンスでのみサポートされている機能が必要な場合は、エクスポートおよびインポート (BACPAC) 機能を使用してデータを移行できます。
