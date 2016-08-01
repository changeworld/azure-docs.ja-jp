<properties 
	pageTitle="DocumentDB S1 アカウントのパワーアップ | Microsoft Azure" 
	description="Azure ポータルでいくつかの簡単な変更を行って、DocumentDB S1 アカウントのスループットを向上させ、活用します。" 
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
	ms.date="06/27/2016" 
	ms.author="mimig"/>

# DocumentDB アカウントのパワーアップ

次の手順に従って、Azure DocumentDB S1 アカウントのスループットを向上させ、活用します。追加コストをほとんどかけずに、既存の S1 アカウントのスループットを 250 [RU/秒](documentdb-request-units.md)から 400 RU/秒以上に向上させることができます。

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## Azure ポータルでユーザー定義のパフォーマンスを変更する

1. ブラウザーで、[**Azure ポータル**](https://portal.azure.com)に移動します。
2. **[参照]**、**[DocumentDB アカウント]** の順にクリックし、変更する DocumentDB アカウントを選択します。
3. **[データベース]** レンズで変更するデータベースを選択し、**[データベース]** ブレードで価格レベルが S1 のコレクションを選択します。

      ![Screen shot of the Database blade with an S1 collection](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. **[コレクション]** ブレードで、上部のバーにある **[設定]** をクリックします。
5. **[設定]** ブレードで **[価格レベル]** をクリックすると、各プランの月額料金の見積もりが表示されます。**[Choose your pricing tier (価格レベルの選択)]** ブレードで **[Standard]** をクリックし、**[選択]** をクリックして変更を保存します。

      ![Screen shot of the DocumentDB Settings and Choose your pricing tier blades](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. **[設定]** ブレードに戻ると、**[価格レベル]** が **[Standard]** に変更され、**[スループット (RU/秒)]** ボックスには既定値の 400 が表示されています。**[OK]** をクリックして変更を保存します。

    > [AZURE.NOTE] スループットは、400 ～ 10,000 [要求ユニット](../articles/documentdb/documentdb-request-units.md)/秒 (RU/秒) の間で設定できます。ページの下部にある **[価格の概要]** が自動的に更新され、月額料金の見積もりが表示されます。
    
	![Screen shot of the Settings blade showing where to change the throughput value](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. **[データベース]** ブレードに戻ると、コレクションのスループットが向上していることを確認できます。

	![Screen shot of the Database blade with modified collection](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

ユーザー定義のスループットと事前定義されたスループットに関連した変更の詳細については、ブログ記事「[DocumentDB: Everything you need to know about using the new pricing options (DocumentDB: 新しい価格オプションの使用に関して知っておく必要があるすべてのこと)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)」を参照してください。

## 次のステップ

より優れた (10,000 RU/秒を上回る) スループットまたはさらに大きな (10 GB を超える) 記憶域が必要であると判断した場合は、パーティション分割コレクションを作成できます。パーティション分割コレクションを作成するには、[コレクションの作成](documentdb-create-collection.md)に関するページを参照してください。

<!---HONumber=AcomDC_0720_2016-->