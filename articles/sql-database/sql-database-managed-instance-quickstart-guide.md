---
title: クイック スタート - Azure SQL Database Managed Instance | Microsoft Docs
description: Azure SQL Database - Managed Instance の基本的な使用方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468174"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の概要

[Azure SQL Database Managed Instance](sql-database-managed-instance-index.yml) は、Azure クラウド内でホストされ、プライベート IP アドレスを使用してお客様の VNet 内に配置されている、フル マネージド PaaS バージョンの SQL Server です。 このセクションでは、Managed Instance の構成と作成、データベースの移行を簡単に行う方法について説明します。

## <a name="quickstart-overview"></a>クイック スタートの概要

このセクションでは、マネージド インスタンスの基本的な使用方法を理解できる記事の概要を紹介します。 初めてマネージド インスタンスを作成する場合、最も簡単な方法は [Azure portal](sql-database-managed-instance-get-started.md) を使用することです。Azure portal では必要なパラメーター (ユーザー名/パスワード、コア数、最大ストレージ) を構成できます。また、Azure ネットワーク環境を自動的に作成できます。ネットワークの詳細やインフラストラクチャの要件を把握している必要はありません。 インスタンスを作成できる[サブスクリプション種類](sql-database-managed-instance-resource-limits.md#supported-subscription-types)の確認のみが必要です。

ネットワークを所有していてそれを使用したい場合、またはネットワークをカスタマイズしたい場合は、Managed Instance 用に[ネットワーク環境を構成する方法](#configure-network-environment)を参照してください。

マネージド インスタンスを作成するときは、次のいずれかの方法でインスタンスに接続する必要があります。

* SQL Server Management Studio と、マネージド インスタンスの配置先と同じ VNet 内のサブネットにあるマネージド インスタンスへのアクセスに使用できるその他のアプリがインストールされた [Azure 仮想マシン](sql-database-managed-instance-configure-vm.md)を作成します。 VM をマネージド インスタンスと同じサブネットに配置することはできません。
* お客様のコンピューター上で[ポイント対サイト接続](sql-database-managed-instance-configure-p2s.md)を設定します。これにより、マネージド インスタンスが配置されている VNet にコンピューターを参加させ、ネットワーク内のその他の任意の SQL Server のようにマネージド インスタンスを使用できるようになります。

別の方法として、ローカル ネットワークから高速ルートまたはサイト間接続を使用することもできますが、このような方法はこれらのクイック スタートでは扱いません。

マネージド インスタンスを作成してアクセスを構成すると、オンプレミスまたは Azure VM の SQL Server に配置されているデータベースの移行を開始できます。 移行するソース データベースにサポートされていない機能がある場合、移行は失敗する点に注意してください。 失敗を回避し、互換性を確認するには、[Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) をインストールします。これにより、SQL Server 上のデータベースが分析され、Managed Instance への移行を妨げる可能性がある問題 (FileStream や複数のログ ファイルの存在など) が検索されます。 このような問題を解決すると、データベースは Managed Instance に移行できる状態になります。 もう 1 つの便利なツールに [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) があります。このツールを使用すると、SQL Server 上のワークロードを記録し、Managed Instance 上で再生することができます。そのため、Managed Instance に移行した場合にパフォーマンスの問題が発生するかどうかを判断できます。

データベースを Managed Instance に移行できると判断したら、[ネイティブの復元](sql-database-managed-instance-get-started-restore.md)機能を使用できます。この機能で、Transact-SQL コマンドを使用してデータベースのバックアップを作成し、それを Azure Blob ストレージにアップロードし、Transact-SQL コマンドを使用して BLOB ストレージから復元データベースを作成することができます。

このようなクイック スタートを使用すると、マネージド インスタンス上のデータベースの構成、作成、および配置を簡単に行うことができます。 シナリオによっては、Managed Instance と必要なネットワーク環境のデプロイをカスタマイズまたは自動化する必要があります。 ここでは、これらのシナリオについて説明します。

## <a name="customizing-network-environment"></a>ネットワーク環境のカスタマイズ

[Azure portal](sql-database-managed-instance-get-started.md) を使用してインスタンスを作成するときに VNet/サブネットを自動的に構成できますが、VNet およびサブネットのパラメーターを構成できるので、マネージド インスタンスの作成を開始する前に作成することをお勧めします。 ネットワーク環境を作成および構成する場合、最も簡単な方法は、[Azure リソース デプロイ](sql-database-managed-instance-create-vnet-subnet.md) テンプレートを使用することです。このテンプレートで、インスタンスが配置されるネットワークとサブネットを作成および構成します。 必要な操作は、Azure Resource Manager のデプロイ ボタンを押し、フォームにパラメーターを入力することのみです。 別の方法として、[PowerShell スクリプト](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)を使用してネットワークの作成を自動化することもできます。

マネージド インスタンスをデプロイする VNet とサブネットを既にお持ちの場合は、その VNet とサブネットが[ネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)を満たしていることを確認する必要があります。 [この PowerShell スクリプトを使用して、サブネットが正しく構成されていることを確認する](sql-database-managed-instance-configure-vnet-subnet.md)ことをお勧めします。 このスクリプトでは、ネットワークが検証され、問題が報告されるだけでなく、変更が必要な点が示されます。また、VNet/サブネットに必要な変更を加えられるように提示されます。 VNet/サブネットを手動で構成したくない場合は、このスクリプトを実行します。また、ネットワーク インフラストラクチャを大幅に再構成した後にも実行するようにします。 お客様のネットワークを作成して構成する場合は、[Managed Instance のドキュメント](sql-database-managed-instance-connectivity-architecture.md)と[こちらのガイド](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)を参照してください。

## <a name="automating-creation-of-managed-instance"></a>Managed Instance の自動作成

 前の手順で説明したように、ネットワーク環境を作成していない場合は、Azure portal で自動的に作成できます。唯一の欠点は、後で変更できない既定のパラメーターをいくつか使用して構成されることです。 別の方法として、[PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)、[PowerShell と Resource Manager テンプレート](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)、または [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/) を使用できます。

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>最小限のダウンタイムで Managed Instance に移行する

このようなクイック スタートの記事を使用すると、Managed Instance を簡単に設定し、データベースを移行することができます。 ただし、ネイティブの復元では、データベースが復元されるまで待つ必要があります。そのため、特にデータベースが大きい場合は、アプリケーションのダウンタイムがある程度発生します。 運用データベースを移行する場合は、最小限のダウンタイムが保証される優れた移行方法が必要になるでしょう。 [データ移行サービス](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)は、ソース データベースで行った変更を、マネージド インスタンスに復元しているデータベースに段階的にプッシュすることで、最小限のダウンタイムでデータベースを移行できる移行サービスです。 この方法であれば、最小限のダウンタイムでアプリケーションをソース データベースからターゲット データベースにすばやく切り替えることができます。

## <a name="next-steps"></a>次の手順

* [Managed Instance でサポートされている機能の概要の一覧についてはこちら](sql-database-features.md)、[詳細情報と既知の問題についてはこちら](sql-database-managed-instance-transact-sql-information.md)を確認します。 
* [Managed Instance の技術的な特性](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)を学びます。 
* [ハウ ツー セクション](sql-database-howto-managed-instance.md)でさらに高度なチュートリアルを確認します。 
