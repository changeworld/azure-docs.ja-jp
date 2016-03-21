<properties
	pageTitle="Azure ポータルで Search エクスプローラーを使用して Azure Search インデックスに対してクエリを実行する | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Search エクスプローラーは、Azure ポータルでコードを使用せずに Azure Search インデックスに対してクエリを実行する方法です。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Azure ポータルで Search エクスプローラーを使用して Azure Search インデックスに対してクエリを実行する
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

**Search エクスプローラー**は Azure ポータルに組み込まれているクエリ ツールで、コードを使用せずに Azure Search インデックスに対してクエリを実行します。サービス内の任意のインデックスに接続し、検索文字列と式を入力するための検索ボックスを提供します。有効なクエリ構文が、入力に基づいて生成されます。結果がポータル ページに表示されます。

Search エクスプローラーは、クエリの構文の習得や、不定期のアドホック クエリの実行、コードに記述する前に、クエリ式の再設定に最適です。これを使用するには、Azure Search サービスとインデックスが必要です。これらのタスクのヘルプについては、「[ポータルでの Azure Search サービスの作成](search-create-service-portal.md)」と「[ポータルを使用した Azure Search へのデータのインポート](search-import-data-portal.md)」を参照してください。

## Search エクスプローラーを開く

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. Azure Search サービスのサービス ダッシュボードを開きます。ダッシュボードを見つけるには、次のいくつかの方法があります。
	- ジャンプバーで、[**ホーム**] をクリックします。ホーム ページには、サブスクリプションのすべてのサービスのタイルが表示されます。タイルをクリックして、サービス ダッシュボードを開きます。
	- ジャンプバーで **[すべて参照]**、**[フィルター]**、**[Search サービス]** の順にクリックして、一覧で Search サービスを見つけます。

3. サービス ダッシュボードの最上部には、**[Search エクスプローラー]** を含むコマンド バーが表示されます。

  	![][1]

4. **[Search エクスプローラー]** をクリックして、Search エクスプローラー ブレードを開きます。
5. インデックスと API のバージョンを設定します。Search エクスプローラーはインデックス リストの最初のインデックスに自動的に接続しますが、**[インデックスの変更]** をクリックして別のインデックスに切り替えることもできます。**[API バージョンを設定]** では、一般公開されているバージョンまたはプレビュー バージョンを指定できます。クエリ構文の中にはプレビュー バージョンしかないものもあります。
6. 「[Azure Search の使用](search-get-started-portal.md)」に従ってロードアイランド州の米国地質調査所 (USG) のデータに基づいてインデックスを作成した場合は、この検索用語 (`roger williams +school -building`) を使用して、Search エクスプローラーで返された同じ 3 つの結果を検証できます。

検索用語の入力への応答で自動的に生成されるクエリ構文に注目してください。

![][2]

## 次のステップ

クエリ構文と例の詳細については、MSDN の[ドキュメントの検索](https://msdn.microsoft.com/library/azure/dn798927.aspx)にあります。

検索サービスまたはインデックスを、コードを追加せずに作成または管理する方法については、次のリンクを参照してください。

- [ポータルでの Azure Search サービスの作成](search-create-service-portal.md)
- [ポータルを使用した Azure Search へのデータのインポート](search-import-data-portal.md)
- [Microsoft Azure で Search サービスを管理する](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->