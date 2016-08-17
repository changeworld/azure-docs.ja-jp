<properties 
	pageTitle="DocumentDB のパフォーマンス レベル | Microsoft Azure" 
	description="DocumentDB のパフォーマンス レベルを使用して、コレクションごとにスループットを予約できるようにする方法について説明します。" 
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
	ms.topic="article" 
	ms.date="07/27/2016" 
	ms.author="mimig"/>

# DocumentDB のパフォーマンス レベル

この記事では、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) のパフォーマンス レベルの概要について説明します。

この記事を読むと、次の質問に回答できるようになります。

-	パフォーマンス レベルとは何か。
-	スループットはデータベース アカウントに対してどのように予約されるか。
-	パフォーマンス レベルをどのように使用するか。
-	パフォーマンス レベルはどのように課金されるか。

## パフォーマンス レベルとは

Standard アカウントで作成された DocumentDB コレクションはそれぞれ、関連するパフォーマンス レベルを使用してプロビジョニングされます。データベース内の各コレクションのパフォーマンス レベルは異なっていてもよいので、頻繁にアクセスするコレクションにはより多いスループット、あまりアクセスしないコレクションにはより低いスループットを指定することができます。DocumentDB では、ユーザー定義のパフォーマンス レベルと事前定義のパフォーマンス レベルの両方をサポートしています。

各パフォーマンス レベルには、関連する[要求ユニット (RU)](documentdb-request-units.md) レートの制限があります。これは、パフォーマンス レベルに基づいてコレクションに予約されるスループットで、そのコレクションが独占的に使用することができます。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
        	<td valign="top"><p></p></td>
            <td valign="top"><p>詳細</p></td>
            <td valign="top"><p>スループットの制限</p></td>
            <td valign="top"><p>ストレージの制限</p></td>
            <td valign="top"><p>バージョン</p></td>
 			<td valign="top"><p>API</p></td>            
        </tr>
        <tr>
        	<td valign="top"><p>ユーザー定義のパフォーマンス</p></td>
            <td valign="top"><p>ストレージの使用量 (GB 単位) に応じて課金。</p><p>100 RU/秒のスループット</p></td>
            <td valign="top"><p>無制限。既定では 400 ～ 250,000 RU/秒 (要求すればそれ以上)</p></td>
            <td valign="top"><p>無制限。既定では 250 GB (要求すればそれ以上) </p></td>
            <td valign="top"><p>V2</p></td>
 			<td valign="top"><p>API 2015-12-16 およびそれ以降</p></td>  
        </tr>
        <tr>
        	<td valign="top"><p>事前定義のパフォーマンス</p></td>
            <td valign="top"><p>10 GB の予約済みストレージ。</p><p>S1 = 250 RU/秒、S2 = 1000 RU/秒、S3 = 2500 RU/秒</p></td>
            <td valign="top"><p>2500 RU/秒</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
 			<td valign="top"><p>任意</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB では、クエリ、ユーザー定義関数 (UDF) を使用したクエリ、ストアド プロシージャ、トリガーなど、さまざまなデータベース操作を実行できます。これらの各操作に関連付けられる処理コストは、CPU、IO、操作の完了に必要なメモリによって異なります。ハードウェア リソースの検討や管理をする代わりに、要求単位をさまざまなデータベース操作の実行やアプリケーション要求の処理に必要なリソースの 1 つのメジャーとして考えることができます。

コレクションの作成は、[Microsoft Azure ポータル](https://portal.azure.com)、[REST API](https://msdn.microsoft.com/library/azure/mt489078.aspx)、または [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して行います。DocumentDB API を使用して、コレクションのパフォーマンス レベルを指定することができます。

> [AZURE.NOTE] コレクションのパフォーマンス レベルの調整は、API や [Microsoft Azure ポータル](https://portal.azure.com/)を使用して行います。パフォーマンス レベルは、3 分以内に変更できるはずです。

## コレクションのパフォーマンス レベルの設定
コレクションが作成されると、指定されたパフォーマンス レベルに基づいたすべての RU の割り当てがコレクションに対して予約されます。

ユーザー定義のパフォーマンス レベルと事前定義のパフォーマンス レベルの両方で、DocumentDB はスループットの予約に基づいて動作することに注意してください。コレクションを作成するとアプリケーションが予約され、予約済みのスループットに対して課金されます。スループットの使用状況は考慮されませんユーザー定義のパフォーマンス レベルの場合はストレージの使用量に基づいて課金されますが、事前定義のパフォーマンス レベルの場合はコレクションの作成時に 10 GB のストレージが予約されます。

コレクションを作成した後、DocumentDB SDK や Azure クラシック ポータルを使用してパフォーマンス レベルを変更することができます。

> [AZURE.IMPORTANT] DocumentDB Standard のコレクションは 1 時間ごとに課金され、ユーザーが作成した各コレクションは最小 1 時間の使用に対して課金されます。

1 時間の間にパフォーマンス レベルを調整した場合、その時間内に設定された最高のパフォーマンス レベルで課金されます。たとえば、午前 8 時 53 分にコレクションのパフォーマンス レベルを上げた場合は、午前 8 時からその新しいレベルで課金されます。同様に、午前 8 時 53 分にパフォーマンス レベルを下げた場合は、新しいレートが午前 9 時から適用されます。

設定されたパフォーマンス レベルに基づいて、各コレクションに要求単位が予約されます。要求単位の消費は 1 秒ごとに評価されます。コレクションに対してプロビジョニングされた要求単位レート (またはパフォーマンス レベル) をアプリケーションが超過した場合、そのレートがコレクションに対して予約されたレベルを下回るまで、そのアプリケーションは調整されます。より高レベルのスループットがアプリケーションに必要な場合は、各コレクションのパフォーマンス レベルを上げることができます。

> [AZURE.NOTE] 1 つ以上のコレクションでアプリケーションがパフォーマンス レベルを超えると、コレクションごとに要求が調整されます。つまり、アプリケーションの要求が成功する場合と調整される場合があります。スロットルが行われた場合は、要求トラフィックの急増に対処できるように、数回だけ再試行を行うことをお勧めします。

## パフォーマンス レベルの操作
DocumentDB のコレクションを使用すると、クエリ パターンとアプリケーションのパフォーマンス ニーズの両方に応じてデータをグループ化することができます。DocumentDB は自動インデックス作成とクエリをサポートしているため、同一コレクション内に異種ドキュメントを配置することもよくあります。データを別々のコレクションに分けるかどうかを決めるときの主な考慮事項は次のとおりです。

- クエリ - コレクションは、クエリ実行のスコープです。一連のドキュメント間でクエリを実行する必要がある場合、最も効率的な読み取りのパターンは、単一コレクションにドキュメントを配置することで実現できます。
- トランザクション - すべてのトランザクションは単一のコレクション内にスコープされます。単一のストアド プロシージャまたはトリガーでの更新が必要なドキュメントがある場合は、それらを同じコレクション内に格納する必要があります。具体的には、コレクション内のパーティション キーがトランザクションの境界となります。詳細については、[DocumentDB でのパーティション分割に関するページ](documentdb-partition-data.md)をご覧ください。
- パフォーマンスの分離 - コレクションにはパフォーマンス レベルが関連付けられています。これにより、各コレクションは予約済みの RU を使用してパフォーマンスを予測できます。アクセス頻度に応じて、パフォーマンス レベルの異なるさまざまなコレクションにデータを割り当てることができます。

> [AZURE.IMPORTANT] アプリケーションで作成されたコレクションの数に基づいて、標準料金の全額が課金されることを理解することが重要です。

保存容量やスループットの大きさが求められている場合を除いて、アプリケーションで使用するコレクションは少数にとどめておくことをお勧めします。新しいコレクションの作成に対するアプリケーションのパターンを十分に理解しておいてください。コレクションの作成を、アプリケーション外部で処理する管理アクションにしておくこともできます。同様に、コレクションのパフォーマンス レベルを調整すると、コレクションの時間あたりの課金レートが変更されます。アプリケーションでこれらを動的に調整する場合は、コレクションのパフォーマンス レベルを監視する必要があります。

## <a id="changing-performance-levels-using-the-azure-portal"></a>S1、S2、S3 からユーザー定義のパフォーマンスに変更する

Azure ポータルで次の手順に従って、使用するパフォーマンス レベルを事前定義のものからユーザー定義のものに変更します。ユーザー定義のスループット レベルを使用すると、ニーズに合わせてスループットを調整できます。S1 アカウントを引き続き使用する場合は、数回クリックするだけで既定のスループットを 250 RU/秒から 400 RU/秒に引き上げることができます。

ユーザー定義のスループットと事前定義されたスループットに関連した価格変更の詳細については、ブログ記事「[DocumentDB: Everything you need to know about using the new pricing options (DocumentDB: 新しい価格オプションの使用に関して知っておく必要があるすべてのこと)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)」をご覧ください。

> [AZURE.VIDEO changedocumentdbcollectionperformance]

1. ブラウザーで、[**Azure ポータル**](https://portal.azure.com)に移動します。
2. **[参照]**、**[DocumentDB アカウント]** の順にクリックし、変更する DocumentDB アカウントを選択します。
3. **[データベース]** レンズで変更するデータベースを選択し、**[データベース]** ブレードで変更するコレクションを選択します。事前定義のスループットを使用するアカウントの価格レベルには、S1、S2、S3 があります。

      ![Screen shot of the Database blade with an S1 collection](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)

4. **[コレクション]** ブレードで、上部のバーにある **[設定]** をクリックします。
5. **[設定]** ブレードで **[価格レベル]** をクリックすると、**[価格レベルの選択]** ブレードに各プランの月額料金の見積もりが表示されます。ユーザー定義のスループットに変更するには、**[Standard]**、**[選択]** の順にクリックして、変更を保存します。

      ![Screen shot of the DocumentDB Settings and Choose your pricing tier blades](./media/documentdb-performance-levels/documentdb-change-performance.png)

6. **[設定]** ブレードに戻ると、**[価格レベル]** が **[Standard]** に変更され、**[スループット (RU/秒)]** ボックスには既定値の 400 が表示されています。スループットは、400 ～ 10,000 [要求ユニット](documentdb-request-units.md)/秒 (RU/秒) の間で設定します。ページの下部にある **[価格の概要]** が自動的に更新され、月額料金の見積もりが表示されます。**[OK]** をクリックして変更を保存します。
    
	![Screen shot of the Settings blade showing where to change the throughput value](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

7. **[データベース]** ブレードに戻ると、コレクションの新しいスループットを確認できます。

	![Screen shot of the Database blade with modified collection](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

より優れた (10,000 RU/秒を上回る) スループットまたはさらに大きな (10 GB を超える) 記憶域が必要であると判断した場合は、パーティション分割コレクションを作成できます。パーティション分割コレクションを作成するには、[コレクションの作成](documentdb-create-collection.md)に関するページをご覧ください。

>[AZURE.NOTE] コレクションのパフォーマンス レベルの変更には、最大で 2 分かかる場合があります。

## .NET SDK を使用するパフォーマンス レベルの変更

コレクションのパフォーマンス レベルを変更するもう 1 つの方法は、マイクロソフトの SDK を使用する方法です。ここでは、マイクロソフトの [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) を使用してコレクションのパフォーマンス レベルを変更する手順を説明しますが、この手順は他の [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) の場合にもほぼ当てはまります。.NET SDK を初めてご使用になる場合は、[チュートリアル入門](documentdb-get-started.md)を参照してください。

プランのスループットを 1 秒あたり 50,000 要求ユニットに変更するためのコード スニペットを次に示します。

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
		              .Where(r => r.ResourceLink == collection.SelfLink)    
		              .AsEnumerable()
		              .SingleOrDefault();
	                          
	// Set the throughput to 5000 request units per second
	offer = new OfferV2(offer, 5000);
	                    
	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);

	// Set the throughput to S2
	offer = new Offer(offer);
	offer.OfferType = "S2";
	                    
	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);



> [AZURE.NOTE] 1 秒あたり 10,000 要求ユニット未満でプロビジョニングされたコレクションは、ユーザー定義のスループットと事前定義のスループット (S1、S2、S3) のどのプランでも自由に移行できます。1 秒あたり 10,000 要求ユニット以上でプロビジョニングされたコレクションは、事前定義のスループット レベルに変換することはできません。

他の例やマイクロソフトのプランの各種メソッドの詳細については、[MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) を参照してください。

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a id="change-throughput"></a>コレクションのスループットの変更

ユーザー定義のパフォーマンスを既に使用している場合は、次の手順に従うことでコレクションのスループットを変更できます。パフォーマンス レベル S1、S2、S3 (事前定義のパフォーマンス) からユーザー定義のパフォーマンスに変更する必要がある場合は、「[S1、S2、S3 からユーザー定義のパフォーマンスに変更する](#changing-performance-levels-using-the-azure-portal)」をご覧ください。

1. ブラウザーで、[**Azure ポータル**](https://portal.azure.com)に移動します。
2. **[参照]**、**[DocumentDB アカウント]** の順にクリックし、変更する DocumentDB アカウントを選択します。
3. **DocumentDB アカウント** ブレードの **[データベース]** レンズで変更するデータベースを選択し、**[データベース]** ブレードで変更するコレクションを選択します。
4. **[コレクション]** ブレードで、上部のバーにある **[設定]** をクリックします。
5. **[設定]** ブレードで、**[スループット (RU/秒)]** ボックスの値を増やし、**[OK]** をクリックして変更を保存します。ブレードの下部にある **[料金の概要]** が更新されて、単一のリージョンにおけるこのコレクションの新しい月額料金の見積もりが表示されます。

    ![[スループット] ボックスと [価格の概要] が強調表示されている [設定] ブレードのスクリーンショット](./media/documentdb-performance-levels/documentdb-change-throughput.png)
 
スループットを増やす量を検討中の場合は、「[スループットのニーズの推定](documentdb-request-units.md#estimating-throughput-needs)」と[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)に関するページをご覧ください。

## 次のステップ

Azure DocumentDB の価格設定とデータ管理の詳細については、以下のリソースを参照してください。
 
- [DocumentDB の料金](https://azure.microsoft.com/pricing/details/documentdb/)
- [DocumentDB の容量の管理](documentdb-manage.md)
- [DocumentDB のデータのモデル化](documentdb-modeling-data.md)
- [DocumentDB でのデータのパーティション分割](documentdb-partition-data.md)
- [要求ユニット](http://go.microsoft.com/fwlink/?LinkId=735027)

DocumentDB の詳細については、Azure DocumentDB に関する[ドキュメント](https://azure.microsoft.com/documentation/services/documentdb/)を参照してください。

DocumentDB に関するスケールとパフォーマンスのテストを始めるには、「[Azure DocumentDB のパフォーマンスとスケールのテスト](documentdb-performance-testing.md)」をご覧ください。

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0803_2016-->