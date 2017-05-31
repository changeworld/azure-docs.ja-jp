---
title: "Azure Cosmos DB のデータベースとコレクションの作成 | Microsoft Docs"
description: "クラウド ベースのドキュメント データベースである Azure Cosmos DB のオンライン サービス ポータルを使用して、データベースとコンテナーを作成する方法について説明します。 無料試用版を今すぐ入手してください。"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 596a4ce9d315e7c6c41a43860469c339c7e96ac5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-collection-and-database-using-the-azure-portal"></a>Azure Portal を使用して Azure Cosmos DB のコレクションとデータベースを作成する方法
Microsoft Azure Cosmos DB を使用するには、[Azure Cosmos DB アカウント](documentdb-create-account.md)、データベース、コレクション、ドキュメントを用意する必要があります。 このトピックでは、Azure Portal で Azure Cosmos DB コレクションを作成する方法について説明します。

コレクションについて不明な場合は、 「[Azure Cosmos DB コレクションとは](#what-is-a-documentdb-collection)」をご覧ください。

ポータルでコレクションを作成する方法は 2 つあり、[コレクションの追加] ボタンを使用するか、[データ エクスプローラー (プレビュー)](#data-explorer) を使用します。

## <a name="create-a-colletion-using-add-collection-button"></a>[コレクションの追加] ボタンを使用してコレクションを作成する

1. [Azure Portal](https://portal.azure.com/) で、ジャンプバーの **[Azure Cosmos DB]** をクリックし、**[Azure Cosmos DB]** ブレードでコレクションを追加するアカウントを選択します。 アカウントが何も表示されていない場合は、 [DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。

   ![ジャンプバーの [Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]、[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)] ブレードのアカウント、[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)] ブレードの [データベース] レンズのデータベースが強調表示されたスクリーンショット](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   **[Azure Cosmos DB]** がジャンプバーに表示されていない場合は、**[その他のサービス]** をクリックし、**[Azure Cosmos DB]** をクリックします。 アカウントが何も表示されていない場合は、 [DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。
2. 選択したアカウントの **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで、**[コレクションの追加]** をクリックします。

    ![ジャンプバーの [Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]、[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)] ブレードのアカウント、[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)] ブレードの [データベース] レンズのデータベースが強調表示されたスクリーンショット](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. **[コレクションの追加]** ブレードの **[コレクション ID]** ボックスに、新しいコレクションの ID を入力します。 コレクション名は、1 ～ 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。 名前が検証されると、緑色のチェック マークが [ID] ボックスに表示されます。

    ![[データベース] ブレードの [コレクションの追加] ボタン、[コレクションの追加] ブレードの設定、[OK] ボタンが強調表示されたスクリーンショット - Azure Cosmos DB の Azure Portal - NoSQL JSON データベースのクラウド ベースのデータベース作成機能](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. 既定では、**[ストレージ容量]** は、パーティション分割コレクションを処理するために **[無制限]** に設定されています。

    スループット レベルが 400 ～ 10,000 要求ユニット/秒 (RU/秒) の[単一のパーティション コレクション](documentdb-partition-data.md#single-partition-and-partitioned-collections)にする場合は、[ストレージ容量] を**[10 GB]** に設定します。 1 RU は、1 KB のドキュメントに対する読み取りのスループットに対応します。 要求ユニットの詳細については、 [要求ユニット](documentdb-request-units.md)に関するページを参照してください。

    複数のパーティションで無制限のストレージを処理するためにスケールすることができ、スループット レベルを 2,500 RU/秒以上にすることができる[パーティション分割コレクション](documentdb-partition-data.md#single-partition-and-partitioned-collections)が必要な場合は、ストレージ容量を **[無制限]** に設定します。

    10 GB または 250 GB 以外の容量をプロビジョニングする場合は、[ストレージ容量] を **[カスタム]** に設定します。 Azure Cosmos DB は、実質的には無制限にスケールできるので、要求するストレージ サイズとスループットの値をサポート要求に含めてください。

6. **[パーティション キー]** ボックスに、コレクションのパーティション キーを入力します。 これは、パーティション分割コレクションでは必須であり、単一のパーティション コレクションでは省略可能です。 効率の良いコレクションを作成するためには、正しいパーティション キーを選択することが大切です。 パーティション キーの選択の詳細については、「 [パーティション分割の設計](../cosmos-db/partition-data.md#designing-for-partitioning)」を参照してください。
7. **[データベース]** ブレードで、新しいデータベースを作成するか、既存のデータベースを使用します。 データベース名は、1 - 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。 名前を検証するには、テキスト ボックスの外側をクリックします。 名前が検証されると、緑色のチェック マークがボックスに表示されます。
8. 画面下部の **[OK]** をクリックすると、新しいコレクションが作成されます。
9. 新しいコレクションは、**[概要]** ブレードの **[コレクション]** レンズに表示されます。

    ![[データベース] ブレードの新しいコレクションのスクリーンショット - Azure Cosmos DB の Azure Portal - NoSQL JSON データベースのクラウド ベースのデータベース作成機能](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **省略可能:** ポータルのコレクションでスループットを変更するには、[リソース] メニューの **[スケール]** をクリックします。

    ![[スケール] が選択された状態の [リソース] メニューのスクリーン ショット](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

<a id="data-explorer"></a>
## <a name="create-a-collection-by-using-data-explorer-preview"></a>データ エクスプローラー (プレビュー) を使用してコレクションを作成する

ポータルでコレクションを作成するためのもう 1 つの方法は、データ エクスプローラーを使用することです。 データ エクスプローラーを開くには、ポータルでナビゲーション バーの **[データ エクスプローラー (プレビュー)]** をクリックします。次に、次のスクリーンショットに示すように **[新しいコレクション]** ボタンをクリックします。

 ![ポータルの [新しいコレクション] ボタンを示しているスクリーンショット](./media/documentdb-create-collection/azure-documentdb-data-explorer.png)


## <a name="what-is-an-azure-cosmos-db-collection"></a>Azure Cosmos DB コレクションとは
コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 コレクションは課金対象のエンティティであり、その [コスト](documentdb-performance-levels.md) は、プロビジョニングされているコレクションのスループットによって決まります。 コレクションは、1 つまたは複数のパーティション/サーバーにまたがって存在でき、拡張性があるので、扱うことのできるストレージのボリュームやスループットには実質的に制限がありません。

コレクションは、Azure Cosmos DB によって 1 つ以上の物理サーバーに自動的にパーティション分割されます。 プロビジョニングするスループットは、コレクションを作成するときに、1 秒あたりの要求ユニット数とパーティション キー プロパティの観点から指定できます。 このプロパティの値は、Azure Cosmos DB がドキュメントを複数のパーティションに分散し、クエリなどの要求をルーティングする際に使用されます。 パーティション キーの値は、ストアド プロシージャやトリガーに対するトランザクションの境界としても作用します。 コレクションごとに、同じアカウント内の他のコレクションとは共有されない、そのコレクション専用のスループットの量が予約されます。 したがって、ストレージとスループットの両方の観点からアプリケーションをスケールアウトすることができます。

コレクションは、リレーショナル データベースにおけるテーブルとは異なります。 コレクションはスキーマを適用しません。実際には、Azure Cosmos DB はスキーマを適用しない、スキーマ フリーのデータベースです。 そのため、さまざまなスキーマを持つ、異なる種類のドキュメントを同じコレクション内に格納できます。 テーブルを扱う場合と同様、1 種類のオブジェクトの格納にコレクションを使用するという選択肢もあります。 何が最善のモデルであるかは、クエリやトランザクションにおいて、データがどのような組み合わせで現れるかによって決まります。

## <a name="other-ways-to-create-an-azure-cosmos-db-collection"></a>Azure Cosmos DB コレクションのその他の作成方法
コレクションは必ずしもポータルを使用して作成する必要はなく、 [DocumentDB SDK](documentdb-sdk-dotnet.md) と REST API を使用して作成することもできます。

* C# のコード例については、 [C# によるコレクションのサンプル](documentdb-dotnet-samples.md#collection-examples)を参照してください。
* Node.js のコード例については、 [Node.js によるコレクションのサンプル](documentdb-nodejs-samples.md#collection-examples)を参照してください。
* Python のコード例については、 [Python によるコレクションのサンプル](documentdb-python-samples.md#collection-examples)を参照してください。
* REST API のサンプルについては、 [コレクションの作成](https://msdn.microsoft.com/library/azure/mt489078.aspx)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
Azure Portal で **[コレクションの追加]** が無効になっている場合は、お使いのアカウントが現在無効になっていることを意味します。通常、アカウントが無効になるのは、その月の特典のクレジットをすべて使用した場合です。    

## <a name="next-steps"></a>次のステップ
コレクションを作成できたら、次の手順は、コレクションへのドキュメントの追加またはインポートです。 コレクションへのドキュメントの追加方法には、いくつかの選択肢があります。

* ポータルのドキュメント エクスプローラーを使用して [ドキュメントを追加](documentdb-view-json-document-explorer.md) できます。
* DocumentDB データ移行ツールを使用して[ドキュメントとデータをインポート](documentdb-import-data.md)できます。このツールでは、JSON ファイルと CSV ファイルをインポートできるほか、SQL Server、MongoDB、Azure Table Storage、その他の DocumentDB コレクションからデータをインポートできます。
* いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md)を使用してドキュメントを追加できます。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。 DocumentDB .NET SDK を使用してドキュメントを操作する方法を示す C# コード サンプルについては、「 [ドキュメントのサンプル](documentdb-dotnet-samples.md#document-examples)」を参照してください。 DocumentDB Node.js SDK を使用してドキュメントを操作する方法を示す Node.js コード サンプルについては、「 [ドキュメントのサンプル](documentdb-nodejs-samples.md#document-examples)」を参照してください。

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。 

