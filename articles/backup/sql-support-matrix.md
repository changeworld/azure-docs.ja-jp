---
title: Azure VM 内の SQL Server のバックアップに関する Azure Backup のサポート マトリックス
description: Azure Backup サービスを使用して Azure VM 内の SQL Server をバックアップする場合のサポート設定と制限事項について概説します。
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: references_regions
ms.openlocfilehash: c0b46a1c75c47b85985946646bf3216c9ab719f5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219264"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure VM 内の SQL Server のバックアップに関するサポート マトリックス

Azure Backup を使用すると、Microsoft Azure クラウド プラットフォームでホストされている Azure VM 内の SQL Server データベースをバックアップできます。 この記事では、Azure VM 内の SQL Server のバックアップのシナリオとデプロイの一般的なサポート設定と制限事項について説明します。

## <a name="scenario-support"></a>シナリオのサポート

**サポート** | **詳細**
--- | ---
**サポートされているデプロイ** | SQL Marketplace の Azure VM と、Marketplace 以外の (SQL Server が手動でインストールされる) VM がサポートされています。
**サポートされているリージョン** | SQL Server データベース用 Azure Backup は、フランス南部 (FRS)、英国北部 (UKN)、英国南部 2 (UKS2)、UG アイオワ (UGI)、ドイツ (Black Forest) を除くすべてのリージョンで利用できます。
**サポートされているオペレーティング システム** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> Linux は現在サポートされていません。
**サポートされる SQL Server のバージョン** | SQL Server 2019、SQL Server 2017 (詳細は[「製品のライフサイクルの検索」ページ](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)を参照)、SQL Server 2016 および SP (詳細は[「製品のライフサイクルの検索」ページ](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)を参照)、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。<br><br>Express LocalDB のバージョンはサポートされていません。
**サポートされている .NET バージョン** | VM にインストールされている .NET Framework 4.5.2 以降
**サポートされているデプロイ** | SQL Marketplace の Azure VM と、Marketplace 以外の (SQL Server が手動でインストールされる) VM がサポートされています。 スタンドアロン インスタンスのサポートは、常に[可用性グループ](backup-sql-server-on-availability-groups.md)で行います。

## <a name="feature-considerations-and-limitations"></a>機能の考慮事項と制限事項

|設定  |上限 |
|---------|---------|
|サーバー (およびコンテナー内) で保護できるデータベースの数    |   2000      |
|サポートされているデータベース サイズ (これを超えると、パフォーマンスの問題が発生する可能性があります)   |   6 TB*      |
|データベースでサポートされているファイルの数    |   1000      |
|1 日あたりにサポートされる完全バックアップの数    |    1 つのスケジュールされたバックアップ。 <br><br> 3 つのオンデマンド バックアップ。 <br><br> 1 日に 3 回以上のバックアップをトリガーしないことをお勧めします。 ただし、試行が失敗した場合にユーザーが再試行できるようにするために、オンデマンド バックアップのハード制限は 9 回の試行に設定されます。 |

_* データベース サイズの制限は、サポートされているデータ転送速度と、バックアップの制限時間の構成によって異なります。これは、ハード制限ではありません。バックアップのスループットのパフォーマンスに関する [詳細](#backup-throughput-performance)をご覧ください。_

* SQL Server のバックアップは、Azure portal または **PowerShell** を使用して構成できます。 CLI はサポートされていません。
* このソリューションは、Azure Resource Manager VM とクラシック VM のどちらの種類の[デプロイ](../azure-resource-manager/management/deployment-models.md)でもサポートされます。
* すべてのバックアップの種類 (完全、差分、ログ) と復旧モデル (簡易、完全、一括ログ) がサポートされています。
* **読み取り専用** データベースの場合: 完全とコピーのみの完全バックアップだけが、バックアップの種類としてサポートされています。
* SQL ネイティブの圧縮は、バックアップ ポリシーでユーザーが明示的に有効にした場合にサポートされます。 Azure Backup を使用すると、ユーザーが設定したこのコントロールの値に応じて、COMPRESSION または NO_COMPRESSION 句を使用してインスタンス レベルの既定値がオーバーライドされます。
* TDE - 有効なデータベース バックアップがサポートされています。 TDE で暗号化されたデータベースを別の SQL Server に復元するには、まず[証明書を宛先サーバーに復元する](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)必要があります。 SQL Server 2016 以降のバージョンでは、TDE が有効になっているデータベースのバックアップの圧縮は使用できますが、[ここ](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)で説明されているように転送サイズは低くなります。
* ミラー データベースおよびデータベース スナップショットのバックアップ操作および復元操作はサポートされていません。
* SQL Server **フェールオーバー クラスター インスタンス (FCI)** はサポートされていません。

## <a name="backup-throughput-performance"></a>Backup のスループットのパフォーマンス

Azure Backup では、大規模な SQL データベース (500 GB) の完全および差分バックアップで、200 Mbps の一貫したデータ転送率がサポートされています。 最適なパフォーマンスを得るために、次のことを確認してください。

- 基になる VM (データベースをホストする SQL Server インスタンスを含む) が、必要なネットワーク スループットを使用して構成されている。 VM の最大スループットが 200 Mbps 未満の場合、Azure Backup は最適な速度でデータを転送できません。<br>また、データベース ファイルを含むディスクには、十分なスループットがプロビジョニングされている必要があります。 Azure VM でのディスクのスループットとパフォーマンスの詳細については、[こちら](../virtual-machines/disks-performance.md)をご覧ください。 
- VM で実行されているプロセスが VM の帯域幅を消費していない。 
- バックアップ スケジュールが、データベースのサブセット全体に分散されている。 VM で同時に実行される複数のバックアップが、バックアップ間でネットワーク消費率を共有している。 同時バックアップの数を制御する方法については、[こちら](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-)をご覧ください。

>[!NOTE]
> VM リソース、帯域幅、バックアップ ポリシーに基づいて、サーバーごとに推奨される保護されたデータベースの概数を算出するために、[詳細なリソース プランナーをダウンロードします](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)。

## <a name="next-steps"></a>次のステップ

Azure VM 上で実行されている[SQL Server データベースをバックアップする](backup-azure-sql-database.md)方法を学習します。