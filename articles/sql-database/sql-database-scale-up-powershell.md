---
title: "PowerShell: Azure SQL Database の価格レベルを変更する | Microsoft Docs"
description: "PowerShell を使用して Azure SQL Database のサービス階層とパフォーマンス レベルを変更し、SQL Database のリソースと価格のスケールアップやスケールダウンを行う方法について説明しています。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 5b1a96149924c6170ea4561b9639f6b85b15ddd3
ms.openlocfilehash: c45d9200dd25d18d45602c377c682875d04c64cb


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>PowerShell で SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)> 
> 

サービス レベルとパフォーマンス レベルは、SQL Database で利用できる機能とリソースを表しており、アプリケーションのニーズの変化に応じて更新できます。 詳細については、「 [サービス レベル](sql-database-service-tiers.md)」をご覧ください。

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。 ごくまれですが、特に、接続が無効になった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。  

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。

* データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
* [geo レプリケーション](sql-database-geo-replication-portal.md) を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
* Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。 「 [Azue SQL Database を障害から回復する](sql-database-disaster-recovery.md) 」に記載されている手順に従って、プライマリ データベースとアクティブなセカンダリ データベース間のレプリケーション プロセスを停止できます。
* サービス階層によって、提供されている復元サービスは異なります。 ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。 詳細については、「 [Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。

**この記事を完了するには、以下が必要です。**

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、このページの上部にある " **無料アカウント** " をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
* Azure SQL Database。 SQL Database がない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って&1; つ作成してください。
* Azure PowerShell。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>SQL Database のサービス階層とパフォーマンス レベルを変更する
[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) コマンドレットを実行し、**-RequestedServiceObjectiveName** を目的の価格レベルのパフォーマンス レベルに設定します (*S0*、*S1*、*S2*、*S3*、*P1*、*P2* など)。

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>SQL Database のサービス階層とパフォーマンス レベルを変更するサンプル PowerShell スクリプト
```{variables}``` は独自の値に置き換えてください (中かっこは不要です)。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>次のステップ
* [スケールアウトとスケールイン](sql-database-elastic-scale-get-started.md)
* [SSMS での SQL Database の接続とクエリ](sql-database-connect-query-ssms.md)
* [Azure SQL Database のエクスポート](sql-database-export-powershell.md)

## <a name="additional-resources"></a>その他のリソース
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](http://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database コマンドレット](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Jan17_HO1-->


