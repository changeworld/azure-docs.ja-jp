---
title: "Azure の回復性技術ガイダンス - データの破損または偶発的な削除からの復旧 | Microsoft Docs"
description: "データの破損または偶発的なデータの削除から復旧する方法、回復力と高可用性を備えたフォールト トレラント アプリケーションを設計する方法、障害復旧を計画する方法に関する記事"
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: ecdfa1a8-d93f-43f2-b4a3-f5cfd13201e7
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8dd45fb6dceddbd982be400f5d9af99e5023112e


---
# <a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Azure の回復性技術ガイダンス - データの破損または偶発的な削除からの復旧
堅牢なビジネス継続性計画の一環として、データが破損した場合や誤って削除された場合に備えた計画を作成します。 以下に、アプリケーション エラーによってデータが破損したか、オペレーターのエラーによってデータが誤って削除された後の復旧に関する情報を示します。

## <a name="virtual-machines"></a>Virtual Machines
Azure Virtual Machines (サービスとしてのインフラストラクチャ VM と呼ばれる場合もあります) をアプリケーション エラーや偶発的な削除から保護するには、 [Azure Backup](https://azure.microsoft.com/services/backup/)を使用します。 Azure Backup を使用すると、複数の VM ディスク間で一貫性のあるバックアップを作成できます。 さらに、バックアップ コンテナーをリージョン間でレプリケートし、リージョン損失からの復旧に備えることができます。

## <a name="storage"></a>Storage
Azure Storage は自動レプリカによるデータの回復性を備えていますが、アプリケーション コード (または開発者やユーザー) による偶発的または意図しない削除や更新などから発生するデータの破損を防ぐことはできません。 アプリケーション エラーやユーザー エラーが発生した場合にデータの忠実性を維持するには、監査ログと共にセカンダリ ストレージの場所にデータをコピーするなどの高度な手法が必要です。 開発者は BLOB の[スナップショット機能](https://msdn.microsoft.com/library/azure/ee691971.aspx)を利用できます。これは、特定の時点における BLOB の内容の読み取り専用スナップショットを作成する機能です。 この機能を Azure Storage BLOB のデータ忠実性ソリューションの基盤として使用できます。

### <a name="blob-and-table-storage-backup"></a>BLOB と Table Storage のバックアップ
BLOB とテーブルは高い持続性を持ち、常にデータの最新状態を表します。 意図しない変更やデータの削除からの復旧では、以前の状態にデータを復元する必要があります。 これは、Azure で提供されている、特定の時点のコピーを保存して保持する機能を活用することで実現できます。

Azure BLOB については、 [BLOB スナップショット機能](https://msdn.microsoft.com/library/ee691971.aspx)を使用して特定の時点のバックアップを行うことができます。 各スナップショットについて、BLOB 内で前回のスナップショットの状態との差分を保存するために必要なストレージのみに課金されます。 スナップショットには、差分を取るための基になる BLOB が存在している必要があるため、別の BLOB または別のストレージ アカウントにコピーすることをお勧めします。 これにより、バックアップ データを誤って削除してしまうことから確実に保護できます。 Azure テーブルについては、別のテーブルまたは別の Azure BLOB をコピー先として特定の時点のコピーを作成できます。 テーブルおよび BLOB に対してアプリケーション レベルのバックアップを実行する場合は、以下の詳細なガイダンスと例を参照してください。

* [Protecting Your Tables Against Application Errors (アプリケーション エラーからのテーブルの保護)](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
* [Protecting Your Blobs Against Application Errors (アプリケーション エラーからの BLOB の保護)](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

## <a name="database"></a>データベース
Azure SQL Database で使用できる [ビジネス継続性](../sql-database/sql-database-business-continuity.md) (バックアップ、復元) のオプションは複数あります。 データベースは、[データベース コピー](../sql-database/sql-database-copy.md)機能を使用するか、SQL Server bacpac ファイルを[エクスポート](../sql-database/sql-database-export.md)および[インポート](https://msdn.microsoft.com/library/hh710052.aspx)することでコピーできます。 データベース コピーではトランザクション上の一貫性が維持されますが、bacpac (Import/Export サービス) ではこの一貫性は維持されません。 これらのオプションの両方が、データセンター内でキュー ベースのサービスとして実行され、現時点では完了までの時間に関する SLA は提供されていません。

> [!NOTE]
> データベース コピーと Import/Export のオプションはソース データベースに大きな負荷がかかります。 そのため、リソースの競合や調整イベントがトリガーされることがあります。
> 
> 

### <a name="sql-database-backup"></a>SQL Database のバックアップ
Microsoft Azure SQL Database の特定の時点のバックアップは、 [Azure SQL データベースをコピーする](../sql-database/sql-database-copy.md)ことによって実現します。 このコマンドを使用すると、同じ論理データベース サーバー上に、または別のサーバーに、トランザクション上の一貫性があるデータベースのコピーを作成できます。 いずれの場合も、データベース コピーは完全に機能し、ソース データベースから完全に独立しています。 作成する各コピーは、特定の時点の回復オプションを表します。 ソース データベース名で新しいデータベースの名前を変更して、データベースの状態を完全に回復できます。 または、Transact-SQL クエリを使用して新しいデータベースからデータの特定のサブセットを復元することができます。 SQL Database の詳細については、「 [Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」を参照してください。

### <a name="sql-server-on-virtual-machines-backup"></a>Virtual Machines 上の SQL Server のバックアップ
Azure のサービスとしてのインフラストラクチャである仮想マシン (多くの場合、IaaS または IaaS VM と呼ばれます) で使用される SQL Server の場合、従来のバックアップとログ配布という 2 つのオプションがあります。 従来のバックアップを使用すると、特定の時点の状態に復元できますが、回復プロセスは低速です。 従来のバックアップを復元するには、最初に完全バックアップから開始し、その後にそれ以降に作成されたバックアップを適用する必要があります。 2 番目のオプションでは、ログ バックアップの復元を (たとえば 2 時間) 遅延するログ配布セッションを構成します。 これにより、プライマリで発生したエラーから回復するための時間が確保されます。

## <a name="other-azure-platform-services"></a>その他の Azure プラットフォーム サービス
Azure の一部のプラットフォーム サービスは、ユーザー制御のストレージ アカウントまたは Azure SQL Database に情報を格納します。 アカウントまたはストレージのリソースが削除されたか破損した場合は、サービスに重大なエラーが発生する可能性があります。 そのような場合に備えて、バックアップを保持することが重要です。バックアップがあれば、リソースが削除されたか破損した場合にリソースを作成し直すことができます。

Azure Web Sites と Azure Mobile Services では、関連付けられたデータベースをバックアップし、管理する必要があります。 Azure Media Service と Virtual Machines では、関連付けられた Azure Storage アカウントとそのアカウントのすべてのリソースを管理する必要があります。 たとえば、Virtual Machines の場合、Azure Blob Storage で VM ディスクをバックアップし、管理する必要があります。

## <a name="checklists-for-data-corruption-or-accidental-deletion"></a>データの破損または偶発的な削除に対するチェックリスト
## <a name="virtual-machines-checklist"></a>Virtual Machines のチェックリスト
1. このドキュメントの「Virtual Machines」セクションを確認する。
2. Azure Backup (または Azure Blob Storage と VHD スナップショットを使用した独自のバックアップ システム) を使用して、VM ディスクをバックアップし、管理する。

## <a name="storage-checklist"></a>Storage のチェックリスト
1. このドキュメントの「Storage」セクションを確認する。
2. 重要なストレージ リソースを定期的にバックアップする。
3. BLOB に対してスナップショット機能の使用を検討する。

## <a name="database-checklist"></a>Database のチェックリスト
1. このドキュメントの「データベース」セクションを確認する。
2. データベース コピー コマンドを使用して特定の時点のバックアップを作成する。

## <a name="sql-server-on-virtual-machines-backup-checklist"></a>Virtual Machines 上の SQL Server のバックアップ チェックリスト
1. このドキュメントの「Virtual Machines 上の SQL Server のバックアップ」セクションを確認する。
2. 従来のバックアップと復元の手法を使用する。
3. 遅延ログ配布セッションを作成する。

## <a name="web-apps-checklist"></a>Web Apps のチェックリスト
1. 関連付けられたデータベースがある場合は、バックアップして管理する。

## <a name="media-services-checklist"></a>Media Services のチェックリスト
1. 関連付けられたストレージ リソースをバックアップして管理する。

## <a name="more-information"></a>詳細情報
Azure のバックアップと復元の機能の詳細については、 [ストレージ、バックアップ、復旧のシナリオ](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
この記事は、 [Azure の回復性技術ガイダンス](resiliency-technical-guidance.md)について重点的に説明したシリーズの一部です。 回復性、障害復旧、高可用性に関するその他のリソースを探している場合は、Azure の回復性技術ガイダンスの「[その他のリソース](resiliency-technical-guidance.md#additional-resources)」を参照してください。




<!--HONumber=Nov16_HO3-->


