<properties
	pageTitle="DocumentDB スクリプト エクスプローラーを使用したストアド プロシージャ、トリガー、およびユーザー定義関数の表示 | Microsoft Azure"
	description="DocumentDB のサーバー側プログラミング アーティファクト (ストアド プロシージャ、トリガー、ユーザー定義関数など) を表示するための Azure ポータル ツール、DocumentDB スクリプト エクスプローラーについて説明します。"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="12/01/2015"
	ms.author="anhoh"/>

# DocumentDB スクリプト エクスプローラーを使用してストアド プロシージャ、トリガー、ユーザー定義関数を表示、編集、作成する

この記事では、DocumentDB のサーバー側プログラミング アーティファクト (ストアド プロシージャ、トリガー、ユーザー定義関数など) の表示を可能にする Azure ポータル ツール、[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) スクリプト エクスプローラーの概要を説明します。DocumentDB サーバー側のプログラミングについて詳しくは、[こちら](documentdb-programming.md)をご覧ください。

このチュートリアルを完了すると、次の質問に回答できるようになります。

-	Web ブラウザーを使用して DocumentDB ストアド プロシージャを簡単に表示する方法を教えてください。
-	Web ブラウザーを使用して DocumentDB トリガーを簡単に表示する方法を教えてください。
-	Web ブラウザーを使用して DocumentDB ユーザー定義関数を簡単に表示する方法を教えてください。

## スクリプト エクスプローラーの起動と操作

スクリプト エクスプローラーは、DocumentDB のアカウント、データベース、コレクションのいずれかのブレードから起動することができます。

1. DocumentDB アカウントまたはデータベース ブレードの上部で、**スクリプト エクスプローラー** コマンドをクリックします。

	![スクリプト エクスプローラー コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. または、各ブレードの下部の近くに **[開発者用ツール]** レンズがあり、その中に**スクリプト エクスプローラー** パーツが含まれています。

	![スクリプト エクスプローラー パーツのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. コマンドまたはそのパーツをクリックし、スクリプト エクスプローラーを起動します。

	<p>**[データベース]** と **[コレクション]** の各ドロップダウン リスト ボックスには、スクリプト エクスプローラーを起動したコンテキストに応じて値が設定されています。たとえば、データベース ブレードから起動した場合には、現在のデータベースが設定されます。コレクション ブレードから起動した場合には、現在のコレクションが設定されます。

	![スクリプト エクスプローラーのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. **[データベース]** ボックスと **[コレクション]** ボックスを使用すると、スクリプト エクスプローラーを閉じて再度起動することなく、現在表示されているドキュメントが含まれるコレクションを簡単に変更できます。

4. スクリプト エクスプローラーでは、現在読み込まれているドキュメントのセットを ID プロパティでフィルター処理することもできます。その方法は、フィルター ボックスに入力するだけです。

	![フィルターが強調表示されたスクリプト エクスプローラーのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	スクリプト エクスプローラーの一覧の結果は、指定された条件に基づいてフィルター処理されます。

	![フィルターの結果が表示されたスクリプト エクスプローラーのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]スクリプト エクスプローラーのフィルター機能では、現在読み込まれているドキュメントのみがフィルター処理されます。現在選択されているコレクションに対してスクリプトが自動的に更新されることはありません。

5. スクリプト エクスプローラーに読み込まれたスクリプトトの一覧を更新するには、ブレードの上部にある **[更新]** をクリックするだけです。

	![スクリプト エクスプローラーの [最新の情報に更新] コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## スクリプト エクスプローラーを使用してストアド プロシージャ、トリガー、ユーザー定義関数を表示、編集、作成する

スクリプト エクスプローラーを使用すると、DocumentDB サーバー側プログラミング アーティファクトに対する CRUD 操作を簡単に実行できます。

- スクリプトを作成するには、スクリプト エクスプローラーで適切な作成コマンドをクリックし、ID を指定し、スクリプトの内容を入力し、**[保存]** コマンドをクリックします。

	![スクリプト エクスプローラーの作成オプションのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- トリガーを作成する場合は、トリガーの種類とトリガー操作を指定する必要があります。

	![スクリプト エクスプローラーのトリガー作成オプションのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- スクリプトを表示するには、関心のあるスクリプトをクリックします。

	![スクリプト エクスプローラーのスクリプト表示エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- スクリプトを編集するには、目的の変更を行い、**[保存]** コマンドをクリックします。

	![スクリプト エクスプローラーのスクリプト表示エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- スクリプトの保留中の変更を破棄するには、**[破棄]** コマンドをクリックします。

	![スクリプト エクスプローラーの変更破棄エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- スクリプト エクスプローラーで **[プロパティ]** をクリックすると、現在読み込まれているスクリプトのシステム プロパティを簡単に表示することもできます。

	![スクリプト エクスプローラーのスクリプトのプロパティ表示のスクリーンショット](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]タイムスタンプ (\_ts) プロパティは内部ではエポック時間として表現されますが、スクリプト エクスプローラーでは、人間が読むことができる GMT 形式で値が表示されます。

- スクリプトを削除するには、スクリプト エクスプ ローラーで削除するスクリプトを選択し、**[削除]** コマンドをクリックします。

	![スクリプト エクスプローラーの削除コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- 削除アクションを **[はい]** をクリックして確認するか、**[いいえ]** をクリックして取り消します。

	![スクリプト エクスプローラーの削除コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## 次のステップ

DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。
 

<!---HONumber=AcomDC_1203_2015-->