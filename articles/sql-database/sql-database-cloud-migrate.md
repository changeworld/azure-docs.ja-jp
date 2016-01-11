<properties
   pageTitle="SQL Server データベースの Azure SQL Database への移行"
   description="Microsoft Azure SQL Database、データベースのデプロイ、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# SQL Server データベースの Azure SQL Database への移行

この記事では、オンプレミスの SQL Server 2005 またはそれ以降のデータベースを Azure SQL Database に移行する方法について説明します。この移行プロセスでは、現在の環境の SQL Server データベースから SQL Database にスキーマとデータを移行します (ただし、移行元のデータベースが互換性テストをパスする必要があります)。[SQL Database V12](sql-database-v12-whats-new.md) では、サーバーレベルの操作とデータベースをまたいだ操作を除き、互換性の問題はほとんどありません。データベースやアプリケーションが、[部分的にしかサポートされていない機能またはサポートされていない機能](sql-database-transact-sql-information.md)を使用している場合には、SQL Server データベースの移行前にリエンジニアリングを実施して[これらの非互換性を修正する](sql-database-cloud-migrate-fix-compatibility-issues.md)必要があります。

> [AZURE.NOTE]Microsoft Access、Sybase、MySQL Oracle、DB2 などの SQL Server 以外のデータベースを Azure SQL Database に移行する場合は、[SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) チームのブログ記事を参照してください。

## SQL Server データベースから SQL Database への移行の互換性の確認

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

SQL Database の互換性の問題があるかどうかを確認するには、移行プロセスを開始する前に、次のいずれかの方法を使用します。

- [SqlPackage を使用する](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage は、互換性の問題の有無を確認するためのコマンド プロンプト ユーティリティの一種です。互換性の問題が検出された場合には、問題を記載したレポートが生成されます。
- [SQL Server Management Studio を使用する](sql-database-cloud-migrate-determine-compatibility-ssms.md): SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードでは、検出されたエラーが画面に表示されます。

## 互換性の問題の修正

互換性の問題が検出された場合、移行を開始する前にその問題を修正する必要があります。

- [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)を使用する
- [Visual Studio 用の SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) を使用する
- [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) を使用する

## 互換性のある SQL Server データベースの SQL Database への移行

互換性のある SQL Server データベースを移行するために、マイクロソフトでは、さまざまなシナリオに合わせていくつかの移行方法を用意しています。許容できるダウンタイム、お使いの SQL Server データベースの規模と複雑さ、Microsoft Azure クラウドへの接続の状態に応じて、方法を選択できます。

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

移行方法を選択するときに最初に考える必要があるのは、移行中に運用環境からデータベースを取り出すことができるか、という点です。アクティブなトランザクションが発生しているときにデータベースを移行すると、データベースの整合性が損なわれ、データベースが破損する可能性があります。クライアント接続を無効にしたり、[データベース スナップショット](https://msdn.microsoft.com/library/ms175876.aspx)を作成したりするなど、データベースはさまざまな方法で停止できます。

最小限のダウンタイムで移行するには、[SQL Server のトランザクション レプリケーションを使用します](sql-database-cloud-migrate-compatible-using-transactional-replication.md)。この場合、お使いのデータベースがトランザクション レプリケーションの要件を満たしている必要があります。ある程度のダウンタイムが許容される場合、または将来の移行に備えて運用データベースの移行をテストする場合には、次の 3 つの方法のいずれかを検討してください。

- [SSMS 移行ウィザード](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): 小規模から中規模のデータベースの場合、互換性のある SQL Server 2005 またはそれ以降のデータベースの移行は、SQL Server Management Studio の、[データベースの Microsoft Azure Database へのデプロイ ウィザード](sql-database-cloud-migrate-compatible-using-migration-wizard.md)を実行するのと同じくらい簡単です。 
- [BACPAC ファイルへのエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)と [BACPAC ファイルからのインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): 接続に問題があり (接続がない、帯域幅が狭い、またはタイムアウトの問題が存在する)、データベースが中規模から大規模の場合には、[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルを使用します。この方法では、スキーマとデータを BACPAC ファイルにエクスポートしたうえで、その BACPAC ファイルを SQL Database にインポートします。エクスポートとインポートには、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードか、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用します。
- BACPAC と BCP の併用: より大規模なデータベースの場合、複雑にはなりますが、[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルと [BCP](https://msdn.microsoft.com/library/ms162802.aspx) を使用することで、より高度な並列処理を実現し、パフォーマンスを向上させることができます。この方法では、スキーマとデータを別々に移行します。 
 - [スキーマのみを BACPAC ファイルにエクスポートします](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)。
 - [スキーマのみを BACPAC ファイルから SQL Database にインポートします](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)。 
 - [BCP](https://msdn.microsoft.com/library/ms162802.aspx) を使用してデータをフラット ファイルに抽出し、これらのファイルを Azure SQL Database に[並列ロード](https://technet.microsoft.com/library/dd425070.aspx)します。

	 ![SSMS の移行ダイアグラム](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_1223_2015-->