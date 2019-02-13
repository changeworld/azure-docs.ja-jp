---
title: クイック スタート - Azure SQL Database マネージド インスタンス | Microsoft Docs
description: Azure SQL Database - マネージド インスタンスの基本的な使用方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a70e83737c6b56aee3279375ec653f12810b13b4
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749816"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスの概要

[マネージド インスタンス](sql-database-managed-instance-index.yml) デプロイ オプションでは、最新の SQL Server オンプレミス (Enterprise Edition) データベース エンジンとの 100% 近い互換性を持つデータベースが作成されます。また、セキュリティに関する一般的な懸念事項に対処するネイティブの[仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 実装のほか、オンプレミスの SQL Server の顧客に有利な[ビジネス モデル](https://azure.microsoft.com/pricing/details/sql-database/)を提供します。 このセクションでは、マネージド インスタンスの構成と作成、データベースの移行を迅速に行う方法について説明します。

## <a name="quickstart-overview"></a>クイック スタートの概要

以下のクイック スタートを使用すると、マネージド インスタンスの迅速な作成、クライアント アプリケーション用の仮想マシンやポイント対サイト VPN 接続の構成のほか、`.bak` ファイルを使用した新しいマネージド インスタンスへのデータベースの復元を行うことができます。

- [Azure portal を使用してマネージド インスタンスを作成します](sql-database-managed-instance-get-started.md)。 Azure portal では、必要なパラメーター (ユーザー名/パスワード、コア数、最大ストレージ) を構成します。また、Azure ネットワーク環境を自動的に作成できます。ネットワークの詳細やインフラストラクチャの要件を把握している必要はありません。 マネージド インスタンスを作成できる[タイプのサブスクリプション](sql-database-managed-instance-resource-limits.md#supported-subscription-types)を持っていることの確認のみが必要です。 独自のネットワークを所有していてそれを使用したい場合、またはネットワークをカスタマイズしたい場合は、マネージド インスタンス用に[ネットワーク環境を構成する](#configure-network-environment)方法を参照してください。
- マネージド インスタンスは、パブリック エンドポイントを持たない独自の VNet に作成されます。 クライアント アプリケーションのアクセスのために、以下のクイック スタートのいずれかを使用して、同じ VNet (異なるサブネット) 内に VM を作成するか、クライアント コンピューターから VNet へのポイント対サイト VPN 接続を作成できます。
  - SQL Server Management Studio などのクライアント アプリケーションの接続用に、[マネージド インスタンス VNet に Azure 仮想マシン](sql-database-managed-instance-configure-vm.md)を作成します。
  - SQL Server Management Studio およびその他のクライアント接続アプリケーションがあるクライアント コンピューターから[マネージド インスタンスへのポイント対サイト VPN 接続](sql-database-managed-instance-configure-p2s.md)を設定します。 これは、マネージド インスタンスとその VNet への接続に関する 2 つのオプションのうちのもう一方です。

  > [!NOTE]
  > ローカル ネットワークから Express Route またはサイト間接続を使用することもできますが、このような方法はこれらのクイック スタートでは扱いません。

マネージド インスタンスを作成してアクセスを構成したら、オンプレミスまたは Azure VM 上の SQL Server に配置されているデータベースの移行を開始できます。 移行するソース データベースにサポートされていない機能がある場合、移行は失敗します。 失敗を回避し、互換性を確認するために、[Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) をインストールできます。これにより、SQL Server 上のデータベースが分析され、マネージド インスタンスへの移行を妨げる可能性がある問題 ([FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) や複数のログ ファイルの存在など) が特定されます。 このような問題を解決すると、データベースはマネージド インスタンスに移行できる状態になります。 もう 1 つの便利なツールとして、[Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) があります。このツールを使用すると、SQL Server 上のワークロードを記録し、マネージド インスタンス上で再生することができます。そのため、マネージド インスタンスに移行した場合にパフォーマンスの問題が発生するかどうかを判断できます。

データベースをマネージド インスタンスに移行できる状態になったら、ネイティブの SQL Server 復元機能を使用して、データベースを `.bak` ファイルからマネージド インスタンスに復元できます。 クイック スタートについては、[バックアップからマネージド インスタンスへの復元](sql-database-managed-instance-get-started-restore.md)に関するページを参照してください。 このクイック スタートでは、`RESTORE` Transact-SQL コマンドを使用して、Azure Blob Storage に格納されている `.bak` ファイルから復元します。 

> [!TIP]
> `BACKUP` Transact-SQL コマンドを使用して Azure Blob Storage にデータベースのバックアップを作成する場合は、「[SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)」を参照してください。

これらのクイック スタートを使用すると、データベース バックアップを迅速に作成、構成し、マネージド インスタンスに復元できます。 シナリオによっては、マネージド インスタンスと必要なネットワーク環境のデプロイをカスタマイズまたは自動化する必要があります。 ここでは、これらのシナリオについて説明します。

## <a name="customizing-network-environment"></a>ネットワーク環境のカスタマイズ

Azure portal を使用してインスタンスを作成するときに、VNet/サブネットを自動的に構成できますが、VNet およびサブネットのパラメーターを構成できるように、マネージド インスタンスの作成を開始する前に VNet/サブネットを作成したい場合があります。 ネットワーク環境を作成および構成するときの最も簡単な方法は、[Azure リソース デプロイ](sql-database-managed-instance-create-vnet-subnet.md) テンプレートを使用して、マネージド インスタンスのネットワークとサブネットを作成および構成する方法です。 必要な操作は、Azure Resource Manager のデプロイ ボタンを押し、フォームにパラメーターを入力することのみです。 

別の方法として、この [PowerShell スクリプト](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)を使用してネットワークの作成を自動化することもできます。

マネージド インスタンスをデプロイする VNet とサブネットが既にある場合は、その VNet とサブネットが[ネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)を満たしていることを確認する必要があります。 この [PowerShell スクリプトを使用して、サブネットが正しく構成されていることを確認](sql-database-managed-instance-configure-vnet-subnet.md)します。 このスクリプトでは、ネットワークが検証され、問題がレポートされるだけでなく、変更が必要な点が示されるほか、VNet/サブネットに必要な変更を加えられるようになります。 VNet/サブネットを手動で構成しない場合は、このスクリプトを実行します。 ネットワーク インフラストラクチャの大規模な再構成後にも、これを実行できます。 独自のネットワークを作成および構成する場合は、[接続のアーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)に関するページや、この[マネージド インスタンス環境を作成および構成するための完全ガイド](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)を参照してください。

## <a name="automating-creation-of-a-managed-instance"></a>マネージド インスタンスの自動作成

 前の手順での説明のようにネットワーク環境を作成していない場合は、Azure portal で自動的に作成できます。唯一の欠点は、後で変更できない既定のパラメーターをいくつか使用して構成されることです。 代替手段として、次のものを使用できます。

- [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
- [PowerShell と Resource Manager テンプレート](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/)。

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>最小限のダウンタイムでのマネージド インスタンスへの移行

これらのクイック スタートの記事を使用すると、マネージド インスタンスを簡単に設定し、ネイティブの `RESTORE` 機能を使用してデータベースを移行することができます。 ただし、ネイティブの `RESTORE` の場合、データベースが復元される (そして、まだ Azure Blob Storage に格納されていない場合は Azure Blob Storage にコピーされる) まで待つ必要があります。 このため、特に大規模なデータベースでは、アプリケーションに多少のダウンタイムが発生します。 運用データベースを移動するには、[データ移行サービス (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) を使用して、最小限のダウンタイムでデータベースを移行します。 DMS は、ソース データベースで行われた変更を、復元中のマネージド インスタンス データベースに増分的にプッシュすることで、これを実現します。 この方法であれば、最小限のダウンタイムでアプリケーションをソース データベースからターゲット データベースにすばやく切り替えることができます。

## <a name="next-steps"></a>次の手順

- [Managed Instance でサポートされている機能の概要の一覧についてはこちら](sql-database-features.md)、[詳細情報と既知の問題についてはこちら](sql-database-managed-instance-transact-sql-information.md)を確認します。
- [Managed Instance の技術的な特性](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)を学びます。 
- 「[Azure SQL Database でマネージド インスタンスを作成する方法](sql-database-howto-managed-instance.md)」で、より高度な操作方法を参照します。 
