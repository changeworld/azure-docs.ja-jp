<properties
	pageTitle="DocumentDB アカウント制限の引き上げ要求 | Microsoft Azure"
	description="使用できるコレクション数、ストアド プロシージャ数、クエリ句数など、DocumentDB 制限の調整を要求する方法について説明します。"
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
	ms.date="01/28/2016"
	ms.author="anhoh"/>

# DocumentDB アカウント制限の引き上げを要求する

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) には既定で、複数の制限とクォータの適用が設定されています。一部のクォータは、Azure サポートに問い合わせて調整できます。この記事では、アカウント制限の引き上げを要求する方法について説明します。

この記事を読むと、次の質問に回答できるようになります。

-	Azure サポートに問い合わせて調整できるのは、どの DocumentDB アカウント クォータか。
-	DocumentDB アカウント クォータの調整を要求するにはどうすればよいか。

##<a id="AdjustableQuotas"></a> 調整可能な DocumentDB アカウント クォータ

Azure サポートに問い合わせて調整できる DocumentDB クォータは、次の表のとおりです。

|エンティティ |クォータ (Standard プラン)|
|-------|--------|
|データベース アカウント |5
|コレクションあたりのストアド プロシージャ、トリガー、UDF の数 |それぞれ 25 個
|データベース アカウントあたりの最大コレクション |100
|データベースあたりの最大ドキュメント ストレージ (100 コレクション単位) |1 TB (テラバイト)
|クエリあたりの最大 UDF 数 |2
|クエリあたりの最大 JOIN 数 |5
|クエリあたりの AND 句の最大数 |20
|クエリあたりの OR 句の最大数 |20
|IN 式あたりの値の最大数 |200
|ST\_WITHIN クエリ内のポリゴン引数の最大ポイント数 |16
|1 分あたりのコレクション作成の最大数 |5
|1 分あたりのスケール操作の最大数 |5

##<a id="RequestQuotaIncrease"></a> クォータ調整の要求
クォータ調整を要求するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)の **[参照]**、**[ヘルプとサポート]** を順にクリックします。

	![ヘルプとサポート起動時のスクリーンショット](media/documentdb-increase-limits/helpsupport.png)

2. **[ヘルプとサポート]** ブレードで **[サポートを受ける]** をクリックします。

	![サポート チケット作成のスクリーンショット](media/documentdb-increase-limits/getsupport.png)

3. **[新しいサポート要求]** ブレードで、**[基本]** をクリックします。次に、**[問題の種類]** を **[クォータ]** に、**[サブスクリプション]** を自分のDocumentDB アカウントをホストするサブスクリプションに、**[サービス]** を **[DocumentDB]** に、**[サポート プラン]** を **[クォータのサポートを含む]** に設定します。最後に、**[次へ]** をクリックします。

	![サポート チケットのリクエストの種類のスクリーンショット](media/documentdb-increase-limits/supportrequest1.png)

4. **[問題]** ブレードで、重大度を選択します。**[問題の種類]** を **[DocumentDB]** に設定し、クォータの増大に関する情報を **[詳細]** に入力します。**[次へ]** をクリックします。

	![サポート チケットのサブスクリプション選択のスクリーンショット](media/documentdb-increase-limits/supportrequest2.png)

5. 最後に、連絡先情報を **[連絡先情報]** ブレードに入力します。

	![サポート チケットのリソース選択のスクリーンショット](media/documentdb-increase-limits/supportrequest3.png)

サポート チケットが作成されると、サポート要求番号が電子メールで届きます。サポート要求は、**[ヘルプとサポート]** ブレードの **[サポート要求]** をクリックして表示することもできます。

![サポート リクエスト ブレードのスクリーンショット](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a>次のステップ
- DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。

<!---HONumber=AcomDC_0128_2016-->