<properties
   pageTitle="Azure ポータルでの SQL Data Warehouse データベースの作成 | Microsoft Azure"
   description="Azure ポータルで Azure SQL Data Warehouse を作成する方法を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# 新しい論理サーバーの作成

SQL Database および SQL Data Warehouse では、各データベースがサーバーに割り当てられ、それぞれのサーバーが地理的な場所に割り当てられます。このサーバーを論理 SQL サーバーといいます。

> [AZURE.NOTE] <a name="note"></a>論理 SQL サーバー:
  >
  > + 地理的に同じ場所に複数のデータベースを構成するための、一貫性のある方法を提供します。
  > + オンプレミスのサーバーのような物理的なハードウェアではありません。サービスのソフトウェアの構成要素です。*論理サーバー*と呼ばれているのは、そのためです。
  > + 複数のデータベースを、それぞれのパフォーマンスに影響を与えずにホストできます。
  > + 名前にはカタカナの *"サーバー"* を使用しています。SQL **サーバー**が Azure の論理サーバーであるのに対し、SQL **Server** は Microsoft のオンプレミス データベース製品です。

1. **[サーバー]**、**[新しいサーバーの作成]** をクリックします。サーバーの料金は発生しません。使用する V12 論理 SQL サーバーが既にある場合は、既存のサーバーを選択し、次の手順に進みます。

    ![新しいサーバーの作成](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. **新しいサーバー**の情報を入力します。

	- **[サーバー名]**: 論理サーバーの名前を入力します。これは、地理的な場所ごとに一意です。
	- **[サーバー管理者名]**: サーバーの管理者アカウントのユーザー名を入力します。
	- **[パスワード]**: サーバー管理者のパスワードを入力します。
	- **[場所]**: サーバーの地理的な場所を選択します。データの転送時間を短縮するために、このデータベースがアクセスする他のデータ リソースと地理的に近い場所にサーバーを配置することをお勧めします。
	- **[V12 サーバーの作成]**: SQL Data Warehouse の場合は常に [はい] を選択してください。
	- **[Azure サービスにサーバーへのアクセスを許可する]**: SQL Data Warehouse の場合は常にオンになります。

    >[AZURE.NOTE] サーバー名、サーバー管理者名、パスワードは、必ずどこかに記録してください。この情報は、サーバーにログオンするときに必要になります。

3. **[OK]** をクリックして論理 SQL サーバーの構成設定を保存し、[SQL Data Warehouse] ブレードに戻ります。

    ![新しいサーバーの構成](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->