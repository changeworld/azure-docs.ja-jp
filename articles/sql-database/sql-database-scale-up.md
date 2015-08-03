<properties 
   pageTitle="データベースのサービス階層とパフォーマンス レベルの変更" 
   description="Azure SQL Database のサービス階層を使用して動的にクラウド データベースをスケール アップまたはスケール ダウンする方法について説明します。これにより、アプリケーションにダウンタイムを発生させることなくビジネス要件とコスト要件に基づいてパフォーマンス レベルを変更できます。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# データベースのサービス階層とパフォーマンス レベルの変更 

このトピックでは、Azure SQL データベースのサービス階層とパフォーマンス レベル間の移行に関する手順について説明します。

## サービス階層の変更 

[SQL Database の Web/Business データベースの新しいサービス階層へのアップグレード](sql-database-upgrade-new-service-tiers.md)に関するトピックと「[Azure SQL Database のサービス階層とパフォーマンス レベル](https://msdn.microsoft.com/library/azure/dn741336.aspx)」の情報を使用して、Azure SQL Database に適したサービス階層とパフォーマンス レベルを決定します。

Azure の管理ポータル、[PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx)、または [REST API](https://msdn.microsoft.com/library/dn505719.aspx) を使用すると、サービス階層間を簡単に移行できます。

サービス階層間を移行するときは、次の点に注意してください。
- 別のサービス階層またはパフォーマンス レベルにアップグレードする前に、サーバー上のクォータが十分であることを確認してください。追加のクォータが必要な場合は、カスタマー サポートにお問い合わせください。
- 統合データベースは、Basic、Standard、または Premium サービス階層にアップグレードできません。

- データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。各サービス階層に対して許可されているサイズの詳細については、後に示すサービス階層とデータベース サイズの表を参照してください。

- [標準の geo レプリケーション](https://msdn.microsoft.com/library/azure/dn758204.aspx)または[アクティブ geo レプリケーション](https://msdn.microsoft.com/library/azure/dn741339.aspx)を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス階層にアップグレードしてから、プライマリ データベースをアップグレードする必要があります。

- Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。「[連続コピーの関係の終了](https://msdn.microsoft.com/library/azure/dn741323.aspx)」に説明されている手順に従って、プライマリ データベースとアクティブ セカンダリ データベース間のレプリケーション プロセスを停止してください。

- サービス階層によって、提供されている復元サービスは異なります。ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。詳細については、「[Azure SQL Database のバックアップと復元](https://msdn.microsoft.com/library/azure/jj650016.aspx)」を参照してください。

- 24 時間の期間内に 4 つまでの個別のデータベースの変更 (サービス階層またはパフォーマンス レベル) を行うことができます。

- データベースに対する新しいプロパティは、変更が完了するまで適用されません。

次の点に注意してください。
- Business および Web のサービス階層は、2015 年 9 月に提供終了となります。詳細については、「[Web および Business エディションの終了に関する FAQ](https://msdn.microsoft.com/library/azure/dn741330.aspx)」を参照してください。

<note included> 
- [フェデレーションの現在の実装は Web および Business のサービス階層と共に終了](https://msdn.microsoft.com/library/azure/dn741330.aspx)します。[Azure SQL Database 用の Elastic Scale](sql-database-elastic-scale-get-started.md) を使用して、Azure SQL Database にシャーディングされたスケール アウト ソリューションを構築することを検討してください。試すには、Azure SQL Database Elastic Scale プレビューの使用に関するトピックを参照してください。

## 上位サービス階層へのアップグレード
データベースをアップグレードするには、次のどちらかの方法を使用します。この手順は Premium サービス階層へのアップグレード方法ですが、すべてのアップグレードに利用できます。

### Azure の管理ポータルの使用
1. Microsoft アカウントを使用して、Azure の管理ポータルにサインインします。
2. **[SQL データベース]** タブに移動します。
3. **[データベース]** ボックスの一覧でデータベースを選択します。これにより、**[データベース ダッシュボード]** または **[クイック スタート]** ページに、そのデータベースが表示されます。
4. データベースの **[スケール]** タブを選択します。
5. **[全般]** セクションで、サービス階層として **[Premium]** を選択します。
6. **[パフォーマンス レベル]** で、**[P1]**、**[P2]**、または **[P3]** を選択します。各パフォーマンス レベルを提供するリソースは、DTU で表されています。パフォーマンス レベルと DTU の詳細については、Azure SQL Database のサービス階層とパフォーマンス レベルに関するトピックを参照してください。
8. 画面下部のコマンド バーにある **[保存]** ボタンをクリックします。
9. **[確認]** ページが表示されます。表示された情報を確認し、チェック ボックスをオンにして確定します。


### Azure PowerShell の使用
1. Set-AzureSqlDatabase を使用して、パフォーマンス レベル、最大データベース サイズ、データベースのサービス階層を指定します。各サービス階層でサポートされているデータベース サイズの一覧については、Azure SQL Database のサービス階層 (エディション) に関するトピックを参照してください。
2. New-AzureSqlDatabaseServerContext コマンドレットを使用して、サーバー コンテキストを設定します。サンプル構文は、「Azure PowerShell コマンドの使用」セクションに記載されています。
3. データベースおよび対象のパフォーマンス レベルのハンドルを取得します。Set-AzureSqlDatabase -ServiceObjective を使用して、パフォーマンス レベルを指定します。

**使用例** 次の点に注意してください。
- ここでは、Premium サービス階層へのアップグレードについて説明します。
- データベース名 "somedb" を示す $db ハンドルが作成されます。
- Premium パフォーマンス レベル 1 を示す $P1 ハンドルが作成されます。
- データベース $db のパフォーマンス レベルは $P1 に設定されます。

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## 下位サービス階層へのダウングレード
データベースを下位サービス階層にダウングレードするには、次のどちらかの方法を使用します。

### Azure の管理ポータルの使用
1. Microsoft アカウントを使用して、Azure の管理ポータルにサインインします。
2. **[SQL データベース]** タブに移動します。
3. 目的のデータベースの **[スケール]** タブを選択します。
4. **[全般]** セクションで、ダウングレード先のサービス階層を選択します。
5. 画面下部のコマンド バーにある **[保存]** ボタンをクリックします。
6. 該当する場合は、**[確認]** ページに表示された情報を確認し、チェック ボックスをオンにして変更を確定します。

### Azure PowerShell の使用
1. Set-AzureSqlDatabase を使用して、データベースのサービス階層、パフォーマンス レベル、最大サイズを指定します。
2. 「Azure PowerShell コマンドの使用」セクションのサンプル構文に従って、New-AzureSqlDatabaseServerContext を使用してサーバー コンテキストを設定します。
3. 以下の手順を実行します。
 - データベースのハンドルを取得します。
 - パフォーマンス レベルのハンドルを取得します。
 - Set-AzureSqlDatabase -ServiceObjective を使用して、データベースのサービス階層、パフォーマンス レベル、最大サイズを指定します。

**使用例**

この例では、Premium サービス階層のデータベースを Standard サービス階層のデータベースにダウングレードする方法を示します。

- データベース名 "somedb" を示す $db ハンドルが作成されます。

- Standard パフォーマンス レベル S2 を示す変数 $S2 が作成されます。

- データベース $db のパフォーマンス レベルは $S2 に設定されます。

- – Edition パラメーターと – MaxSizeGB パラメーターを使用して、データベースのサービス階層と最大サイズを指定します。– MaxSizeGB パラメーターに指定する値は、移行後のサービス階層で有効な値である必要があります。前に示した各サービス階層の MaxSize 値の表を参照してください。

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##パフォーマンス レベルの変更
次のどちらかの方法を使用して、Standard データベースまたは Premium データベースのパフォーマンス レベルを上位または下位のレベルに変更できます。データベースのパフォーマンス レベルの変更には、時間がかかる場合があります。詳細については、[Premium データベースの変更の影響](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact)に関するセクションを参照してください。

アクティブ geo レプリケーション リレーションシップが構成されている Premium データベースのパフォーマンス レベルを変更する場合、プライマリ データベースとアクティブ セカンダリ データベースについて次の順序に従ってください。

これは、アクティブ セカンダリ データベースのパフォーマンス レベルがプライマリ データベース以上である必要があるためです。
- 上位のパフォーマンス レベルを下位のパフォーマンス レベルに変更する場合、まずプライマリ データベースから始めて、その後に 1 つまたは複数のアクティブ セカンダリ データベースを処理してください。

- 下位のパフォーマンス レベルを上位のパフォーマンス レベルに変更する場合は、アクティブ セカンダリ データベースから始めて、最後にプライマリ データベースを処理してください。

###Azure の管理ポータルの使用
1. Microsoft アカウントを使用して、Azure の管理ポータルにサインインします。
2. **[SQL データベース]** タブに移動します。
3. **[データベース]** ボックスの一覧で、アカウントまたは特定のサーバーのデータベースを選択します。これにより、**[データベース ダッシュボード]** または **[クイック スタート]** ページに、そのデータベースが表示されます。
5. データベースの **[スケール]** タブを選択します。
6. **[パフォーマンス レベル]** オプションで、パフォーマンス レベルを選択します。
7. 画面下部のコマンド バーにある **[保存]** ボタンをクリックします。

###Azure PowerShell の使用
1. Set-AzureSqlDatabase を使用して、データベースのパフォーマンス レベルを指定します。
2. New-AzureSqlDatabaseServerContext コマンドレットを使用して、サーバー コンテキストを設定します。サンプル構文は、「Azure PowerShell コマンドの使用」セクションに記載されています。
3. 以下の手順を実行します。
- データベースのハンドルを取得します。
- パフォーマンス レベルのハンドルを取得します。
- Set-AzureSqlDatabase -ServiceObjective を使用して、パフォーマンス レベルを指定します。

**使用例**

次の点に注意してください。

- データベース名 "somedb" を示す $db ハンドルが作成されます。

- Premium パフォーマンス レベル 2 を示す $P2 ハンドルが作成されます。

- データベース $db のパフォーマンス レベルは $P2 に設定されます。

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##データベースの変更の影響
ここでは、Standard または Premium サービス階層にアップグレードする場合、またはデータベースのパフォーマンス レベルを変更する場合の影響について説明します。

###データベースを変更するときのアプリケーション接続の処理
パフォーマンス レベルを変更したとき、またはアップグレードまたはダウングレードが完了したときにデータベースへの接続が一時的に切断され、接続が再確立されるまでに数秒間かかる場合があります。SQL Database アプリケーションは、接続が切断されても回復できるように設計しておく必要があります。なぜなら、この問題は、データ センターでコンピューターが停止し、SQL Database サービスによってデータベースがフェールオーバーされるときに SQL Database で常に発生する可能性があるためです。Premium データベースを使用するために、または Premium データベースのパフォーマンス レベルを変更するために、アプリケーションの実装を変更する必要はありません。

###データベースの変更の待機時間の把握と推測
多くの場合、データベースの SLO を変更するにはデータを移動する必要があるため、変更要求が完了して関連付けられている料金変更が適用されるまで、数時間かかる可能性があります。データの移行は、データベースのアップグレード/ダウングレード時に変更があった場合や、データベースのパフォーマンス レベルを変更するときに発生します。

**データ移動を伴う SLO 変更の待機時間**

データベースのストレージ サイズを決定した後で、次のヒューリスティックを使用して SLO の変更要求の待機時間を推定することができます。

3 (5 分 + データベース サイズ / 150 MB/分)

たとえば、データベースのサイズが 50 GB の場合、SLO の変更要求の待機時間は次のヒューリスティックによって推定できます。

3 x (5 分 + 50 GB x 1,024 MB/GB / 150 MB/分) ≈17 時間

このヒューリスティックを使用した推定待機時間は、最小値が空のデータベースの場合の 15 分、最大値が 150 GB のデータベースの場合の約 2 日間となります。データ センターの状況によっては、推定待機時間の範囲はさらに大きくなります。

**上位のパフォーマンス レベルから下位のパフォーマンス レベルに変更する場合の待機時間**

一般的に、データベースのパフォーマンス レベルを上位レベルから小さいサイズに変更する場合、データの移行はありません。このような場合、SLO 変更の待機時間は非常に短く、一般的には数秒程度になります。

警告: 上の説明は、Premium サービス階層と Standard サービス階層の間のダウングレードに対してのみ適用されます。Web、Business、または Basic のサービス階層へのダウングレードには、データの移動が伴います。

###データベースの変更の状態の確認
サービス階層間のアップグレードまたはダウングレードの間、または次のどちらかの方法でパフォーマンス レベルを変更するときに、データベースの状態を確認できます。

####Azure の管理ポータルの使用
1. Microsoft アカウントを使用して、Azure の管理ポータルにサインインします。
2. **[データベース]** ボックスの一覧でデータベースを選択します。これにより、**[データベース ダッシュボード]** または **[クイック スタート]** ページに、そのデータベースが表示されます。
3. **[データベース ダッシュボード]** の **[概要]** にある **[エディション]** セクションで、状態情報を確認できます。
4. サービス レベル目標 (SLO) は、サービス階層内のパフォーマンス レベルを示します。


##Azure PowerShell コマンドの使用
ここでは、Azure PowerShell コマンドを使用するための前提条件について説明します。

**前提条件**

このトピックで説明した Azure PowerShell コマンドレットを使用するには、PowerShell を実行するコンピューターに次のソフトウェアをインストールする必要があります。
1. http://www.microsoft.com/ja-jp/download/details.aspx?id=34595 で Windows PowerShell 3.0 以降をダウンロードします。

2. [Azure SDK とツールのダウンロード](http://azure.microsoft.com/downloads/) ページのコマンドライン ツールのセクションから Azure PowerShell をダウンロードします。

次の手順を実行します。
**[スタート]** 画面または **[スタート]** メニューから、Azure PowerShell に移動して開始します。

サーバーのユーザー名とパスワードを入力します。

**New-AzureSqlDatabaseServerContext** を使用して、サーバー コンテキストを設定します。

**例**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Azure PowerShell の参照情報**
このトピックで使用されている Azure PowerShell コマンドレットの詳細については、[Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/dn546726.aspx)に関するページを参照してください。

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=July15_HO4-->