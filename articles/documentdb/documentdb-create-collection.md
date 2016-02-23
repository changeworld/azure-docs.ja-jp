<properties 
	pageTitle="DocumentDB データベース コレクションの作成 |Microsoft Azure" 
	description="クラウド ベースの NoSQL ドキュメント データベースである Azure DocumentDB 用のオンライン サービス ポータルを使用して、JSON ドキュメント コレクションを作成する方法について説明します。無料試用版を今すぐ入手してください。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/18/2015" 
	ms.author="mimig"/>

# Azure ポータルを使用して DocumentDB コレクションを作成する方法

Microsoft Azure DocumentDB を使用するには、[DocumentDB アカウント](documentdb-create-account.md)、[データベース](documentdb-create-database.md)、コレクション、およびドキュメントを用意する必要があります。このトピックでは、Azure ポータルで DocumentDB コレクションを作成する方法について説明します。

コレクションについて不明な場合は、 「[DocumentDB コレクションとは](#what-is-a-documentdb-collection)」を参照してください。

![ジャンプバーの [DocumentDB アカウント] 、[DocumentDB アカウント] ブレードのアカウント、[DocumentDB アカウント] ブレードの [データベース] レンズのデータベースを強調表示しているスクリーン ショット](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  [Azure ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB アカウント]** をクリックします。 

2.  **[DocumentDB アカウント]** ブレードで、コレクションを追加するアカウントを選択します。アカウントが何も表示されていない場合は、[DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。

3. 選択したアカウントの **[DocumentDB アカウント]** ブレードで、**[データベース]** レンズまで下にスクロールし、コレクションを追加するデータベースを選択します。
    
4. **[データベース]** ブレードで、**[コレクションの追加]** をクリックします。

	![Screen shot highlighting the Add Collection button on the Database blade, the settings on the Add Collection blade, and the OK button - Azure portal for DocumentDB - Cloud based database creator for NoSQL JSON databases](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. **[コレクションの追加]** ブレードで、新しいコレクションの ID を入力します。コレクション名は、1 ～ 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。名前が検証されると、緑色のチェック マークが [ID] ボックスに表示されます。

6. 新しいコレクションの価格レベルを選択します。作成するそれぞれのコレクションが、課金対象のエンティティになります。使用可能なパフォーマンス レベルの詳細については、[DocumentDB のパフォーマンス レベル](documentdb-performance-levels.md)に関するページを参照してください。

7. 次のいずれかの **[インデックス作成ポリシー]** を選択します。

	- **[既定]**。このポリシーでは、文字列に対してハッシュ インデックスを、数値に対して範囲インデックスを使用します。このポリシーは、文字列に対しては等値クエリを実行し、数値に対しては ORDER BY、範囲、および等値クエリを使用する場合に最適です。また、インデックスのストレージ オーバーヘッドが小さく、地理空間インデックスを含んでいます。
	- **[範囲]**。このポリシーは、数値と文字列の両方に対してORDER BY、範囲、等値クエリを使用する場合に最適です。このポリシーは、**既定**よりもインデックスのストレージ オーバーヘッドが大きく、地理空間インデックスを含んでいます。

	インデックス作成ポリシーの詳細については、「[DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)」を参照してください。

8. 画面下部の **[OK]** をクリックすると、新しいコレクションが作成されます。


9. 新しいコレクションは、**[データベース]** ブレードの **[コレクション]** レンズに表示されます。
 
	![Screen shot of the new collection in the Database blade - Azure portal for DocumentDB - Cloud based database creator for NoSQL JSON databases](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## DocumentDB コレクションとは 

コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。コレクションは課金対象のエンティティで、その[コスト](documentdb-performance-levels.md)はコレクションに関連付けられたパフォーマンス レベルによって決まります。

コレクションは、ストアド プロシージャやトリガーにとってのトランザクション境界であり、クエリや CRUD 操作へのエントリ ポイントです。コレクションごとに、同じアカウント内の他のコレクションとは共有されない、そのコレクション専用のスループットの量が予約されます。そのため、コレクションをさらに追加し、それにドキュメントを分配することで、ストレージとスループットの両方に関してアプリケーションをスケールアウトすることができます。

コレクションは、リレーショナル データベースにおけるテーブルとは異なります。コレクションは、スキーマを適用しません。実際、DocumentDB はスキーマを適用しない、スキーマ フリーのデータベースです。そのため、さまざまなスキーマを持つ、異なる種類のドキュメントを同じコレクション内に格納できます。テーブルを扱う場合と同様、1 種類のオブジェクトの格納にコレクションを使用するという選択肢もあります。何が最善のモデルであるかは、クエリやトランザクションにおいて、データがどのような組み合わせで現れるかによって決まります。

## DocumentDB コレクションを作成するその他の方法

コレクションは必ずしもポータルを使用して作成する必要はなく、[DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して作成することもできます。DocumentDB .NET SDK を使用してコレクションを作成する方法を示す C# コードのサンプルについては、CollectionManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) ファイルを参照してください。このファイルは、[GitHub.com](https://github.com) の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) リポジトリから入手できます。

## トラブルシューティング

Azure ポータルで **[コレクションの追加]** が無効になっている場合は、お使いのアカウントが現在無効になっていることを意味します。通常、その月の特典のクレジットをすべて使用した場合に無効になります。

## 次のステップ

コレクションを作成できたら、次の手順は、コレクションへのドキュメントの追加またはインポートです。コレクションへのドキュメントの追加方法には、いくつかの選択肢があります。

- ポータルのドキュメント エクスプローラーを使用して[ドキュメントを追加](documentdb-view-json-document-explorer.md)できます。
- DocumentDB データ移行ツールを使用して[ドキュメントとデータをインポート](documentdb-import-data.md)できます。このツールでは、JSON ファイルと CSV ファイルをインポートできるほか、SQL Server、MongoDB、Azure Table Storage、その他の DocumentDB コレクションからデータをインポートできます。 
- いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用してドキュメントを追加できます。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。[GitHub.com](https://github.com) の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) リポジトリから入手できる DocumentManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) ファイルに、DocumentDB の .NET SDK を使用したドキュメントに対する CRUD 操作を示します。

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-queries)することができます。

<!---HONumber=AcomDC_0218_2016-->