<properties 
	pageTitle="DocumentDB Document Explorer を使用した JSON ドキュメントの表示、編集、作成、およびアップロード | Azure" 
	description="DocumentDB Document Explorer を使用して JSON ドキュメントを表示、編集、作成、およびアップロードするための Azure プレビュー ポータル ツールである DocumentDB Document Explorer について説明します。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# DocumentDB Document Explorer を使用した JSON ドキュメントの表示、編集、作成、およびアップロード #

この記事では、[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) Document Explorer の概要を説明します。これは、DocumentDB Document Explorer を使用して JSON ドキュメントの表示、編集、作成、およびアップロードする Azure プレビュー ポータル ツールです。 

このチュートリアルを完了すると、次の質問に回答できるようになります。  

-	Web ブラウザーを使用して個々の DocumentDB ドキュメントを簡単に作成、表示、編集、および削除するにはどうすればよいか。
-	Web ブラウザーを使用して DocumentDB ドキュメントのシステム プロパティを簡単に表示するにはどうすればよいか。
-	Web ブラウザーを使用して DocumentDB へのドキュメントの一括インジェストを簡単に実行するにはどうすればよいか。

##<a id="Launch"></a>Document Explorer の起動と操作##

Document Explorer は、DocumentDB アカウント、データベース、およびコレクションのブレードのいずれからも起動することができます。  

1. 各ブレードの下部の近くに **[開発者用ツール]** レンズがあり、その中に **Document Explorer** が含まれます。

	![Screenshot of the Document Explorer part](./media/documentdb-view-JSON-document-explorer/documentexplorerpart.png) 

2. タイルをクリックするだけで、Document Explorer が起動します。

	<p>**[データベース]** ボックスと **[コレクション]** ボックスは、Document Explorer を起動した状況に応じて事前に設定されます。たとえば、データベース ブレードから起動した場合には、現在のデータベースが設定されます。コレクション ブレードから起動した場合には、現在のコレクションが設定されます。

	![Screenshot of Document Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

3. Document Explorer には、選択したコレクション内の最初の 100 個のドキュメントが読み込まれます。Document Explorer ブレードの下部にある **[さらに読み込む]** オプションをクリックすることで、(100 個単位で) 別のドキュメントを読み込むことができます。  

4. **[データベース]** ボックスと **[コレクション]** ボックスを使用すると、Document Explorer を閉じて再度起動することなく、現在表示されているドキュメントが含まれるコレクションを簡単に変更できます。  

5. Document Explorer では、現在読み込まれているドキュメントのセットを ID プロパティでフィルター処理することもできます。その方法は、フィルター ボックスに入力するだけです。

	![Screenshot of Document Explorer with filter highlighted](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png) 

	Document Explorer の一覧の結果は、指定された条件に基づいてフィルター処理されます。

	![Screenshot of Document Explorer with filtered results](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)


	> [AZURE.IMPORTANT] Document Explorer のフィルター機能では、***現在***読み込まれているドキュメントのみがフィルター処理されます。現在選択されているコレクションに対してクエリが実行されることはありません。

6. Document Explorer に読み込まれたドキュメントの一覧を更新するには、ブレードの上部にある **[更新]** をクリックするだけです。

	![Screenshot of Document Explorer refresh command](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)


##<a id="Create"></a>Document Explorer を使用したドキュメントの表示、作成、および編集##

Document Explorer では、ドキュメントを簡単に作成、編集、および削除することができます。  

- ドキュメントを作成するには、**[ドキュメントの作成]** をクリックするだけで、最小限の JSON スニペットが入力されます。

	![Screenshot of Document Explorer create document experience](./media/documentdb-view-JSON-document-explorer/createdocument.png) 

- 作成するドキュメントの JSON コンテンツを入力するか貼り付け、**[保存]** をクリックするだけで、ドキュメントがコミットされます。

	![Screenshot of Document Explorer save command](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] "ID" プロパティを指定していない場合は、Document Explorer によって自動的に ID プロパティが追加され、ID 値として GUID が生成されます。 

- 既存のドキュメントを編集するには、Document Explorer でそのドキュメントを選択し、自由に編集して **[保存]** をクリックするだけです。

	![Screenshot of Document Explorer edit document functionality](./media/documentdb-view-JSON-document-explorer/editdocument.png)

- ドキュメントの編集中に現在の編集内容を破棄する必要がある場合は、破棄コマンドをクリックして破棄アクションを確認するたけで、前の状態のドキュメントが再度読み込まれます。

	![Screenshot of Document Explorer discard command](./media/documentdb-view-JSON-document-explorer/discardedit.png)

- ドキュメントを削除するには、そのドキュメントを選択し、**[削除]** をクリックして、削除を確認します。確認後、ドキュメントは Document Explorer の一覧から即座に削除されます。

	![Screenshot of Document Explorer delete command](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

- Document Explorer では、新規または編集されたドキュメントに有効な JSON が含まれているかどうかが検証されます。不適切なセクションをマウスでポイントして、検証エラーの詳細を表示することもできます。

	![Screenshot of Document Explorer invalid JSON highlighting](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

- また、Document Explorer では、無効な JSON コンテンツを含むドキュメントを保存できません。

	![Screenshot of Document Explorer invalid JSON save error](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

- 最後に、Document Explorer で **[プロパティ]** をクリックすると、現在読み込まれているドキュメントのシステム プロパティを表示することができます。

	![Screenshot of Document Explorer document properties view](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

	> [AZURE.NOTE] タイムスタンプ (_ts) プロパティは内部ではエポック時間として表現されますが、Document Explorer では、人間が読むことができる GMT 形式で値が表示されます。

##<a id="BulkAdd"></a>Document Explorer を使用したドキュメントの一括追加##

Document Explorer では、1 つまたは複数の既存の JSON ドキュメントの一括インジェストをサポートしています。  

1. アップロード処理を開始するには、**[ドキュメントの追加]** をクリックします。
	
	![Screenshot of Document Explorer bulk ingestion functionality](./media/documentdb-view-JSON-document-explorer/adddocument1.png)

2. 新しいブレードが開きます。[参照] をクリックしてエクスプローラー ウィンドウを開き、アップロードする JSON ドキュメントを 1 つ以上選択します。

	![Screenshot of Document Explorer bulk ingestion process](./media/documentdb-view-JSON-document-explorer/adddocument2.png)

	> [AZURE.NOTE] Document Explorer では、現在、アップロード処理ごとに最大 100 個の JSON ドキュメントをサポートしています。

3. 選択したら、**[アップロード]** をクリックします。ドキュメントが自動的に Document Explorer グリッドに追加されます。操作の進捗状況に合わせてアップロード結果が表示されます。インポートが失敗すると、ファイルごとに報告されます。

	![Screenshot of Document Explorer bulk ingestion results](./media/documentdb-view-JSON-document-explorer/adddocument3.png)

4. 操作が完了すると、アップロードする別のドキュメントを最大 100 個選択することができます。

##<a name="NextSteps"></a>次のステップ

DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。

<!--HONumber=49-->