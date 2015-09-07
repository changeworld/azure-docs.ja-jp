<properties
   pageTitle="SSMS を使用した SQL Database への移行"
	description="Microsoft Azure SQL Database、sql データベースの移行、ssms を使用した移行"
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
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#SSMS を使用した互換性のあるデータベースの移行

![SSMS の移行ダイアグラム](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

データベース スキーマと Azure SQL Database に互換性がある場合、移行で必要なのはデータベースを Azure にインポートすることのみです。これは、SSMS を使用してデータベースを Azure SQL Database に "デプロイする" という単一の手順で実行することも、最初に現在のデータベースの BACPAC をエクスポートした後、その BACPAC を新しいデータベースとして Azure SQL Server にインポートするという 2 段階の手順で実行することもできます。

BACPAC をエクスポートする場合は、ローカル ファイルか、直接 Azure BLOB にエクスポートできます。ローカルにエクスポートする場合は、エクスポートした BACPAC を Azure BLOB にアップロードできます。Azure BLOB に BACPAC ファイルを保存すると、Azure ポータルを使用してデータベースとして BACPAC をインポートできます。Azure ポータルでインポートを実行すると、インポートの段階での待機時間が短縮され、大規模なデータベースでの移行のパフォーマンスと信頼性が向上します。

SSMS から直接デプロイすると、常にスキーマ全体とすべてのデータがデプロイされますが、BACPAC を使用したエクスポートでは、BACPAC をエクスポートするオブジェクトのサブセットを選択できます。エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。SSMS からデプロイするか、SSMS (または Azure ポータル) からエクスポートしてインポートするかに関係なく、内部では同じ DAC テクノロジが使用されているため、結果は同じになります。

このオプションは、2 番目の方法の最後の手順としても使用され、データベースを Azure SQL Database と互換性を持つように更新してから移行します。

## 最新バージョンの SQL Server Management Studio を入手する

最新バージョンの Microsoft SQL Server Management Studio for SQL Server を使用して、SSMS 内のツールと Azure ポータルの操作に最新の更新プログラムがあることを確認します。最新の Microsoft SQL Server Management Studio for SQL Server を入手するには、[これをダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)して、移行するデータベースとインターネットに接続されているクライアント コンピューターにインストールします。

##SSMS を使用して Azure SQL Database にデプロイする
1.	Microsoft Azure 管理ポータルを使用して論理サーバーをプロビジョニングします。
2.	SSMS オブジェクト エクスプローラーでソース データベースを検索し、タスク **[データ層アプリケーションのエクスポート...]** を実行します。

	![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	デプロイメント ウィザードで、手順 1 でプロビジョニングしたターゲットとなる Azure SQL Database 論理サーバーへの接続を構成します。
4.	データベースの**名前**を指定し、**エディション** (サービス プラン) と**サービス目標** (パフォーマンス レベル) を設定します。これらの設定の構成については、「[Azure SQL Database のサービス階層](sql-database-service-tiers.md)」をご覧ください。

	![設定のエクスポート](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	データベースを移行するウィザードを完了します。データベースのサイズと複雑さに応じて、デプロイメントには数分から数時間かかる場合があります。互換性がない場合は、実際に Azure へのデプロイが発生する前にスキーマの検証ルーチンがすばやくエラーを検出します。データベース スキーマと SQL Database に互換性がないことをエラーが示している場合は、2 番目の移行オプションを使用してください。「[データベースのインプレース更新と Azure SQL Database へのデプロイ](sql-database-migrate-visualstudio-ssdt.md)」をご覧ください。

##SSMS で BACPAC をエクスポートしてから SQL Database にインポートする
デプロイメント プロセスは、エクスポートとインポートの 2 つの手順に分けることができます。最初の手順では、2 番目の手順で入力として使用される BACPAC ファイルを作成します。

1.	Microsoft Azure 管理ポータルを使用して論理サーバーをプロビジョニングします。
2.	SSMS オブジェクト エクスプローラーでソース データベースを検索し、タスク **[Azure SQL Database へのデータベースのデプロイ...]** を選択します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. エクスポート ウィザードで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。一部またはすべてのテーブルからデータを除外する場合は、[詳細設定] タブを使用してください。たとえば、参照テーブルのデータのみをエクスポートするように選択できます。

	![設定のエクスポート](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	BACPAC が作成されたら、手順 1 で作成したサーバーに接続し、**[データベース]** フォルダーを右クリックして、**[データ層アプリケーションのインポート...]** をクリックします。
	
	>[AZURE.NOTE]注: Microsoft Azure 管理ポータル内の Azure BLOB に保存されている BACPAC ファイルをインポートすることもできます。

	![[データ層アプリケーションのインポート] メニュー項目](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	インポート ウィザードで、エクスポートした BACPAC ファイルを選択して、Azure SQL Database に新しいデータベースを作成します。

	![設定のインポート](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	データベースの名前を指定し、エディション (サービス プラン) とサービス目標 (パフォーマンス レベル) を設定します。

7.	ウィザードを完了して、BACPAC ファイルをインポートし、Azure SQL Database にデータベースを作成します。

	![データベース設定](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##代替手段
コマンド ライン ユーティリティである sqlpackage.exe を使用して、データベースのデプロイまたは BACPAC のエクスポートとインポートを実行することもできます。Sqlpackage.exe では、SSMS と同じ DAC テクノロジが使用されているため、結果は同じです。詳細については、MSDN の「[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)」をご覧ください。

<!---HONumber=August15_HO9-->