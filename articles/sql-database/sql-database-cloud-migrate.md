<properties
   pageTitle="Azure SQL Database へのデータベースの移行"
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
	ms.date="09/02/2015"
	ms.author="carlrab"/>

# Azure SQL Database へのデータベースの移行

Azure SQL Database V12 は、SQL Server 2014 以降とのほぼ完全なエンジン互換性を提供します。そのため、SQL Server 2005 以降のオンプレミスのインスタンスから Azure SQL Database にデータベースを移行するタスクが大幅に簡略化されます。大量のデータベースの移行は、スキーマとデータの簡単な移動操作であり、スキーマの変更がほとんど不要で、アプリケーションの再構築もほとんど、もしくはまったく必要としません。データベースの変更が必要な場合も、変更の範囲は限定的です。

仕様では、Azure SQL Database V12 は、SQL Server のサーバー スコープの機能をサポートしていません。これらの機能に依存するデータベースとアプリケーションは、移行前に再構築する必要があります。Azure SQL Database V12 とオンプレミスの SQL Server データベースとの互換性は向上していますが、特に大規模で複雑なデータベースの移行では、慎重に計画し、実行する必要があります。

## 互換性を判断する
オンプレミスの SQL Server データベースが Azure SQL Database V12 と互換性があるかどうかを判断するには、以下のオプション 1 で説明する 2 つの方法のいずれかを使用して移行を開始し、スキーマの検証ルーチンが非互換性を検出するかを確認するか、以下のオプション 2 で説明するように Visual Studio の SQL Server Data Tools を使用して互換性を検証できます。オンプレミスの SQL Server データベースに互換性の問題がある場合は、Visual Studio の SQL Server Data Tools または SQL Server Management Studio を使用して互換性の問題に対応し、解決できます。

## 移行方法
互換性のあるオンプレミスの SQL Server データベースを Azure SQL Database V12 に移行する方法は多数あります。

- 小規模から中規模のデータベースの場合、接続に問題点 (接続なし、低帯域幅、タイムアウト) がなければ、互換性のある SQL Server 2005 以降のデータベースの移行は、SQL Server Management Studio の [データベースの Microsoft Azure Database へのデプロイ] ウィザードを実行するのと同じくらい簡単です。
- 中規模から大規模のデータベースの場合や接続に問題点がある場合は、SQL Server Management Studio を使用して、データとスキーマを BACPAC ファイル (ローカルまたは Azure BLOB に保存される) にエクスポートしてから、Azure SQL インスタンスに BACPAC ファイルをインポートできます。Azure BLOB に BACPAC を保存する場合は、Azure ポータルから BACPAC ファイルをインポートすることもできます。BACPAC ファイルの詳細については、「[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)」を参照してください。
- 大規模なデータベースの場合は、スキーマとデータを個別に移行することでパフォーマンスを最適化できます。SQL Server Management Studio または Visual Studio を使用してスキーマをデータベース プロジェクトに抽出し、スキーマをデプロイして Azure SQL Database を作成できます。BCP を使用してデータを抽出したら、BCP を使用して、並列ストリームを使用したデータを Azure SQL Database にインポートできます。大規模で複雑なデータベースの移行には、どの方法でも数時間かかる場合があります。

### 方法 1
******SQL Server Management Studio を使用した互換性のあるデータベースの移行***

SQL Server Management Studio では、互換性のあるオンプレミスの SQL Server データベースを Azure SQL Database に移行する 2 つの方法を提供しています。[データベースの Microsoft Azure SQL Database へのデプロイ] ウィザードを使用するか、BACPAC ファイルにデータベースをエクスポートでき、その後それをインポートして新しい Azure SQL Database を作成できます。ウィザードは、Azure SQL Database V12 の互換性を検証し、スキーマとデータを BACPAC ファイルに抽出し、これを指定された Azure SQL Database インスタンスにインポートします。このオプションを使用するには、[SSMS の使用](sql-database-migrate-ssms.md)に関するページを参照してください。

### 方法 2
***Visual Studio を使用してオフラインでデータベース スキーマを更新し、SQL Server Management Studio でデプロイする***

オンプレミスの SQL Server データベースに互換性がない場合や、互換性があるかどうかを判断する場合は、分析のために Visual Studio データベース プロジェクトにデータベース スキーマをインポートできます。分析するには、SQL Database V12 にプロジェクトのターゲット プラットフォームを指定し、プロジェクトを構築します。構築に成功した場合は、データベースには互換性があります。構築に失敗した場合は、Visual Studio 用の SQL Server Data Tools ("SSDT") でエラーを解決できます。プロジェクトが正常に構築されたら、これをソース データベースのコピーとして発行し、SSDT のデータ比較機能を使用して、ソース データベースから Azure SQL V12 互換のデータベースにデータをコピーできます。この更新されたデータベースは、方法 1 を使用して、Azure SQL Database にデプロイされます。スキーマのみの移行が必要な場合は、Visual Studio から Azure SQL Database に直接、発行することができます。この方法は移行ウィザードのみで処理できる変更よりも多数の変更を、データベース スキーマで行う必要がある場合に使用します。このオプションを使用するには、[Visual Studio の使用](sql-database-migrate-visualstudio-ssdt.md)に関するページを参照してください。

## 使用する方法を決定する
- 変更することなくデータベースを移行することができると予想される場合は、迅速かつ簡単な方法 1 を使用してください。不明な場合は、方法 1 の説明に従って、データベースからスキーマのみの BACPAC をエクスポートして開始します。エラーなしで、エクスポートが成功した場合は、方法 1 を使用してそのデータとデータベースを移行することができます。  
- 方法 1 のエクスポート中にエラーが発生した場合は、方法 2 を使用し、移行ウィザードと手動で適用したスキーマの変更の組み合わせを使用して Visual Studio のデータベース スキーマをオフラインで修正します。ソース データベースのコピーはその場で更新され、方法 1 を使用して Azure に移行されます。

## 移行ツール
使用されるツールには、SQL Server Management Studio (SSMS)、Visual Studio (VS、SSDT) の SQL Server ツール、Azure ポータルが含まれます。

> 以前のバージョンは、Azure SQL Database V12 と互換性がないため、クライアント ツールの最新バージョンをインストールしてください。

### SQL Server Management Studio (SSMS)
SSMS を使用して、互換性のあるデータベースを Azure SQL Database に直接デプロイすることもできますし、BACPAC としてデータベースの論理バックアップをエクスポートし、その後、SSMS を使用してインポートし、新しい Azure SQLDatabase を作成することもできます。

[最新バージョンの SSMS をダウンロードする](https://msdn.microsoft.com/library/mt238290.aspx)

### Visual Studio の SQL Server ツール (VS、SSDT)
Visual Studio の SQL Server ツールを使用して、スキーマ内の各オブジェクトの Transact-SQL ファイルのセットを構成するデータベース プロジェクトを作成し、管理することができます。プロジェクトは、データベースかスクリプト ファイルからインポートできます。プロジェクトが作成されると、プロジェクトを構築し、スキーマの互換性を検証する Azure SQL Database v12 に移行します。エラーをクリックすると、対応する Tranact-SQL ファイルが開き、編集とエラーの修正が可能になります。すべてのエラーが修正されると、プロジェクトを SQL Database に直接発行して空のデータベースを作成するか、元の SQL Server Database (のコピー) に発行してスキーマを更新することができます。これによって、上記のように SSMS を使用してデータとデータベースをデプロイできます。

[最新の Visual Studio 用 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) は Visual Studio 2013 Update 4 以降で使用してください。

## 比較
| 方法 1 | 方法 2 |
| ------------ | ------------ | ------------ |
| Azure SQL Database に互換性のあるデータベースをデプロイする | データベースのインプレース更新と Azure SQL Database へのデプロイ |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![オフライン編集](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| SSMS の使用 | VS と SSMS の使用 |
|単純なプロセスでは、そのスキーマに互換性があることが必要です。スキーマは変更されずに移行されます。 | スキーマは Visual Studio のデータベース プロジェクトにインポートされます。Visual Studio の SSDT を使用して追加の更新が行われ、最後のスキーマを使用してその場でデータベースが更新されます。 |
| 常にデータベース全体をデプロイするか、エクスポートします。 | 移行にはオブジェクトの完全な制御が含まれています。 |
| エラーがある場合、出力を変更するためのプロビジョニングはありません。送信元スキーマには互換性が必要です。 | Visual Studio の SSDT のすべての機能を使用できます。スキーマはオフラインで変更されます。 | アプリケーションの検証は Azure で行われます。変更することなく移行するには、スキーマを最小にする必要があります。 | データベースを Azure にデプロイする前に、SQL Server でアプリケーションの検証を実行できます。 |
| 簡単に構成簡単に構成された 1 つまたは 2 つの処理手順。 | より複雑な複数の処理手順 (スキーマのデプロイのみの場合はより簡単)。 |

<!---HONumber=September15_HO1-->