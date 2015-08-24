<properties 
	pageTitle="DocumentDB のパフォーマンス レベル | Microsoft Azure" 
	description="DocumentDB のパフォーマンス レベルを使用して、コレクションごとにスループットを予約できるようにする方法について説明します。" 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="johnmac"/>

#DocumentDB のパフォーマンス レベル

この記事では、[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) のパフォーマンス レベルの概要について説明します。

この記事を読むと、次の質問に回答できるようになります。

-	パフォーマンス レベルとは何か。
-	スループットはデータベース アカウントに対してどのように予約されるか。
-	パフォーマンス レベルをどのように使用するか。
-	パフォーマンス レベルはどのように課金されるか。

##パフォーマンス レベルとは

Standard アカウントで作成された DocumentDB コレクションはそれぞれ、関連するパフォーマンス レベルを使用してプロビジョニングされます。パフォーマンス レベルは、最低のパフォーマンスから最高のパフォーマンスまで、S1、S2、S3 のレベルで指定されます。コレクションのパフォーマンス レベルによって、アプリケーション用に予約されるサービス リソースの量が決まります。データベース内の各コレクションのパフォーマンス レベルは異なっていてもよいので、頻繁にアクセスするコレクションにはより多いスループット、あまりアクセスしないコレクションにはより低いスループットを指定することができます。

各パフォーマンス レベルには、関連する要求単位 (RU) レートの制限があります。これは、パフォーマンス レベルに基づいてコレクションに予約されるスループットで、そのコレクションが独占的に使用することができます。コレクションの作成は、[Azure ポータル](http://portal.azure.com)で、または [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して行います。DocumentDB API を使用して、コレクションのパフォーマンス レベルを指定することができます。

コレクションのパフォーマンス レベル|予約済みのスループット
---|---
S1|250 RU/秒
S2|1,000 RU/秒
S3|2,500 RU/秒

DocumentDB では、クエリ、ユーザー定義関数 (UDF) を使用したクエリ、ストアド プロシージャ、トリガーなど、さまざまなデータベース操作を実行できます。これらの各操作に関連付けられる処理コストは、CPU、IO、操作の完了に必要なメモリによって異なります。ハードウェア リソースの検討や管理をする代わりに、要求単位をさまざまなデータベース操作の実行やアプリケーション要求の処理に必要なリソースの 1 つのメジャーとして考えることができます。

> [AZURE.NOTE]パフォーマンス レベルは要求単位で測定されます。各パフォーマンス レベルには、1 秒あたりの最大要求単位が関連付けられています。コレクションのパフォーマンス レベルの調整は、API や [Azure ポータル](https://portal.azure.com/)を使用して行います。

##コレクションのパフォーマンス レベルの設定
コレクションが作成されると、指定されたパフォーマンス レベルに基づいたすべての RU の割り当てがコレクションに対して予約されます。たとえば、コレクションのパフォーマンス レベルが S3 に指定されている場合、このコレクションは毎秒 2,500 の RU を処理することができます。コレクションはそれぞれ、指定されたスループットと 10 GB のデータベース ストレージを予約します。コレクションの価格は、選択したパフォーマンス レベル (S1、S2、S3) によって異なります。DocumentDB は容量の予約に基づいて動作します。コレクションを作成すると、アプリケーションはスループットとデータベース ストレージを予約し、そのストレージとスループットが実際にどれだけ使用されているかにかかわらず、予約されたスループットとストレージに応じて課金されます。

コレクションを作成した後、DocumentDB SDK や Microsoft Azure 管理ポータルを使用してパフォーマンス レベルを変更することができます。

> [AZURE.IMPORTANT]DocumentDB Standard のコレクションは 1 時間ごとに課金され、ユーザーが作成した各コレクションは最小 1 時間の使用に対して課金されます。

1 時間の間にパフォーマンス レベルを調整した場合、その時間内に設定された最高のパフォーマンス レベルで課金されます。たとえば、午前 8 時 53 分にコレクションのパフォーマンス レベルを上げた場合は、午前 8 時からその新しいレベルで課金されます。同様に、午前 8 時 53 分にパフォーマンス レベルを下げた場合は、新しいレートが午前 9 時から適用されます。

設定されたパフォーマンス レベルに基づいて、各コレクションに要求単位が予約されます。要求単位の消費は 1 秒ごとに評価されます。コレクションに対してプロビジョニングされた要求単位レート (またはパフォーマンス レベル) をアプリケーションが超過した場合、そのレートがコレクションに対して予約されたレベルを下回るまで、そのアプリケーションは調整されます。より高レベルのスループットがアプリケーションに必要な場合は、各コレクションのパフォーマンス レベルを上げることができます。

> [AZURE.NOTE]1 つ以上のコレクションでアプリケーションがパフォーマンス レベルを超えると、コレクションごとに要求が調整されます。つまり、アプリケーションの要求が成功する場合と調整される場合があります。

##パフォーマンス レベルの操作
DocumentDB のコレクションを使用すると、クエリ パターンとアプリケーションのパフォーマンス ニーズの両方に応じてデータをパーティション分割することができます。DocumentDB を使用したデータのパーティション分割の詳細については、[データのパーティション分割に関するドキュメント](documentdb-partition-data.md)を参照してください。DocumentDB は自動インデックス作成とクエリをサポートしているため、同一コレクション内に異種ドキュメントを配置することもよくあります。データを別々のコレクションに分けるかどうかを決めるときの主な考慮事項は次のとおりです。

- クエリ - コレクションは、クエリ実行のスコープです。一連のドキュメント間でクエリを実行する必要がある場合、最も効率的な読み取りのパターンは、単一コレクションにドキュメントを配置することで実現できます。
- トランザクション - コレクションは、ストアド プロシージャとトリガーのトランザクション ドメインです。すべてのトランザクションは単一のコレクションにスコープされます。 
- パフォーマンス - コレクションにはパフォーマンス レベルが関連付けられています。これにより、各コレクションは予約済みの RU を使用してパフォーマンスを予測できます。アクセス頻度に応じて、パフォーマンス レベルの異なるさまざまなコレクションにデータを割り当てることができます。

> [AZURE.IMPORTANT]アプリケーションで作成されたコレクションの数に基づいて、標準料金の全額が課金されることを理解することが重要です。

保存容量やスループットの大きさが求められている場合を除いて、アプリケーションで使用するコレクションは少数にとどめておくことをお勧めします。新しいコレクションの作成に対するアプリケーションのパターンを十分に理解しておいてください。コレクションの作成を、アプリケーション外部で処理する管理アクションにしておくこともできます。同様に、コレクションのパフォーマンス レベルを調整すると、コレクションの時間あたりの課金レートが変更されます。アプリケーションでこれらを動的に調整する場合は、コレクションのパフォーマンス レベルを監視する必要があります。

##Azure プレビュー ポータルを使用するパフォーマンス レベルの変更

Azure プレビュー ポータルは、コレクションのパフォーマンス レベルを管理する場合に使用できる方法の 1 つです。Azure ポータルから、コレクションのパフォーマンス レベルを変更するには、次の手順に従います。

1. ブラウザーから [**Azure プレビュー ポータル**](https://portal.azure.com)に移動します。
2. 左側にあるジャンプ バーから **[参照]** をクリックします。
3. **[参照]** ハブで、**[フィルター]** ラベルの下の **[DocumentDB アカウント]** をクリックします。
4. **[DocumentDB アカウント]** ブレードで、目的のコレクションが含まれている DocumentDB アカウントをクリックします。
5. **[DocumentDB アカウント]** ブレードで、**[データベース]** レンズまで下にスクロールして目的のコレクションが含まれているデータベースをクリックします。 
6. 新しく開かれた **[データベース]** ブレードで、**[コレクション]** レンズまで下にスクロールして目的のコレクションを選択します。
7. 最後に、**[コレクション]** ブレードで、**[使用状況]** レンズの **[価格レベル]** タイルを探してクリックします。
8. **[価格レベルの選択]** ブレードで、目的のパフォーマンス レベルをクリックし、ブレードの下部にある **[選択]** をクリックします。 

>[AZURE.NOTE]コレクションのパフォーマンス レベルの変更には、最大で 2 分かかる場合があります。

![価格レベルの変更][1]

##.NET SDK を使用するパフォーマンス レベルの変更

コレクションのパフォーマンス レベルを変更するもう 1 つの方法は、マイクロソフトの SDK を使用する方法です。ここでは、マイクロソフトの [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) を使用してコレクションのパフォーマンス レベルを変更する手順を説明しますが、この手順は他の [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) の場合にもほぼ当てはまります。.NET SDK を初めてご使用になる場合は、[チュートリアル入門](documentdb-get-started.md)を参照してください。

次に、プランの種類を変更するコード スニペットを示します。

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
	                          .Where(r => r.ResourceLink == "collection selfLink")    
	                          .AsEnumerable()
	                          .SingleOrDefault();
	                          
	//Change the user mode to All
	offer.OfferType = "S3";
	                    
	//Now persist these changes to the database by replacing the original resource
	Offer updated = await client.ReplaceOfferAsync(offer);

他の例やマイクロソフトのプランの各種メソッドの詳細については、[MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) を参照してください。

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##次のステップ

Azure DocumentDB の価格設定とデータ管理の詳細については、以下のリソースを参照してください。
 
- [DocumentDB の価格](http://azure.microsoft.com/pricing/details/documentdb/)
- [DocumentDB の容量の管理](documentdb-manage.md) 
- [DocumentDB のデータのモデル化](documentdb-modeling-data.md)
- [DocumentDB でのデータのパーティション分割](documentdb-partition-data.md)

DocumentDB の詳細については、Azure DocumentDB に関する[ドキュメント](http://azure.microsoft.com/documentation/services/documentdb/)を参照してください。

[1]: ./media/documentdb-performance-levels/img1.png

<!---HONumber=August15_HO7-->