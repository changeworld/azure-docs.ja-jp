<properties
	pageTitle="ポータルでの Azure Search インデックスの作成"
	description="管理ポータルでフィールド定義を入力して、Azure Search サービスにインデックスを追加する"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# ポータルでの Azure Search インデックスの作成

Azure 管理ポータルで作成することで、Azure Search のインデックスのプロトタイプをすばやく作成できます。ポータルは概念実証テストでの使用に最適です。また、サービスに展開されているすべてのインデックスに対するスキーマの定義およびリソースの使用状況を表示するために使用することもできます。

このタスクを完了するには、すぐに使用できる Azure Search サービスを備えていることを確認してください。セットアップのサポートが必要な場合は、「[Create an Azure Search service in the portal (ポータルでの Azure Search サービスの作成)](search-create-service-portal.md)」を参照してください。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. Azure Search サービスのダッシュボードを開きます。ダッシュボードを見つけるには、次のいくつかの方法があります。
	- ジャンプバーで、[**ホーム**] をクリックします。ホーム ページには、サブスクリプションのすべてのサービスのタイルが表示されます。タイルをクリックして、サービス ダッシュボードを開きます。
	- ジャンプバーで [**すべて参照**] | [**フィルター**] | [**Search サービス**] をクリックして、一覧で Search サービスを見つけます。

3. サービス ダッシュボードの最上部には、[**インデックスの追加**] を含むコマンド バーが表示されます。

	価格レベルを確認します。無料バージョンの場合は、最大 3 つのインデックスを作成できます。空き領域を得るために、インデックスを削除しなければならない場合があります。

     ![][1]

4. インデックスを削除するには、インデックスをクリックしてブレードを開きます。[**削除**] をクリックします。

     ![][2]

5. ポータルで新しいインデックスを作成すには、[**インデックスの追加**] をクリックし、*hotels* などの名前を付けます。

	インデックスの作成には時間がかかることがありますが、使用可能になったらインデックスの一覧に表示されます。

6. [*hotels*] をクリックして、インデックス定義ブレードを開きます。

	ポータルでインデックスを作成すると、入力必須フィールド (id) が作成されます。これはキー フィールドであり、各ドキュメントを一意に識別するために使用します。キーごとにフィールドは 1 つだけで (複合キーではない)、常に文字列となります。

	キー フィールドの名前を変更する場合は、インデックスの作成中のこの時点で、この手順を実行する必要があります。インデックスを作成した後、フィールドの名前を変更することはできません。

	![][3]

7. フィールド名を編集するには、フィールドの一覧にある右矢印をクリックします。

8. *id* を *hotelId* に置き換えます。

9. 各ブレード (フィールドおよびインデックス) で [**OK**] をクリックして、インデックスを作成します。

##フィールドの追加

Azure Search では、既定で searchable、 facetable、filterable などのインデックス属性が有効になっています。通常、これらの属性を設定するときは、意味のない検索動作 (並べ替えや説明のファセットなど) を無効にします。

ポータルは異なります。ポータルでは、該当するすべての動作をフィールド単位で選択できるように、既定で検索動作が無効になっています。

1. [**フィールドの追加/編集**] をクリックして、他のフィールドを追加します。この演習では、記事「[How to use Fiddler with Azure Search (Azure Search で Fiddler を使用する方法)](search-fiddler.md)」で説明されている *hotels* インデックスを再作成します。

	![][4]

2. フィールドを追加して構成し、スキーマを完了します。

	![][5]

	フィールドの名前と型の詳細については、「[名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)」および「[サポートされるデータ型](https://msdn.microsoft.com/library/azure/dn798938.aspx)」を確認してください。

    インデックス属性は、以下の項目で構成されています。

	- **Retrievable** は、検索結果でフィールドを返すことができるかどうかを設定します。
	- **Filterable** は、**$filter** クエリでフィールドを使用できるようにします。
	- **Sortable** は、並べ替えオプションとしてフィールドを使用できるようにします。
	- **Facetable** は、自律フィルター処理のファセット ナビゲーション構造でフィールドを使用できるようにします。通常は、複数のドキュメント (たとえば、1 つの製品やサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。
	- **Key** は、ドキュメント検索に使用される、各ドキュメントの一意の ID です。各インデックスに、1 つのキーが必要です。1 つのフィールドだけをキーにすることができ、そのフィールドを Edm.String に設定する必要があります。
	- **Searchable** は、フィールドをフルテキスト検索可能としてマークします。

3. 不要なフィールドを削除するには、フィールドを右クリックし、[**削除**] を選択します。

4. [**OK**] をクリックして、定義したインデックスを保存し、[インデックスの追加] ページの [**OK**] をクリックしてインデックスを構築します。


##次のステップ

インデックスが定義されましたが、ドキュメントが読み込まれるまでは使用できません。テスト目的で使用される Hotels インデックスを再作成している場合は、[Fiddler](search-fiddler.md) でそのインデックス用のいくつかのドキュメントを簡単に読み込むことができます。「[Azure Search で Fiddler を使用する方法](search-fiddler.md)」の「**ドキュメントを読み込む**」セクションの手順に従ってください。その後、この記事の残りの手順に従って、いくつかのクエリを実行することができます。

基本のインデックスに慣れたら、言語アナライザーまたはサジェスターを追加して、多言語サポートまたは検索候補を追加することを検討します。両方の機能は、インデックス スキーマで指定します。詳細については、「[Language Support (言語サポート)](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx)」および「[Create Index (インデックスの作成)](https://msdn.microsoft.com/library/azure/dn798941.aspx)」を参照してください。

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=July15_HO4-->