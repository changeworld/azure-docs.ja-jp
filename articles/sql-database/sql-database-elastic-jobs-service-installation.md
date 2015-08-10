<properties 
	pageTitle="弾力性データベース ジョブのインストール" 
	description="弾力性ジョブの機能のインストールについて説明します。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# エラスティック データベース ジョブ コンポーネントのインストール

[エラスティック データベース プール (プレビュー)](sql-database-elastic-pool-portal.md) は、大量のデータベースをデプロイするために予測可能なモデルを提供します。エラスティック データベース プールを作成すると、**エラスティック データベース ジョブ**を使用して、エラスティック データベース プール内の各データベース間の管理タスクを実行できます。たとえば、適切な資格情報を持つ人にのみ、データ機密性の高いデータを表示する各データベースの RLS ポリシーの設定などの新しいスキーマをデプロイすることができます。以下に、**エラスティック データベース ジョブ**のインストール方法を示します。

**推定所要時間:** 10 分。

## 前提条件
* Azure サブスクリプション。無料評価版については、「[1 か月間の無料評価版](http://azure.microsoft.com/pricing/free-trial/)」をご覧ください。
* 弾力性データベース プール。「[Create and manage a SQL Database elastic pool (preview) (SQL Database エラスティック プールの作成 (プレビュー))](sql-database-elastic-pool-portal.md)」をご覧ください。

## サービス コンポーネントのインストール
最初に、[Azure プレビュー ポータル](https://ms.portal.azure.com/#)に移動します。


1. 弾力性データベース プールの [ダッシュボード] ビューで、**[ジョブの作成]** をクリックします。
2. 初めて、ジョブを作成する場合は、**[PREVIEW TERMS]** をクリックして、**弾力性データベース ジョブ**をインストールします。 
3. チェック ボックスをクリックして条項に同意します。
4. [サービスのインストール] ビューで、**[ジョブの資格情報]** をクリックします。

	![サービスのインストール][1]

5. データベース管理者のユーザー名とパスワードを入力します。スクリプト実行のための一般的なユーザーが弾力性データベース プール内のすべてのデータベース間に既に存在する場合は、この同じユーザーを使用して要件を消去し、各データベースにスクリプト実行のための新しいユーザーを追加することを検討します。インストールの一環として、新しい Azure SQL Database サーバーが作成されます。この新しいサーバー内に、管理データベースと呼ばれる新しいデータベースが作成され、弾力性データベース ジョブのメタデータの格納に使用されます。ここで作成されるユーザー名とパスワードは、(1) 管理データベースへのログインと、(2) スクリプト実行のジョブを実行する際に弾力性プール内の各データベースへのログインに使用する資格情報の 2 つの目的のために使用されます。
 
	![ユーザー名とパスワードの作成][2]
6. [OK] ボタンをクリックします。新しい[リソース グループ](../resource-group-portal.md)にコンポーネントが数分で作成されます。下記に示すように、新しいリソース グループがスタート ボードに固定表示されています。作成されると、弾力性データベース ジョブ (クラウド サービス、SQL Database、Service Bus、ストレージ記憶域) は、すべてグループ内に作成されます。

	![スタート ボードのリソース グループ][3]


7. 弾力性データベース ジョブをインストール中にジョブを作成または管理しようとすると、**資格情報**の入力中に 、次のメッセージが表示されます。

	![実行中のデプロイメント][4]

8. 何らかの理由でインストールに失敗した場合は、リソース グループを削除します。「[弾力性データベース ジョブ コンポーネントのインストール方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。


## 次のステップ

スクリプトの実行のための適切な権限で、弾力性データベース プール内の各データベースに存在しない弾力性データベース ジョブをインストールするときに、新しい資格情報を指定した場合は、各データベースで、資格情報を作成する必要があります。「[弾力性データベース プールにユーザーを追加する方法](sql-database-elastic-jobs-add-logins-to-dbs.md)」をご覧ください。ジョブの作成については、「[弾力性データベースのジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」をご覧ください。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=July15_HO5-->