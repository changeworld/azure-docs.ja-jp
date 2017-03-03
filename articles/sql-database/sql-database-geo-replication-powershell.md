---
title: "PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する | Microsoft Docs"
description: "PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: bc5e50e4-bbb2-4ce1-9ee5-9a632de6fa06
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/14/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 85efdb2a5b9571d76338aeb0871b729693b63dcb
ms.lasthandoff: 02/16/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-powershell"></a>PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する

この記事では、PowerShell を使用して SQL Database のアクティブ geo レプリケーションを構成する方法について説明します。

PowerShell を使用してフェールオーバーを開始するには、「 [PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-powershell.md)」を参照してください。

> [!NOTE]
> すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。 2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。
> 
> 

PowerShell を使用してアクティブ geo レプリケーションを構成するには、次のものが必要です。

* Azure サブスクリプション。 
* Azure SQL Database - レプリケートするプライマリ データベースです。
* Azure PowerShell 1.0 以降。 Azure PowerShell モジュールをダウンロードしてインストールするには、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="configure-your-credentials-and-select-your-subscription"></a>資格情報を構成してサブスクリプションを選択
まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。 ログイン画面で、Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。

    Login-AzureRmAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。

### <a name="select-your-azure-subscription"></a>Azure サブスクリプションを選択します。
サブスクリプションを選択するには、サブスクリプション ID が必要です。 前の手順で表示した情報からサブスクリプション ID をコピーできるほか、複数のサブスクリプションがあって複数の詳細情報が必要な場合は **Get-AzureRmSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。 次のコマンドレットでは、サブスクリプション ID を使用して、現在のサブスクリプションを設定します。

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

**Select-AzureRmSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。

## <a name="add-secondary-database"></a>セカンダリ データベースの追加
次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。  

セカンダリ データベースを有効にするには、サブスクリプションの所有者または共同所有者でなければなりません。 

**New-AzureRmSqlDatabaseSecondary** コマンドレットを使用すると、パートナー サーバー上のセカンダリ データベースを、接続先のサーバー上のローカル データベース (プライマリ データベース) に追加することができます。 

このコマンドレットでは、**Start-AzureSqlDatabaseCopy** を **-IsContinuous** パラメーターに置き換えます。  これにより **AzureRmSqlDatabaseSecondary** オブジェクトが出力されます。他のコマンドレットはこのオブジェクトを使用して、特定のレプリケーション リンクを明確に区別することができます。 このコマンドレットは、セカンダリ データベースが作成され、そのシード処理が完全に行われると、戻ります。 データベースのサイズによって所要時間は異なり、数分 ～ 数時間の幅があります。

セカンダリ サーバー上のレプリケート データベースは、プライマリ サーバー上のデータベースと同じ名前となります。既定でのサービス レベルもプライマリ サーバー上のデータベースの場合と同じになります。 セカンダリ データベースは、読み取り可能または読み取り不可とすることができるほか、スタンドアロン データベースとして、またはエラスティック プールに作成することができます。 詳細については、「[New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689\(v=azure.300\).aspx)」と「[サービス レベル](sql-database-service-tiers.md)」をご覧ください。
セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。 次の手順では、PowerShell を使用してこの操作を行い、読み取り不可または読み取り可能なセカンダリ データベースを、スタンドアロン データベースとして作成するか、またはエラスティック プールに作成する方法について説明します。

パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。

### <a name="add-a-non-readable-secondary-standalone-database"></a>読み取り不可のセカンダリ データベース (スタンドアロン データベース) の追加
次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のデータベース "mydb" に対して読み取り不可のセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-standalone-database"></a>読み取り可能なセカンダリ (スタンドアロン データベース) の追加
次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のデータベース "mydb" に対して読み取り可能なセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-pool"></a>読み取り不可のセカンダリ (エラスティック プール) の追加
次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のエラスティック プール "ElasticPool1" 内のデータベース "mydb" に対して読み取り不可のセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-pool"></a>読み取り可能なセカンダリ (エラスティック プール) の追加
次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のエラスティック プール "ElasticPool1" 内のデータベース "mydb" に対して読み取り可能なセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する
セカンダリ データベースとそのプライマリ データベースとの間のレプリケーション パートナーシップを完全に終了させるには、**Remove-AzureRmSqlDatabaseSecondary** コマンドレットを使用します。 リレーションシップの終了後、セカンダリ データベースは読み取り/書き込みデータベースになります。 セカンダリ データベースへの接続が切断された場合、コマンドは成功します。ただし、接続が復元されると、セカンダリ データベースは読み取り/書き込みデータベースになります。 詳細については、「[Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457\(v=azure.300\).aspx)」と「[サービス レベル](sql-database-service-tiers.md)」をご覧ください。

このコマンドレットは、レプリケーション用の Stop-AzureSqlDatabaseCopy に取って代わります。 

この削除は強制終了の場合と同じです。強制終了では、レプリケーション リンクが削除され、前のセカンダリ データベースは、終了前に完全にレプリケートされるとは限らないスタンドアロン データベースとして残されます。 前のプライマリ データベースと前のセカンダリ データベースが利用可能な場合は、その両方ですべてのリンク データがクリーンアップされます。 このコマンドレットは、レプリケーション リンクが削除されると戻ります。 

セカンダリ データベースを削除するユーザーは、RBAC に従ってプライマリ データベースとセカンダリ データベースの両方への書き込みアクセス権を有する必要があります。 詳細については、ロールベースのアクセス制御を参照してください。

次のコードを実行すると、"mydb" という名前のデータベースと、リソース グループ "rg2" に属するサーバー "srv2" とのレプリケーション リンクが削除されます。 

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -SecondaryResourceGroup "rg2" -PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>geo レプリケーションの構成と正常性を監視する
監視タスクには、geo レプリケーションの構成に関する監視と、データ レプリケーションの正常性に関する監視が含まれます。  

sys.geo_replication_links のカタログ ビューに表示される順方向のレプリケーション リンクに関する情報を取得するには、[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330\(v=azure.300\).aspx) を使用します。

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2” 上のプライマリ データベース "mydb" とセカンダリ データベースの間のレプリケーション リンクのステータスを取得します。

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -PartnerResourceGroup "rg2” -PartnerServerName "srv2”


## <a name="next-steps"></a>次のステップ
* アクティブ geo レプリケーションの詳細については、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)


