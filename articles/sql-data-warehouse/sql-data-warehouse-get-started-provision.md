<properties
	pageTitle="Azure プレビュー ポータルでの SQL Data Warehouse の作成 | Microsoft Azure"
	description="Azure プレビュー ポータルで Azure SQL Data Warehouse を作成する方法を説明します。"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2015"
   ms.author="lodipalm;barbkess"/>

# Azure プレビュー ポータルでの SQL Data Warehouse の作成#

このチュートリアルでは、Azure プレビュー ポータルで Azure SQL Data Warehouse を数分で簡単に作成する方法を示します。

このチュートリアルでは次を行います。

- 新しい SQL Data Warehouse データベースを作成します。
- データベース用にサーバーを作成します。
- 新しいデータベースに AdventureWorksDW を読み込みます。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## サインインおよび SQL Data Warehouse の検索

1. [プレビュー ポータル](https://portal.azure.com)にサインインします。

2. ハブ メニューで **[新規]**、**[データ + ストレージ]**、**[SQL Data Warehouse]** をクリックします。

	![データ ウェアハウスの作成](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

## パフォーマンスおよびその他の基本設定の構成

**[SQL Data Warehouse]** ウィンドウで次のフィールドを入力します。**[サーバー]** と **[ソース]** は次のセクションで構成します。

1. **[データベース名]**: SQL Data Warehouse データベースの名前を入力します。

2. **[パフォーマンス]**: インスタンスのプロビジョニング時の開始パフォーマンスを調整できます。SQL Data Warehouse が提供する MPP の利点をより明らかにする、400 DWU から始めることをお勧めします。
     
    ![名前および DWU](./media/sql-data-warehouse-get-started-provision/name-and-dwu.png)

    > [AZURE.NOTE] パフォーマンスは、Data Warehouse ユニット (DWU) で測定します。DWU が上がると、SQL Data Warehouse では、データ ウェアハウスのデータベース操作に使用できるコンピューティング リソースが増えます。

	> [AZURE.NOTE] パフォーマンス レベルは、データベースの作成後に迅速かつ簡単に変更できます。たとえば、データベースを使用していない場合は、左にスライダーを移動し、コストを削減します。または、さらにリソースが必要な場合はパフォーマンスを上げます。これが、SQL データ ウェアハウスの拡張性です。
	

2. **[リソース グループ]**。既定値のままにします。リソース グループは、Azure リソースのコレクション管理のサポートを目的としたコンテナーです。[リソース グループ](../azure-portal/resource-group-portal.md)に関する詳細情報を参照してください。
3. **[サブスクリプション]**。このデータベースで請求する、サブスクリプションを選択します。


## 論理サーバーの構成

3. **[サーバー]**、**[新しいサーバーの作成]** をクリックします。これにより、データベースに関連付けられる論理サーバーが作成されます。使用する V12 サーバーが既にある場合は、既存のサーバーを選択し、次のセクションに移動します。

    ![新しいサーバーの作成](./media/sql-data-warehouse-get-started-provision/create-new-server.png)

    >[AZURE.NOTE] SQL Data Warehouse と SQL Database では、サーバーが、クラウドベースのデータベースを構成する一貫した方法を提供します。Azure では、サーバーは 1 つのデータ センターに関連付けられていますが、オンプレミスの SQL Server インスタンスの場合とは異なり、これは物理ハードウェアではなくサービス ソフトウェアの一部です。そのため、論理サーバーと呼ばれています。現実の場合とは異なり、同じサーバーでデータベースとデータ ウェアハウスが複数のワークロードで実行されていても、互いのパフォーマンスには影響しないことに注意してください。

1. **[新しいサーバー]** ウィンドウに要求された情報を入力します。

    サーバー名、管理者名およびパスワードは、必ずどこかに記録してください。この情報は、サーバーにログオンするときに必要になります。
	- **[サーバー名]**:論理サーバー名を入力します。
	- **[サーバー管理者名]**:サーバーの管理者アカウントのユーザー名を入力します。
	- **[パスワード]**:サーバーの管理者パスワードを入力します。
	- **[場所]**:ユーザーおよびその他の Azure リソースに近い地理的な場所を選択します。これにより、論理サーバーに属するすべてのデータベースとリソースが同じリージョンに物理的に配置されるため、ネットワークの待機時間が減少します。

    ![新しいサーバーの構成](./media/sql-data-warehouse-get-started-provision/configure-new-server.png)

1. **[OK]** をクリックして、サーバーの構成設定を保存します。

## サンプル データベースの読み込み

1. **[ソース]**、**[サンプル]** を選択し、新しいデータベースを、AdventureWorksDW のサンプル データベースで初期化します。 

    ![データ ウェアハウスの作成](./media/sql-data-warehouse-get-started-provision/create-data-warehouse.png)

## データベースの作成完了

1. **[作成]** をクリックして、SQL Data Warehouse データベースを作成します。 

1. 後は数分間待機するのみです。完了すると、ホーム ページにサンプル データベースが表示されます。

    ![SQL Data Warehouse ポータル ビュー](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

## 次のステップ

これで SQL Data Warehouse のサンプル データベースの作成が完了しました。次のチュートリアルでは SQL Data Warehouse の使用方法について説明します。

- 「[接続とクエリ](./sql-data-warehouse-get-started-connect-query.md)」。

	> [AZURE.NOTE] 記事の改善にご協力ください。「この記事は役に立ちましたか?」という質問に「いいえ」を選択した場合は、不足しているものやどうすれば記事を改善できるかについてのご提案をお願いいたします。ご協力ありがとうございます。

<!----HONumber=Oct15_HO1-->