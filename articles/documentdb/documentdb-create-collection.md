---
title: "DocumentDB データベースとコレクションの作成 | Microsoft Docs"
description: "クラウド ベースのドキュメント データベースである Azure DocumentDB 用のオンライン サービス ポータルを使用して、NoSQL データベースと JSON ドキュメント コレクションを作成する方法について説明します。 無料試用版を今すぐ入手してください。"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: f8e0b05301b83223a7cb15d55be7001f3299213f


---
# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>Azure Portal を使用して DocumentDB コレクションとデータベースを作成する方法
Microsoft Azure DocumentDB を使用するには、 [DocumentDB アカウント](documentdb-create-account.md)、データベース、コレクション、およびドキュメントを用意する必要があります。 このトピックでは、Azure ポータルで DocumentDB コレクションを作成する方法について説明します。

コレクションについて不明な場合は、 「[DocumentDB コレクションとは](#what-is-a-documentdb-collection)」を参照してください。

1. [Azure Portal](https://portal.azure.com/) で、ジャンプバーにある **[DocumentDB (NoSQL)]** をクリックし、**[DocumentDB (NoSQL)]** ブレードでコレクションを追加するアカウントを選択します。 アカウントが何も表示されていない場合は、 [DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。

   ![ジャンプバーの [DocumentDB アカウント] 、[DocumentDB アカウント] ブレードのアカウント、[DocumentDB アカウント] ブレードの [データベース] レンズのデータベースを強調表示しているスクリーン ショット](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   **[DocumentDB (NoSQL)]** がジャンプバーに表示されていない場合は、**[その他のサービス]**、**[DocumentDB (NoSQL)]** の順にクリックします。 アカウントが何も表示されていない場合は、 [DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。
2. 選択したアカウントの **[DocumentDB アカウント]** ブレードで、**[コレクションの追加]**をクリックします。

    ![ジャンプバーの [DocumentDB アカウント] 、[DocumentDB アカウント] ブレードのアカウント、[DocumentDB アカウント] ブレードの [データベース] レンズのデータベースを強調表示しているスクリーン ショット](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. **[コレクションの追加]** ブレードの **[ID]** ボックスに、新しいコレクションの ID を入力します。 コレクション名は、1 ～ 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。 名前が検証されると、緑色のチェック マークが [ID] ボックスに表示されます。

    ![Screen shot highlighting the Add Collection button on the Database blade, the settings on the Add Collection blade, and the OK button - Azure portal for DocumentDB - Cloud based database creator for NoSQL JSON databases](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. 既定では、コレクションのスループットとストレージをカスタマイズできるように、**[価格レベル]** が **[標準]** に設定されています。 価格レベルの詳細については、「 [DocumentDB のパフォーマンス レベル](documentdb-performance-levels.md)」をご覧ください。  
5. コレクションの**パーティション分割モード**として **[単一パーティション]** または **[パーティション分割]** を選択します。

    **単一のパーティション** には、予約済みの記憶域容量が 10 GB 存在し、スループット レベルは毎秒 400 ～ 10,000 要求ユニット (RU/秒) となります。 1 RU は、1 KB のドキュメントに対する読み取りのスループットに対応します。 要求ユニットの詳細については、 [要求ユニット](documentdb-request-units.md)に関するページを参照してください。

    **パーティション分割コレクション** は、拡張することによって、複数のパーティションで無制限の記憶域を扱うことができ、スループット レベルは 10,100 RU/秒以上となります。 ポータルでは、予約できる最大の記憶域は 250 GB であり、予約できる最高のスループットは 250,000 RU/秒です。 いずれかのクォータを増やすには、「 [DocumentDB アカウント制限の引き上げを要求する](documentdb-increase-limits.md)」の説明に従って要求を送信します。 パーティション分割コレクションの詳細については、「 [単一のパーティションとパーティション分割コレクション](documentdb-partition-data.md#single-partition-and-partitioned-collections)」を参照してください。

    既定では、1 つの新しい単一パーティション コレクションのスループットは、10 GB の記憶域で 1000 RU/秒に設定されています。 パーティション分割コレクションでは、コレクションのスループットは 250 GB の記憶域で 10100 RU/秒に設定されています。 コレクションのスループットと記憶域は、コレクション作成後に変更することができます。
6. パーティション分割コレクションを作成する場合は、コレクションの **パーティション キー** を選択します。 効率の良いコレクションを作成するためには、正しいパーティション キーを選択することが大切です。 パーティション キーの選択の詳細については、「 [パーティション分割の設計](documentdb-partition-data.md#designing-for-partitioning)」を参照してください。
7. **[データベース]** ブレードで、新しいデータベースを作成するか、既存のデータベースを使用します。 データベース名は、1 - 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。 名前を検証するには、テキスト ボックスの外側をクリックします。 名前が検証されると、緑色のチェック マークがボックスに表示されます。
8. 画面下部の **[OK]** をクリックすると、新しいコレクションが作成されます。
9. 新しいコレクションは、**[概要]** ブレードの **[コレクション]** レンズに表示されます。

    ![Screen shot of the new collection in the Database blade - Azure portal for DocumentDB - Cloud based database creator for NoSQL JSON databases](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **省略可能:** ポータルのコレクションでスループットを変更するには、[リソース] メニューの **[スケール]** をクリックします。

    ![[スケール] が選択された状態の [リソース] メニューのスクリーン ショット](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>DocumentDB コレクションとは
コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 コレクションは課金対象のエンティティであり、その [コスト](documentdb-performance-levels.md) は、プロビジョニングされているコレクションのスループットによって決まります。 コレクションは、1 つまたは複数のパーティション/サーバーにまたがって存在でき、拡張性があるので、扱うことのできるストレージのボリュームやスループットには実質的に制限がありません。

コレクションは、DocumentDB によって自動的に 1 つまたは複数の物理サーバーにパーティション分割されます。 プロビジョニングするスループットは、コレクションを作成するときに、1 秒あたりの要求ユニット数とパーティション キー プロパティの観点から指定できます。 DocumentDB がドキュメントを複数のパーティションに分散し、クエリなどの要求をルーティングする際に、このプロパティの値が使用されます。 パーティション キーの値は、ストアド プロシージャやトリガーに対するトランザクションの境界としても作用します。 コレクションごとに、同じアカウント内の他のコレクションとは共有されない、そのコレクション専用のスループットの量が予約されます。 したがって、ストレージとスループットの両方の観点からアプリケーションをスケールアウトすることができます。

コレクションは、リレーショナル データベースにおけるテーブルとは異なります。 コレクションは、スキーマを適用しません。実際、DocumentDB はスキーマを適用しない、スキーマ フリーのデータベースです。 そのため、さまざまなスキーマを持つ、異なる種類のドキュメントを同じコレクション内に格納できます。 テーブルを扱う場合と同様、1 種類のオブジェクトの格納にコレクションを使用するという選択肢もあります。 何が最善のモデルであるかは、クエリやトランザクションにおいて、データがどのような組み合わせで現れるかによって決まります。

## <a name="other-ways-to-create-a-documentdb-collection"></a>DocumentDB コレクションを作成するその他の方法
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

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-sql-queries)できます。 



<!--HONumber=Nov16_HO3-->


