<properties
	pageTitle="Azure ポータルを使用して BACPAC ファイルに Azure SQL Database をアーカイブする"
	description="Azure ポータルを使用して BACPAC ファイルに Azure SQL Database をアーカイブする"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/15/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルを使用して BACPAC ファイルに Azure SQL Database をアーカイブする

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

この記事では、[Azure ポータル](https://portal.azure.com)を使用して、(Azure BLOB ストレージに格納されている) BACPAC ファイルに Azure SQL Database をアーカイブする方法について説明します。

Azure SQL Database のアーカイブを作成する必要がある場合は、BACPAC ファイルにデータベース スキーマとデータをエクスポートできます。BACPAC ファイルは、単に BACPAC の拡張子を持つ ZIP ファイルです。BACPAC ファイルは、後で Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納することや、後で Azure SQL Database またはオンプレミス SQL Server インストールに戻ってインポートすることができます。

***考慮事項***

- アーカイブにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、または Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります。
- Azure Blob Storage にアーカイブできる BACPAC ファイルの最大サイズは 200 GB です。より大きな BACPAC ファイルをローカル ストレージにアーカイブするには、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドプロンプト ユーティリティを使用します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。SQL Server Data Tools の最新版を[ダウンロード](https://msdn.microsoft.com/library/mt204009.aspx)し、このユーティリティを入手することもできます。
- BACPAC ファイルを使用した Azure Premium Storage へのアーカイブはサポートされていません。
- エクスポート操作が 20 時間を超える場合は取り消されることがあります。エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。
 - サービス レベルを一時的に上げる。
 - エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
 - すべての大きなテーブルに null 以外の値を持つ[クラスター化インデックス](https://msdn.microsoft.com/library/ms190457.aspx)を使用する。クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。テーブルがエクスポート向けに最適化されているかを判断するための適切な方法として、**DBCC SHOW\_STATISTICS** を実行し、*RANGE\_HI\_KEY* が null 以外の値であり、分布が適切であることを確認する方法があります。詳細については、「[DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)」を参照してください。


> [AZURE.NOTE] BACPAC はバックアップおよび復元操作に使用するためのものでありません。Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。
- Azure SQL Database。
- [Azure Standard Storage アカウント](../storage/storage-create-storage-account.md) (標準的なストレージに BACPAC を格納する BLOB コンテナーを含む)。

## データベースのエクスポート

エクスポートするデータベースの [SQL Database] ブレードを開きます。

> [AZURE.IMPORTANT] トランザクションに関する BACPAC ファイルの一貫性を保証するには、最初に[データベースのコピーを作成](sql-database-copy.md)してエクスポートする必要があります。

1.	[Azure ポータル](https://portal.azure.com)にアクセスします。
2.	**[SQL Database]** をクリックします。
3.	アーカイブするデータベースをクリックします。
4.	[SQL Database] ブレードで、**[エクスポート]** をクリックして **[データベースのエクスポート]** ブレードを開きます。

    ![[エクスポート] ボタン][1]

5.  **[Storage]** をクリックし、ストレージ アカウントと BACPAC が格納される BLOB コンテナーを選択します。

    ![データベースのエクスポート][2]

6. 認証の種類を選択します。
7.  エクスポートするデータベースを含む Azure SQL Server の適切な認証資格情報を入力します。
8.  **[OK]** をクリックしてデータベースをアーカイブします。**[OK]** をクリックすると、データベースのエクスポート要求が作成され、それがサービスに送信されます。エクスポートの所要時間の長さは、サイズ、データベースの複雑さ、およびサービス レベルによって異なります。通知を受信します。

    ![通知のエクスポート][3]

## エクスポート操作の進行状況の監視

1.	**[SQL Server]** をクリックします。
2.	アーカイブした元 (ソース) のデータベースを含むサーバーをクリックします。
3.  下にスクロールして、[操作] に移動します。
4.	[SQL Server] ブレードで、**[インポート/エクスポート履歴]** をクリックします。

    ![インポート/エクスポート履歴][4]

## BACPAC がストレージ コンテナーにあることの確認

1.	**[ストレージ アカウント]** をクリックします。
2.	BACPAC アーカイブを格納したストレージ アカウントをクリックします。
3.	**[コンテナー]** をクリックし、データベースをエクスポートしたコンテナーを選択して詳細を参照します (ここから BACPAC をダウンロードして保存できます)。

    ![.bacpac ファイルの詳細][5]

## 次のステップ

- Azure SQL Database への BACPAC のインポートについては、[Azure SQL Database への BACPAC のインポート](sql-database-import.md)に関するページをご覧ください
- SQL Server データベースへの BACPAC のインポートについては、[SQL Server データベースへの BACPAC のインポート](https://msdn.microsoft.com/library/hh710052.aspx)に関するページをご覧ください



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0817_2016-->