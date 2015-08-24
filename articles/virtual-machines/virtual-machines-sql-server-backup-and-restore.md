<properties 
	pageTitle="Azure の仮想マシンにおける SQL Server のバックアップと復元"
	description="Azure 仮想マシンで実行されている SQL Server データベースのバックアップと復元に関する考慮事項について説明します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />

<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/05/2015"
	ms.author="jroth" />

# Azure の仮想マシンにおける SQL Server のバックアップと復元

## 概要

SQL Server データベースのデータのバックアップは、アプリケーション エラーやユーザー エラーによるデータ損失から保護する戦略の重要な要素です。これは、Azure 仮想マシン (VM) で実行されている SQL Server にも同様に当てはまります。

Azure VM で実行されている SQL Server では、バックアップ ファイルの保存先として接続されたディスクを使用するネイティブなバックアップおよび復元手法を使用できます。ただし、[仮想マシンのサイズ](virtual-machines-size-specs.md)に基づいて、Azure 仮想マシンに接続できるディスク数には制限があります。また、ディスク管理のオーバーヘッドも考慮する必要があります。

SQL Server 2014 以降では、Microsoft Azure BLOB ストレージへのバックアップと復元が可能です。SQL Server 2016 には、このオプションの拡張機能も用意されています。また、データベース ファイルが Microsoft Azure BLOB ストレージに保存されている場合、SQL Server 2016 では、Azure のスナップショットを使用して、ほぼ瞬時にバックアップを作成し、迅速に復元できます。この記事では、これらのオプションの概要を説明します。追加情報については、「SQL Server Backup and Restore with Microsoft Azure Blob Storage Service (Microsoft Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元)」(https://msdn.microsoft.com/library/jj919148(v=sql.130).aspx)) をご覧ください。

>[AZURE.NOTE]大規模なデータベースのバックアップのオプションについては、「[Multi-Terabyte SQL Server Database Backup Strategies for Azure Virtual Machines (Azure 仮想マシンの数 TB の SQL Server データベースのバックアップ戦略)](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)」をご覧ください。

以降のセクションには、Azure 仮想マシンでサポートされているさまざまなバージョンの SQL Server に固有の情報が記載されています。

## データベース ファイルが Microsoft Azure BLOB サービスに保存されている場合のバックアップに関する考慮事項

データベース ファイルが Microsoft Azure BLOB ストレージに保存されている場合、データベースのバックアップを実行する理由や、基になるバックアップ テクノロジ自体が変わります。データベース ファイルを Azure BLOB ストレージに保存する方法の詳細については、[Azure における SQL Server データ ファイル](https://msdn.microsoft.com/library/jj919148.aspx)に関するページをご覧ください。

- Microsoft Azure では、Microsoft Azure サービスの一部として、ハードウェアやメディアの障害からの保護が提供されるため、この保護のためにデータベースのバックアップを実行する必要はなくなりました。

- ユーザー エラーからの保護、保存目的、規制上の理由、または管理目的で、データベースのバックアップを引き続き実行する必要があります。

- Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2) の SQL Server ファイル スナップショット バックアップ機能を使用して、ほぼ瞬時のバックアップと迅速な復元を実行できます。詳細については、「[Snapshot Backups for Database Files in Azure (Azure でのデータベース ファイルのスナップショット バックアップ)](https://msdn.microsoft.com/library/mt169363.aspx)」をご覧ください。

## Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2) でのバックアップと復元

Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2) では、SQL Server 2014 の [Azure BLOB を使用したバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx)機能をサポートしています。ただし、次の拡張機能も用意されています。

- **ストライピング**: Microsoft Azure BLOB ストレージにバックアップする場合、SQL Server 2016 では、複数の BLOB へのバックアップをサポートしているので、最大 12.8 TB の大規模なデータベースをバックアップできます。

- **スナップショット バックアップ**: Azure のスナップショットを使用することで、SQL Server ファイル スナップショット バックアップでは、Azure BLOB ストレージ サービスを使用して保存されたデータベース ファイルをほぼ瞬時にバックアップし、迅速に復元できます。この機能により、バックアップと復元のポリシーを簡素化することができます。ファイル スナップショット バックアップでは、ポイントインタイム リストアもサポートしています。詳細については、「[Snapshot Backups for Database Files in Azure (Azure でのデータベース ファイルのスナップショット バックアップ)](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)」をご覧ください。

- **マネージ バックアップのスケジュール設定**: Azure への SQL Server マネージ バックアップでカスタム スケジュールがサポートされるようになりました。詳細については、「[SQL Server Managed Backup to Microsoft Azure (Microsoft Azure への SQL Server マネージ バックアップ)](https://msdn.microsoft.com/library/dn449496.aspx)」をご覧ください。

>[AZURE.NOTE]Azure BLOB ストレージを使用する場合の、SQL Server 2016 の機能のチュートリアルについては、「[Tutorial: Using the Microsoft Azure Blob storage service with SQL Server 2016 databases (チュートリアル: SQL Server 2016 データベースでの Microsoft Azure BLOB ストレージ サービスの使用)](https://msdn.microsoft.com/library/dn466438.aspx)」をご覧ください。

## SQL Server 2014 でのバックアップと復元

SQL Server 2014 には、次の拡張機能が用意されています。

1. **Azure へのバックアップと復元**:

 - SQL Server Management Studio で、*SQL Server Backup to URL* がサポートされるようになりました。SQL Server Management Studio でバックアップ タスクまたは復元タスクを使用するときや、メンテナンス プラン ウィザードを使用するときに、Azure にバックアップするオプションを利用できるようになりました。詳細については、「[SQL Server Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)」をご覧ください。
 - *Azure への SQL Server マネージ バックアップ*は、バックアップ管理の自動化を可能にする新機能です。この機能は、Azure マシンで実行されている SQL Server 2014 インスタンスのバックアップ管理を自動化する際に特に役立ちます。詳細については、「[SQL Server Managed Backup to Microsoft Azure (Microsoft Azure への SQL Server マネージ バックアップ)](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)」をご覧ください。
 - *自動アックアップ*は、Azure の SQL Server VM の既存のデータベースと新しいデータベースのすべてを対象とした *Azure への SQL Server マネージ バックアップ*を自動的に有効にする別の自動化機能です。詳細については、「[Azure 仮想マシンでの SQL Server の自動バックアップ](virtual-machines-sql-server-automated-backup.md)」をご覧ください。
 - Azure に SQL Server 2014 をバックアップする際のすべてのオプションの概要については、「[SQL Server Backup and Restore with Microsoft Azure Blob Storage Service (Microsoft Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元)](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)」をご覧ください。

1. **暗号化**: SQL Server 2014 では、バックアップの作成時におけるデータの暗号化をサポートしています。複数の暗号化アルゴリズムと、証明書または非対称キーの使用をサポートしています。詳細については、「[バックアップの暗号化](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx)」をご覧ください。

## SQL Server 2012 でのバックアップと復元

SQL Server 2012 での SQL Server のバックアップと復元の詳細については、「[SQL Server データベースのバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)」をご覧ください。

SQL Server 2012 SP1 Cumulative Update 2 以降では、Azure BLOB ストレージ サービスとの間でバックアップと復元を実行できます。この拡張機能を使用して、Azure 仮想マシンで実行されている SQL Server 上またはオンプレミスのインスタンス上の SQL Server データベースをバックアップできます。詳細については、「[Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)」をご覧ください。

Azure BLOB ストレージ サービスを使用する利点として、接続されるディスク数の上限 (16 個) を回避できること、管理しやすいこと、移行または災害復旧のために、Azure 仮想マシンで実行されている SQL Server インスタンスの別のインスタンスまたはオンプレミスのインスタンスでバックアップ ファイルを直接利用できることなどがあります。SQL Server バックアップに Azure BLOB ストレージ サービスを使用する利点の詳細については、「[Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)」の*利点*に関するセクションをご覧ください。

ベスト プラクティスの推奨事項とトラブルシューティング情報については、「[バックアップと復元に関するベスト プラクティス (Azure BLOB ストレージ サービス)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)」をご覧ください。

## Azure 仮想マシンでサポートされる他のバージョンの SQL Server でのバックアップと復元

SQL Server 2008 R2 での SQL Server のバックアップと復元については、「[SQL Server でのデータベースのバックアップおよび復元](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)」をご覧ください。

SQL Server 2008 での SQL Server のバックアップと復元については、「[SQL Server でのデータベースのバックアップおよび復元](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)」をご覧ください。

## 次のステップ

Azure VM に SQL Server をデプロイすることを計画している場合、プロビジョニングのガイダンスについては、チュートリアル「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」をご覧ください。

バックアップと復元を使用してデータを移行できますが、Azure VM の SQL Server へのより簡単なデータ移行パスが存在する可能性があります。移行オプションと推奨事項の詳細については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database.md)」をご覧ください。

[Azure 仮想マシンで SQL Server を実行するための他のリソース](virtual-machines-sql-server-infrastructure-services.md)を確認します。

<!---HONumber=August15_HO7-->